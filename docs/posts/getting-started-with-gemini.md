---
draft: false
date: 2024-11-30
authors:
  - me
categories:
  - GenAI
---

# Getting Started with Google AI Studio
Google was a bit later than expected with widely available GenAI. They've rapidly gained momentum, and have several compelling models that lend themselves to interesting use cases. Here's a recent example <!-- more -->of what I mean: [Video Scraping](https://simonwillison.net/2024/Oct/17/video-scraping/).  

Here are my notes for getting started with the Google AI Studio. 

!!! note "Pro Tip - Create a New Google Account"
    Google leverages Drive for prompt library, file storage, and chat history. Anthropic, and OpenAI will also authticate to a google drive for access to files. It's helpful to have a google account _just_ for AI things. Create one [here](https://accounts.google.com/signup).


## Setup
Developers should use [Google AI Studio](https://aistudio.google.com/). This is analogous to the OpenAI Playground. There's a [Python SDK](https://pypi.org/project/google-generativeai/) ([GH](https://github.com/google-gemini/generative-ai-python)) available that may be installed with:

```
pip install -q -U google-generativeai
```

When you generate an API key, you'll be prompted to add it to a project. There's no concept of a _default_ project like OpenAI / Anthropic. As far as I can tell, what Google means by "project" here is a [Google Cloud Project](https://cloud.google.com/resource-manager/docs/creating-managing-projects). Similar to a Resource Group in Azure. The Google AI Studio will automatically spin a project up for you if one does not exist, and subsequently enable the Gemini API within that project. You can view this by visiting the [Google Cloud Console](https://console.cloud.google.com/). For me, it created a project called "Gemini API" in an Organization named "No organization". It also enabled the Gemini API within the project.

**Example Script**  

The [Gemini API Quickstart](https://ai.google.dev/gemini-api/docs/quickstart?lang=python) will walk you through creating the following script:

```python
import google.generativeai as genai
from dotenv import load_dotenv
from rich import print
import os

load_dotenv()

API_KEY = os.getenv('API_KEY')

genai.configure(api_key=os.environ["API_KEY"])

model = genai.GenerativeModel("gemini-1.5-flash")
response = model.generate_content("Write a story about a magic backpack.")

print(response.text)
```

## People to Follow
The level of exposure the community has to the people building this tooling is remarkable. Here are several I follow:

 - [Raiza Martin](https://x.com/raiza_abubakar) - Product Lead for NotebookLM
 - [Josh Woodward](https://x.com/joshtwoodward) - VP at Google Labs
 - [Logan Kilpatrick](https://x.com/OfficialLoganK) - Leed Product for Google AI Studio

They are iterating incredibly fast, and are very open to community feedback. Here's a great [example](https://x.com/levelsio/status/1831836883829322156) of a discussion between Logan and Pieter.

## Other Notes
Gemini's [free tier](https://ai.google.dev/pricing) for developers is the most generous of any AI provider. This opens the door to some interesting use cases. I'm using the platform quite a bit... and time permitting, hope to write about the gems I find along the way.