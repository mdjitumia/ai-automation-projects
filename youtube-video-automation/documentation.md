# AI YouTube Automation — Documentation

## 1. Overview

This project is a two-workflow AI-to-video pipeline. It starts from a user-provided text idea and automates content generation, visual generation, voiceover generation, video creation, post-production, and YouTube publishing.

## 2. End-to-End Flow

```text
Text Idea
   ↓
AI Ideator
   ↓
Script + Title + Description
   ↓
Script Split into 6-Second Chunks
   ├───────────────┐
   ↓               ↓
Image Prompts    Full Script
   ↓               ↓
AI Images       ElevenLabs
   ↓               ↓
Google Drive    Voiceover Audio
   ↓               ↓
Kling AI          Google Drive
   ↓
Video Generation
   ↓
Kling Callback
   ↓
Status Check
   ↓
FAL / FFmpeg
(Video + Audio)
   ↓
Final Video
   ↓
YouTube Upload
```

## 3. Workflow 1 — Content Generation & Media Creation

The first workflow creates the content and media required for the final video.

### AI Ideation

The AI ideator generates a structured video concept containing a script, title, and description from the user input.

### Script Segmentation

The script is divided into approximately 6-second segments so each segment can be paired with a visual.

### Image Prompt Generation

For every script segment, O3-MINI generates a simple visual prompt suitable for image generation.

### Image Generation

GPT-Image-1-MINI generates an image for each segment. The generated images are stored in Google Drive.

### Voiceover Generation

In parallel, the complete script is sent to the **ElevenLabs API** to generate the voiceover audio, which is stored in Google Drive.

### Video Generation

The generated images are sent to **Kling AI's image-to-video API** to create video footage for the visual segments.

## 4. Workflow 2 — Post-Production & YouTube Publishing

The second workflow handles the asynchronous video-generation result and final publication.

### Kling Callback

A webhook receives the callback after Kling AI finishes the video-generation task.

### Status Validation

The workflow checks the Kling task status and continues when the generation succeeds.

### Retrieve Audio

The generated voiceover file is downloaded from Google Drive.

### Merge Video + Audio

The Kling-generated video and ElevenLabs voiceover are merged through the FAL FFmpeg merge API.

### Final Render

The merged video file is downloaded as the final render.

### YouTube Publishing

The final video is uploaded to YouTube with publishing metadata.

## 5. Key Automation Value

The system removes the need to manually coordinate multiple content-production steps. Two connected workflows orchestrate the process from text input to a published video.

## 6. Tech Stack

| Area | Technology |
|---|---|
| Workflow orchestration | n8n |
| Content generation | Grok-3-MINI |
| Image prompt generation | O3-MINI |
| Image generation | GPT-Image-1-MINI |
| Voice generation | ElevenLabs |
| Video generation | Kling AI |
| File storage | Google Drive |
| Video/audio processing | FAL / FFmpeg |
| Publishing | YouTube |
