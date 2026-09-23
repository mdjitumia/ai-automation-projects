# AI YouTube Automation

An end-to-end **two-workflow AI video production pipeline** that transforms a text idea into a finished YouTube video.

### What It Does

**Idea → Script → Images → Voiceover → Video → Merge → YouTube**

The pipeline uses AI for content generation, visual creation, voice generation, video generation, post-production, and publishing.

### Technologies

**n8n • Grok-3-MINI • O3-MINI • GPT-Image-1-MINI • ElevenLabs • Kling AI • Google Drive • FAL / FFmpeg • YouTube**

### Workflows

**01 — Content Generation & Media Creation**

Generates the script, title and description; splits the script into 6-second chunks; creates image prompts and images; generates the voiceover with ElevenLabs; stores media in Google Drive; and sends images to Kling AI for video generation.

**02 — Post-Production & YouTube Publishing**

Receives the Kling AI callback, checks generation status, retrieves the audio, merges video and audio with FAL/FFmpeg, downloads the final render, and uploads it to YouTube with metadata.

### Repository Structure

```text
youtube-automation/
├── README.md
├── documentation.md
└── workflows/
    ├── 01-content-generation-media-creation.json
    └── 02-post-production-youtube-publishing.json
```
