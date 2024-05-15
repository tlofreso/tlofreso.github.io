---
draft: false
date: 2024-01-30
authors:
  - me
categories:
  - GenAI
---

# Speech to Text using Whisper

This year, I've had fun finding ways to use OpenAI tooling in everyday life. For most folks, this means interacting with ChatGPT through the app, or in a web browser at [https://chat.openai.com](https://chat.openai.com){target=blank}. For me, it's been learning how to effectively use the tools programmatically. One of those tools, is  [Whisper](https://openai.com/research/whisper){target=blank}. <!-- more --> Whisper will take an audio file (up to 25MB) and transcribe the audio into text. Contained here, is how I use Whisper, with other tooling, to convert voice memos I've recorded in iOS to text, then subsequently summarize the text into meeting minutes.

## Tech Stack
For this workflow, I use Dropbox as short-term storage, GitHub actions to run all the tasks, and Open AI's Whisper / GPT-4 models to do the actual work. The sequence of events looks something like this _(click for larger image)_:  

[![Sequence Diagram Dark]][Sequence Diagram Dark]
[Sequence Diagram Dark]: ../assets/posts/speech-to-text/diagram-dark.svg#only-dark

[![Sequence Diagram]][Sequence Diagram]
[Sequence Diagram]: ../assets/posts/speech-to-text/diagram-light.svg#only-light

??? note "Mermaid diagram code"

    ```
    sequenceDiagram
        participant VoiceMemos as Record a Voice Memo
        participant Dropbox as Dropbox
        participant GitHubAction as GitHub Action
        participant Whisper as Whisper
        participant GPT4 as GPT-4
        VoiceMemos->>Dropbox: Share audio to Dropbox
        Dropbox->>GitHubAction: Download audio
        activate GitHubAction
        GitHubAction->>Whisper: Transcribe audio using Whisper
        activate Whisper
        Whisper-->>GitHubAction: Return transcript
        deactivate Whisper
        GitHubAction->>GPT4: Create meeting minutes from transcript
        activate GPT4
        GPT4-->>GitHubAction: Return meeting minutes
        deactivate GPT4
        GitHubAction-->>Dropbox: Upload meeting minutes
        deactivate GitHubAction
    ```

### Dropbox App
For long-lived Dropbox API interactions, you'll need a Dropbox App. A bit much for my use case... I wish there were an easier way to interact with Dropbox programmatically, but if you want to use the API, you need oauth, and you get oauth by having an app_key/app_secret pair. At a very high level, here are the steps:

1. Create an app using the [App Console](https://www.dropbox.com/developers/apps){target=blank}
2. Retrieve the App key and App secret in your Dropbox app's settings, and use the app key to create an Authorization URL:  
```
https://www.dropbox.com/oauth2/authorize?client_id=<APP_KEY>&token_access_type=offline&response_type=code
```

3. Complete the auth-z flow by browsing to the Authorization URL. You'll receive an `authorization_code` at the end.
4. Make a `POST` call to `https://api.dropboxapi.com/oauth2/token` with the appropriate parameters: `APP_KEY`, `APP_SECRET`, `AUTHORIZATION_CODE`
```bash
BASIC_AUTH=$(echo '<APP_KEY>:<APP_SECRET>' | base64) # Encode username:password
curl --location --request POST 'https://api.dropboxapi.com/oauth2/token' \
     --header 'Authorization: Basic $BASIC_AUTH' \
     --header 'Content-Type: application/x-www-form-urlencoded' \
     --data-urlencode 'code=<AUTHORIZATION_CODE>' \
     --data-urlencode 'grant_type=authorization_code'
```
The response for this call will include a `refresh_token` ...
```json
{
  "access_token": "sl.***",
  "token_type": "bearer",
  "expires_in": 14400,
  "refresh_token": "***",
  "scope": "<SCOPES>",
  "uid": "***",
  "account_id": "***"
}
```
Use the refresh token in your code. The Dropbox [Python SDK](https://github.com/dropbox/dropbox-sdk-python){target=blank} has support for this:
```python
import dropbox

dropbox_client = dropbox.Dropbox(
  app_key=config['dropbox_app_key'],
  app_secret=config['dropbox_app_secret'],
  oauth2_refresh_token=config['dropbox_refresh_token']
)
```

Here are some links I found helpful while creating my app:

 - [Handy oauth post](https://stackoverflow.com/questions/70641660/how-do-you-get-and-use-a-refresh-token-for-the-dropbox-api-python-3-x){target=blank}
  - [also useful](https://www.dropboxforum.com/t5/Dropbox-API-Support-Feedback/Oauth2-refresh-token-question-what-happens-when-the-refresh/td-p/486241){target=blank}
 - [getting a token](https://dropbox.tech/developers/generate-an-access-token-for-your-own-account){target=blank}

### OpenAI Whisper
OpenAI's documentation is pretty great.

 - Here are the docs for the endpoint I'm using [Create transcription](https://platform.openai.com/docs/api-reference/audio/createTranscription){target=blank}.
  - Here's a more in-depth example of how to use the endpoint: [Speech to text](https://platform.openai.com/docs/guides/speech-to-text){target=blank}

This is my code

```python
from openai import OpenAI

openai_client = OpenAI()

def transcribe_audio(file, prompt=None):
    print("Starting transcribe...")
    with open(file, "rb") as file:
        transcript = openai_client.audio.transcriptions.create(model = "whisper-1", file=file, prompt=prompt)
    
    return transcript.text

```

### GitHub Actions
I like GitHub quite a bit. Public repos get access to some great tooling like [Dependabot](https://github.blog/2023-01-12-a-smarter-quieter-dependabot/){target=blank}, and up to 2,000 minutes / month for GitHub Action Runners. I have a goal this year to use GitHub Actions more.

The action below runs on demand (with `worflow_dispatch`) or every day at 5PM _`schedule` uses [UTC time](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#schedule){target=blank}_. The [`setup-python`](https://github.com/marketplace/actions/setup-python){target=blank} action is pretty slick as it takes advantage of GitHub's [Hosted tool cache](https://github.com/actions/setup-python/blob/main/docs/advanced-usage.md#hosted-tool-cache){target=blank} feature. This improves the total runtime of your jobs by caching the full dev environment instead of downloading/installing for each run.

```yaml
name: Speech to Text Runner
run-name: Transcribe audio file to text
on:
  schedule:
      - cron: '0 22 * * *'
  workflow_dispatch:
env:
  DROPBOX_APP_KEY: ${{ secrets.DROPBOX_APP_KEY }}
  DROPBOX_APP_SECRET: ${{ secrets.DROPBOX_APP_SECRET }}
  DROPBOX_REFRESH_TOKEN: ${{ secrets.DROPBOX_REFRESH_TOKEN }}
  OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
jobs:
  Audio-Transcriber:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      - name: Setup Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.11'
          cache: 'pip'
      - run: pip install -r requirements.txt
      - name: Run Main
        run: python main.py
```

## Enhancing
I quickly realized that support for larger audio files was going to be important for my use case. I also wanted to try GPT-4's hand at generating meeting notes for the transcripts.

### Adding Support for Larger Files
By default, Whisper supports files up to 25MB in size. I've found that 40 minutes is a good cutoff to stay under this limit when using the voice memos app in iOS. OpenAI has some guidance for [Longer inputs](https://platform.openai.com/docs/guides/speech-to-text/longer-inputs){target=blank}. They recommend [PyDub](https://github.com/jiaaro/pydub){target=blank} to chunk the audio.(1) They also recommend [Prompting](https://platform.openai.com/docs/guides/speech-to-text/prompting){target=blank} Whisper:
{ .annotate }

1. ffmpeg is a dependency for PyDub

> To preserve the context of a file that was split into segments, you can prompt the model with the transcript of the preceding segment.


Here's what I added for larger file support

```python
from pydub import AudioSegment
from pydub.utils import make_chunks

# Takes a chunk duration in minutes, and a file as arguments.
# Saves chunks to the working directory, and returns a list of filenames.
def chunk_audio(chunk_mins, file):
    print("Chunking audio...")

    chunks_out = []

    audio = AudioSegment.from_file(file)
    chunk_size = chunk_mins * 60 * 1000
    chunks = make_chunks(audio, chunk_size)
    for i, chunk in enumerate(chunks):
        audio_out = f'{i}.mp3'
        chunk.export(audio_out, format='mp3')
        chunks_out.append(audio_out)
    
    print(f"Chunked into {len(chunks_out)} files...")
    return(chunks_out)

if __name__ == "__main__":

    for file in audio_files:

        chunks = chunk_audio(40, file)
        transcripts = ['']

        # Transcribe chunks with preceding chunk transcript as a prompt
        for chunk in chunks:
            transcript = transcribe_audio(file=chunk, prompt=transcripts[-1])
            transcripts.append(transcript)
```

I also had to update my runner to install ffmpeg with [this](https://github.com/marketplace/actions/setup-ffmpeg){target=blank}. Now the steps are:

```yaml
steps:
  - name: Checkout
    uses: actions/checkout@v4
  - name: Setup ffmpeg
    uses: FedericoCarboni/setup-ffmpeg@v3
  - name: Setup Python
    uses: actions/setup-python@v5
```


### Use GPT-4 for meeting minutes
Now that we have the full text for audio files of any size, we can use GPT-4 to summarize the content. Doing so was remarkably trivial. I followed [this tutorial](https://platform.openai.com/docs/tutorials/meeting-minutes){target=blank} [almost verbatim](https://github.com/tlofreso/speech-to-text/blob/main/meeting_notes.py){target=blank}, and added three lines of Python to `main.py`

```python
from meeting_notes import meeting_minutes
with open(out_txt, "r") as full_transcript:
     notes = meeting_minutes(full_transcript.read())
```

## Cost
My use of Dropbox and GitHub fall well within the free tiers. The spend in ChatGPT is quite low for what you get. Especially when you consider the sheer compute it takes to do the job: Transcribe, then Summarize. Here's where my January bill stands:

 - 26,600 transcribed seconds: $2.90
 - 274,600 tokens: $3.00

Something else you'll quickly hit when using the APIs for anything other than trivial tasks, is [Usage limits](https://platform.openai.com/docs/guides/rate-limits/usage-tiers?context=tier-free){target=blank}. Luckily, the ceiling for Tier 2 is quite high, and you can simply purchase $50 in API credits to unlock it.