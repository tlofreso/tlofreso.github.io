---
draft: true
date: 2024-01-27
authors:
  - me
categories:
  - ChatGPT
---

# Speech to Text using Whisper

This year, I've had fun finding ways to use OpenAI tooling in everyday life. For most folks, this means interacting with ChatGPT through the app, or in a web browser at [https://chat.openai.com](https://chat.openai.com){target=blank}. For me, it's been learning how to effectively use the tools programmatically. One of those tools, is  [Whisper](https://openai.com/research/whisper){target=blank}. <!-- more --> Whisper will take an audio file (up to 25MB) and transcribe the audio into text. Contained here, is how I use Whisper, with other tooling, to convert voice memos I've recorded in iOS to text, then subsequently summarize the text into meeting minutes.

## Tech Stack
For this workflow, I use Dropbox as short-term storage, GitHub actions to run all the tasks, and Open AI's Whisper / GPT-4 models to do the actual work. The sequence of events looks something like this:  

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
GH Action content here.

## Enhancing Things
I quickly realized that support for larger audio files was going to be important for my use case. I also wanted to try GPT-4's hand at generating meeting notes for the transcripts.

### Adding Support for Larger Files
By default, Whisper supports files up to 25MB in size. I've found that 40 minutes is a good cutoff to stay under this limit when using the voice memos app in iOS. OpenAI has some guidance for [Longer inputs](https://platform.openai.com/docs/guides/speech-to-text/longer-inputs){target=blank}. They recommend [PyDub](https://github.com/jiaaro/pydub){target=blank} to chunk the audio.(1) This also recommend [Prompting](https://platform.openai.com/docs/guides/speech-to-text/prompting){target=blank} Whisper:
{ .annotate }

1. ffmpeg is a dependency for PyDub

> To preserve the context of a file that was split into segments, you can prompt the model with the transcript of the preceding segment.


Here's what I added for larger file support

```python
from pydub import AudioSegment
from pydub.utils import make_chunks

def transcribe_audio(file, prompt=None):
    print("Starting transcribe...")
    with open(file, "rb") as file:
        transcript = openai_client.audio.transcriptions.create(model = "whisper-1", file=file, prompt=prompt)
    
    return transcript.text

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

    audio_files = get_filenames(AUDIO_PATH) # Get filenames of all audio files in Dropbox
    download_files(AUDIO_PATH, audio_files) # Download all audio files from Dropbox

    for file in audio_files:

        chunks = chunk_audio(40, file)
        transcripts = ['']

        for chunk in chunks:
            transcript = transcribe_audio(file=chunk, prompt=transcripts[-1])
            transcripts.append(transcript)
```




## Enhancing
### Add support for larger files
 - generally, .m4a files are 500KB / Min. So breaking files into 40 minute chunks will work
### Add prompting for meeting minutes

1. ffmpeg is a pre-requisite for PyDub