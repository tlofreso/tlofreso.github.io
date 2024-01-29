---
draft: true
date: 2024-01-27
authors:
  - me
categories:
  - ChatGPT
---

# Speech to Text using Whisper

This year, I've had fun finding ways to use OpenAI tooling in everyday life. For most folks, this means interacting with ChatGPT through the app, or in a web browser at [https://chat.openai.com](https://chat.openai.com){target=blank}. For me, it's been learning how to effectively use the tools programmatically. One of those tools, is  [Whisper](https://openai.com/research/whisper){target=blank}. Whisper will take an audio file (up to 25MB) and transcribe the audio into text. Contained here, is how I use Whisper, with other tooling, to convert voice memos I've recorded in iOS to text, then subsequently summarize the text into meeting minutes.

## Tech Stack

### Dropbox App
### OpenAI Whisper
## Enhancing
### Add support for larger files
 - generally, .m4a files are 500KB / Min. So breaking files into 40 minute chunks will work
### Add prompting for meeting minutes