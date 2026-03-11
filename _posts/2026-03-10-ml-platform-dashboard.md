---
layout: post
title: ML Platform Ops Dashboard
image: "/posts/Dashboard.png"
tags: [kubernetes, sagemaker, terraform, fastapi, react, github-actions, docker]
---

# ML Platform Delivery — Multi-Client SageMaker + Kubernetes

This project demonstrates the development of an internal platform that orchestrates ML endpoints for three client contracts using SageMaker, FastAPI, Kubernetes (EKS), Terraform, GitHub Actions CI/CD, and a React dashboard. This was intended to meet the following scenario:

You are a developer on a devops team at a contracting firm that builds ML-powered data pipelines for external clients. Each client brings a different dataset and use case, and your team is responsible for training, deploying, and maintaining the model endpoints they integrate into their own services. You currently have three active client contracts:

* Client A (Financial Services) — you built an XGBoost model that scores consumer credit risk from their history data, exposed as an endpoint they call from their loan approval workflow
* Client B (Outdoor Recreation) — you built a clustering model that ranks locations from public datasets (national parks, visitation rules, capacity) into accessibility and feasibility scores for their trip-planning app
* Client C (Legal Tech) — you built an NLP model that extracts structured fields (parties, terms, dates) from raw contracts for their document processing pipeline

Each client's model runs on its own SageMaker endpoint behind a FastAPI service your team built. Your platform handles deployment, routing, and monitoring across all three so your team can manage them from one place.

Your implementation should demonstrate how this can be done safely, repeatably, and with clear operational visibility. 

## Live Demo

**Live Dashboard:** [https://kd365.github.io/assessment-iv/](https://kd365.github.io/assessment-iv/)

**Source Code:** [https://github.com/kd365/assessment-iv](https://github.com/kd365/assessment-iv)

The dashboard runs in demo mode when backend services are offline, displaying sample predictions from each model.

## Overview

The platform serves three external clients, each with a different ML use case:

| Client | Use Case | Model | Input |
|--------|----------|-------|-------|
| **Financial Services** | Credit risk scoring | XGBoost (binary classification) | 43 numeric features |
| **Outdoor Recreation** | Park accessibility ranking | K-Means (clustering, k=5) | 12 park features |
| **Legal Tech** | Contract entity extraction | HuggingFace BERT NER | Free text |

Each client's model is deployed as a serverless SageMaker endpoint in us-west-2, wrapped in a FastAPI service, and orchestrated on an EKS cluster in us-east-1 with full namespace isolation.

## Technology Stack

**ML & Backend:**
- AWS SageMaker (serverless endpoints)
- Python 3.11 / FastAPI / boto3
- Models: XGBoost, K-Means, HuggingFace BERT NER

**Orchestration:**
- Kubernetes (Amazon EKS)
- Namespace isolation per client
- ConfigMaps, Secrets, ResourceQuotas, LimitRanges
- Liveness, readiness, and startup probes

**Infrastructure as Code:**
- Terraform with Kubernetes provider
- S3 remote state with DynamoDB locking
- Manages namespaces, ConfigMaps, and quotas as code

**CI/CD:**
- GitHub Actions (build, push to GHCR, deploy to EKS, verify rollouts)
- Automated on push to main

**Dashboard:**
- React + Vite + Tailwind CSS
- Live health polling (15s interval)
- Test-request interface for each model
- Demo mode with sample data when backends are offline

## Key Features

- **A/B Model Routing** — Client A supports traffic splitting between model versions (e.g., 80/20 v1/v2) controlled via ConfigMap, enabling canary deployments without code changes
- **Retry Logic** — Exponential backoff handles SageMaker serverless cold starts gracefully
- **Cross-Region Architecture** — EKS in us-east-1 calls SageMaker in us-west-2, with region config managed through ConfigMaps
- **Full CI/CD Pipeline** — Push to main triggers Docker builds, GHCR push, EKS deployment, and rollout verification

## Author

**Kathleen Hill**
- Portfolio: [khilldata.com](https://khilldata.com)
- GitHub: [@kd365](https://github.com/kd365)
- LinkedIn: [kathleen-hill322](https://www.linkedin.com/in/kathleen-hill322/)
