---
layout: post
title: Neural Voice Studio — AWS Polly Text-to-Speech App
image: "/posts/voice-generator.png"
tags: [react, flask, aws-polly, aws-lambda, api-gateway, docker, python]
---

# Neural Voice Studio — Full-Stack Text-to-Speech with AWS Polly

**Live Demo:** [https://kd365.github.io/neural-voice-studio/](https://kd365.github.io/neural-voice-studio/)

**Source Code:** [https://github.com/kd365/neural-voice-studio](https://github.com/kd365/neural-voice-studio)

A full-stack web application that converts text into natural-sounding speech using AWS Polly's neural voice engine. Users type or paste text, choose from six neural voices, and instantly generate downloadable MP3 audio. The live demo is limited to 3 generations per visitor to manage AWS costs.

## Overview

| Layer | Technology |
|-------|-----------|
| **Frontend** | React 19, Vite, Lucide icons |
| **Backend (prod)** | AWS Lambda, API Gateway |
| **Backend (local)** | Flask 3.0, boto3 |
| **Cloud** | AWS Polly (neural TTS, us-east-1) |
| **Hosting** | GitHub Pages (frontend), AWS Lambda (backend) |
| **CI/CD** | GitHub Actions (build + deploy to Pages) |
| **Containers** | Docker Compose (local development) |

## How It Works

1. User enters text (up to 3,000 characters) and selects a voice
2. React frontend checks the localStorage rate limit (max 3 calls)
3. Frontend sends a POST to API Gateway, which proxies to a Lambda function
4. Lambda calls `polly_client.synthesize_speech()` with the neural engine
5. AWS Polly returns an MP3 audio stream
6. Lambda base64-encodes the audio and returns it as JSON
7. Frontend decodes the blob, creates an Object URL, and streams playback

## Architecture

```
┌──────────────────────────────┐
│  GitHub Pages                │
│  React Frontend (static)     │
│  Rate Limiting (localStorage)│
└──────────┬───────────────────┘
           │ POST /api/generate
           ▼
┌──────────────────────────────┐
│  API Gateway (us-east-1)     │
│  REST API + CORS             │
└──────────┬───────────────────┘
           │ Lambda Proxy
           ▼
┌──────────────────────────────┐
│  AWS Lambda (Python 3.11)    │
│  boto3 Polly Client          │
│  Base64 Encoding             │
└──────────┬───────────────────┘
           │ synthesize_speech()
           ▼
┌──────────────────────────────┐
│  AWS Polly (us-east-1)       │
│  Neural TTS Engine           │
│  MP3 Output Stream           │
└──────────────────────────────┘
```

## Features

- **Six Neural Voices** — Joanna, Matthew, Amy, Brian, Emma, and Justin with natural intonation
- **Audio Player** — Play/pause, progress bar, elapsed/total time display
- **MP3 Download** — Save generated audio files with timestamped filenames
- **Input Validation** — Character counter, empty-text rejection, error messaging
- **Rate Limiting** — 3 generations per visitor via localStorage to control AWS Polly costs
- **Serverless Backend** — Lambda + API Gateway for zero idle cost
- **Multi-Stage Docker Build** — Node 22 build stage → Nginx Alpine production image for local development

## Deployment

The app runs on a fully serverless stack with no idle costs:

- **Frontend**: Static React build deployed to GitHub Pages via GitHub Actions. On every push to `main`, the workflow installs dependencies, builds with the API Gateway URL baked in, and deploys to Pages.
- **Backend**: A single AWS Lambda function (Python 3.11) behind API Gateway handles `/api/generate` requests. The Lambda has an IAM role with only `polly:SynthesizeSpeech` permission — minimal access for minimal risk.
- **Rate Limiting**: Each visitor gets 3 text-to-speech generations tracked in localStorage. This keeps AWS Polly costs near zero for a portfolio demo while still providing a fully functional experience.

## Technology Highlights

- **React 19** with functional components and hooks (`useState`, `useEffect`, `useRef`) for audio lifecycle management
- **Object URL cleanup** in `useEffect` return to prevent memory leaks from audio blobs
- **AWS Lambda** with API Gateway proxy integration — serverless backend with CORS handled in the Lambda response headers
- **Multi-stage Docker build** compiles the Vite app then serves static files from Nginx Alpine — minimal production image size
- **CORS middleware** on Flask enables cross-origin requests for local development

## Author

**Kathleen Hill**
- Portfolio: [khilldata.com](https://khilldata.com)
- GitHub: [@kd365](https://github.com/kd365)
- LinkedIn: [kathleen-hill322](https://www.linkedin.com/in/kathleen-hill322/)
