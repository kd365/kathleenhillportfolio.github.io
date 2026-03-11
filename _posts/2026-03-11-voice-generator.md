---
layout: post
title: Neural Voice Studio — AWS Polly Text-to-Speech App
image: "/posts/voice-generator.png"
tags: [react, flask, aws-polly, docker, python]
---

# Neural Voice Studio — Full-Stack Text-to-Speech with AWS Polly

**Source Code:** [https://github.com/kd365/neural-voice-studio](https://github.com/kd365/neural-voice-studio)

A full-stack web application that converts text into natural-sounding speech using AWS Polly's neural voice engine. Users type or paste text, choose from six neural voices, and instantly generate downloadable MP3 audio — all through a containerized React + Flask architecture.

## Overview

| Layer | Technology |
|-------|-----------|
| **Frontend** | React 19, Vite, Lucide icons |
| **Backend** | Flask 3.0, boto3 |
| **Cloud** | AWS Polly (neural TTS, us-east-1) |
| **Containers** | Docker Compose (frontend + backend) |

## How It Works

1. User enters text (up to 3,000 characters) and selects a voice
2. React frontend sends a POST to the Flask API (`/api/generate`)
3. Flask calls `polly_client.synthesize_speech()` with the neural engine
4. AWS Polly returns an MP3 audio stream
5. Backend base64-encodes the audio and returns it as JSON
6. Frontend decodes the blob, creates an Object URL, and streams playback

## Features

- **Six Neural Voices** — Joanna, Matthew, Amy, Brian, Emma, and Justin with natural intonation
- **Audio Player** — Play/pause, progress bar, elapsed/total time display
- **MP3 Download** — Save generated audio files with timestamped filenames
- **Input Validation** — Character counter, empty-text rejection, error messaging
- **Health Endpoint** — `/health` for container orchestration and liveness probes
- **Multi-Stage Docker Build** — Node 22 build stage → Nginx Alpine production image for the frontend

## Architecture

```
┌─────────────────────────┐
│   React Frontend (:3000)│
│   Vite + Lucide Icons   │
│   Audio Blob / Object URL│
└──────────┬──────────────┘
           │ POST /api/generate
           ▼
┌─────────────────────────┐
│   Flask Backend (:5001) │
│   boto3 Polly Client    │
│   Base64 Encoding       │
└──────────┬──────────────┘
           │ synthesize_speech()
           ▼
┌─────────────────────────┐
│   AWS Polly (us-east-1) │
│   Neural TTS Engine     │
│   MP3 Output Stream     │
└─────────────────────────┘
```

Both services are orchestrated with Docker Compose, with container networking handling service discovery.

## Technology Highlights

- **React 19** with functional components and hooks (`useState`, `useEffect`, `useRef`) for audio lifecycle management
- **Object URL cleanup** in `useEffect` return to prevent memory leaks from audio blobs
- **Multi-stage Docker build** compiles the Vite app then serves static files from Nginx Alpine — minimal production image size
- **CORS middleware** on Flask enables cross-origin requests between frontend and backend containers

## Author

**Kathleen Hill**
- Portfolio: [khilldata.com](https://khilldata.com)
- GitHub: [@kd365](https://github.com/kd365)
- LinkedIn: [kathleen-hill322](https://www.linkedin.com/in/kathleen-hill322/)
