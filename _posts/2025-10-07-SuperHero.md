---
layout: post
title: Superhero Name Generator — Classic ML vs Foundation Model
image: "/posts/pow.png"
image_style: contain
tags: [react, python, tensorflow, aws-sagemaker, aws-bedrock, aws-lambda, api-gateway]
---

# Superhero Name Generator — Side-by-Side AI Comparison

**Live Demo:** [https://kd365.github.io/superhero-name-generator/](https://kd365.github.io/superhero-name-generator/)

**Source Code:** [https://github.com/kd365/superhero-name-generator](https://github.com/kd365/superhero-name-generator)

A full-stack web application that generates superhero names using two AI approaches side by side — a **Classic ML** model (TensorFlow LSTM deployed on SageMaker) and a **Foundation Model** (Amazon Bedrock Nova Lite + Titan Image Generator). Enter a seed word and see how a trained LSTM compares to a large language model in real time.

This project evolved from a [Coursera Guided Project](https://www.coursera.org/learn/superhero-tensorflow) ([Certificate](https://www.coursera.org/account/accomplishments/verify/56YBSC92KTYX)) where I trained the original LSTM model on 9,000+ superhero names. The upgrade adds a modern React frontend, serverless AWS backend, and a Foundation Model comparison.

## Overview

| Layer | Technology |
|-------|-----------|
| **Frontend** | React 19, Vite |
| **Backend** | AWS Lambda (Python 3.11), API Gateway |
| **Classic ML** | TensorFlow LSTM on SageMaker Serverless |
| **Foundation Model** | Amazon Bedrock (Nova Lite + Titan Image Generator v2) |
| **Hosting** | GitHub Pages (frontend), AWS Lambda (backend) |
| **CI/CD** | GitHub Actions (build + deploy to Pages) |

## How It Works

### Classic ML (SageMaker)
1. User enters a seed word (e.g., "star")
2. Lambda converts seed characters to indices using the 28-character vocabulary
3. Lambda iteratively calls the SageMaker endpoint running TF Serving
4. Each iteration pads the sequence and gets the next character prediction via argmax
5. Returns the completed name (e.g., "Starlord")

### Foundation Model (Bedrock)
1. User enters the same seed word
2. Lambda calls Nova Lite via the Converse API for a name + backstory
3. Lambda sanitizes the name and calls Titan Image Generator for a hero portrait
4. Returns name, backstory, and base64-encoded portrait

Both modes fire simultaneously — the Generate button triggers parallel API calls so results appear side by side.

## Architecture

```
┌──────────────────────────────────────────────────┐
│  GitHub Pages — React Frontend                   │
│  Side-by-side: Classic ML vs Foundation Model    │
│  Rate Limiting (3 calls total, localStorage)     │
└──────────┬───────────────────────────────────────┘
           │ POST /api/generate  { seed, mode }
           ▼
┌──────────────────────────────────────────────────┐
│  API Gateway (us-east-1)                         │
│  REST API + CORS                                 │
└──────────┬───────────────────────────────────────┘
           │ Lambda Proxy
           ▼
┌──────────────────────────────────────────────────┐
│  AWS Lambda (Python 3.11)                        │
│  Routes by mode parameter:                       │
│                                                  │
│  mode="classic"     │  mode="bedrock"            │
│  ↓                  │  ↓                         │
│  SageMaker Runtime  │  Bedrock Runtime           │
│  invoke_endpoint()  │  converse() + invoke_model │
└──────┬──────────────┴──────┬─────────────────────┘
       ▼                     ▼
┌──────────────┐  ┌──────────────────────────────┐
│  SageMaker   │  │  Bedrock                     │
│  Serverless  │  │  Nova Lite → name + backstory│
│  Endpoint    │  │  Titan Image → hero portrait │
│  LSTM Model  │  └──────────────────────────────┘
└──────────────┘
```

## Features

- **Side-by-Side Comparison** — Classic ML and Foundation Model results displayed simultaneously
- **Classic ML** — Character-level LSTM (16,229 params) generates names based on patterns from 9,000+ superhero names
- **Foundation Model** — Nova Lite creates unique names + backstories, Titan Image Generator produces hero portraits
- **Content Safety** — Sanitized prompts and retry logic for Titan's content filters
- **Rate Limiting** — 3 generations per visitor via localStorage to control AWS costs
- **Serverless Architecture** — Lambda + API Gateway + SageMaker Serverless = zero idle cost

## LSTM Model Details

The Classic ML model is a character-level LSTM:

- **Architecture**: Embedding(29,8) → Conv1D(64) → MaxPool → LSTM(32) → Dense(29, softmax)
- **Parameters**: 16,229
- **Training Data**: 9,000+ superhero names, 88,279 character sequences
- **Vocabulary**: 28 lowercase characters + end token
- Converted from Keras to SavedModel format and deployed on SageMaker Serverless via TF Serving

## Technology Highlights

- **React 19** with functional components and hooks for managing parallel async requests
- **SageMaker Serverless Inference** — TF Serving container with SavedModel, zero idle cost
- **Bedrock Converse API** — Nova Lite for structured JSON generation (name + backstory)
- **Titan Image Generator v2** — Text-to-image with content filter sanitization and retry logic
- **Lambda routing** — Single function handles both ML modes via the `mode` parameter
- **GitHub Actions CI/CD** — Automated build and deploy to Pages on every push

## Author

**Kathleen Hill**
- Portfolio: [khilldata.com](https://khilldata.com)
- GitHub: [@kd365](https://github.com/kd365)
- LinkedIn: [kathleen-hill322](https://www.linkedin.com/in/kathleen-hill322/)
