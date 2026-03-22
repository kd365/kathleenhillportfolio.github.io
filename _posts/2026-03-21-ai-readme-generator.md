---
layout: post
title: AI README Generator — Multi-Agent Pipeline
image: "/posts/readme-generator.png"
tags: [aws-bedrock, terraform, step-functions, prompt-engineering, multi-agent, python, lambda]
---

# AI README Generator — Multi-Agent Orchestration with AWS Bedrock

A production-grade tool that generates professional README files for any public GitHub repository using a pipeline of five specialized AI agents. Point it at a repo, and it clones, analyzes file structure and contents, runs a security audit, and produces a structured README with project summary, installation instructions, usage examples, and security notes.

**Source Code:** [https://github.com/kd365/readme-generator](https://github.com/kd365/readme-generator)

---

## The Problem

README files are the first thing anyone sees when they encounter a repository, yet they're consistently one of the most neglected artifacts in software projects. Writing a good one requires understanding the project's purpose, identifying the correct build tools and package managers, documenting accurate usage commands, and keeping it all current. For data teams evaluating open-source tools or onboarding onto new codebases, a missing or outdated README creates friction before work even begins.

## The Approach

Rather than using a single monolithic prompt, this system decomposes README generation into five specialized agents — each with a narrowly scoped task, strict constraints, and a defined output format. This mirrors how production ML pipelines are built: discrete, testable components with clear interfaces.

The agents are deployed as AWS Bedrock Agents via Terraform and can be orchestrated two ways: a local Python CLI for fast iteration, or a serverless AWS Step Functions pipeline for automated execution.

## Architecture

The system provides two orchestration paths that share the same deployed agents:

**Local CLI** — clones the repo to your machine, reads files directly, calls agents sequentially, runs a local security scan, and writes the output to disk.

**Serverless (Step Functions)** — a fully managed state machine that clones via Lambda, runs three analytical agents in parallel, compiles the result, and saves to S3. Includes declarative retry logic with exponential backoff and error handling with fallback states.

Both paths feed the same five agents:

| Agent | Role | Key Design Decision |
|-------|------|-------------------|
| **Repo Scanner** | Clones repo, returns file list + key file contents | Bypassed in Step Functions path — direct Lambda returns structured JSON instead of narrative |
| **Project Summarizer** | Writes a factual project summary | Strict auditor rule prevents hallucination from training data |
| **Installation Guide** | Writes Getting Started section | Reads `packageManager` field and lock files to detect correct build tool |
| **Usage Examples** | Writes Usage section | Constrained to runtime usage only — no overlap with installation |
| **Final Compiler** | Assembles sections into formatted Markdown | Deduplicates overlapping content between sections |

## Prompt Engineering

Each agent prompt follows a structured framework: **Persona**, **Critical Rule**, **Goal**, **Constraints**, and **Output Format**.

The most impactful design choice was the **Strict Auditor Rule** applied to every analytical agent:

> *"You must ONLY use the provided data to generate your response. If a detail is not explicitly found in the provided files or contents, do NOT include it. Do not use your internal knowledge about common libraries or frameworks to fill in gaps."*

This prevents a class of failures where the model confidently generates plausible but incorrect information — like adding `pip install` instructions to a Node.js project because it saw a `pyproject.toml` that only contained linter configuration.

Other prompt refinements that measurably improved output quality:

- **Dev vs. runtime prerequisite separation** — a `pyproject.toml` with only `[tool.ruff]` is a dev dependency, not a runtime requirement
- **Package manager detection** — reads `packageManager` field in `package.json` and checks for lock files (`pnpm-lock.yaml`, `yarn.lock`) instead of defaulting to npm
- **One-shot examples** — each agent receives a concrete output template to eliminate formatting ambiguity
- **Negative constraints** — "Do NOT repeat installation steps in the Usage section" proved more effective than positive instructions alone

## Security Hardening

The tool clones arbitrary public repositories to the local filesystem, which introduces attack surface. Every clone goes through a security audit:

| Check | What It Detects |
|-------|----------------|
| Git hooks disabled | Prevents malicious `post-checkout` scripts from executing |
| Symlink detection | Blocks symlinks pointing outside the repo directory |
| Path traversal | Skips files with `..` in their path |
| Clone size check | Warns on repositories exceeding 500MB |
| IDE config detection | Flags `.vscode/tasks.json` and `.devcontainer/` auto-run files |
| Secret scanning | Finds hardcoded API keys and credentials (filters test files and docs) |

The clone security report is displayed after every clone operation, giving visibility into what was checked and what was found before any data is sent to the model.

![Clone Security Report](/img/posts/SecurityScan.png)

## Sample Output

Tested against [OpenClaw](https://github.com/openclaw/openclaw), a large open-source multi-channel AI gateway with 2,000+ files and 50+ extensions. The generator correctly:

- Identified pnpm as the package manager (not npm) by reading the `packageManager` field in `package.json`
- Separated runtime prerequisites (Node.js 24+, pnpm) from dev-only tools (Python for ruff/pytest)
- Generated accurate CLI commands based on actual source code, not assumptions
- Produced clean section boundaries with no duplication between Getting Started and Usage
- Flagged 4 genuine security findings while filtering out 150+ false positives from test files

![Generated README Preview](/img/posts/Preview.png)

## Step Functions — Parallel Agent Execution

The serverless path uses AWS Step Functions to orchestrate the pipeline with patterns from production ML workflows:

**Parallel execution** — the three analytical agents and security scan run simultaneously as a Parallel state, reducing total execution time from ~60 seconds (sequential) to ~20 seconds.

![Step Functions Execution Graph](/img/posts/readme-generator-stepfunctions.png)

**Declarative retry with backoff** — each step retries 3 times with exponential backoff (10s, 20s, 40s intervals), handling transient Bedrock throttling without custom retry code.

**Error handling with fallback** — if the scanner fails, the pipeline hard-fails. If an analytical agent fails, the compiler receives placeholder text and still produces a valid document.

## Infrastructure as Code

All AWS resources are managed with Terraform using reusable modules:

| Resource | Purpose |
|----------|---------|
| 3 Terraform modules | Reusable S3, IAM, and Bedrock Agent modules |
| 5 Bedrock Agents | Deployed with configurable `name_suffix` for multi-tenant environments |
| 4 Lambda functions | Scanner, agent invoker, security scan, S3 writer |
| Step Functions state machine | Serverless orchestration with parallel branches |
| S3 + DynamoDB | Remote Terraform state with locking |
| GitHub Actions + OIDC | CI/CD pipeline with keyless AWS authentication |

## Technology Stack

| Component | Technology |
|-----------|-----------|
| AI Agents | AWS Bedrock (Claude Sonnet 4) |
| Orchestration | AWS Step Functions + Python CLI |
| Infrastructure | Terraform with reusable modules |
| Compute | AWS Lambda (Python 3.11) |
| Storage | S3 (output + Terraform state) |
| CI/CD | GitHub Actions with OIDC authentication |
| CLI Interface | Python + Rich library |
| Security | Custom scanning + git hook protection |

## Key Takeaways

- **Decomposition over monolith** — five focused agents with strict output contracts produce more reliable results than a single complex prompt
- **Prompt constraints matter more than instructions** — telling the model what NOT to do (strict auditor rule, no ecosystem guessing) eliminated the most common failure modes
- **Post-processing is essential** — validating install commands against actual repo files catches hallucinations that even strong prompts miss
- **Infrastructure as Code enables iteration** — deploying agents via Terraform meant prompt changes could be tested in seconds with `terraform apply`
- **Two orchestration paths serve different needs** — local CLI for development speed, Step Functions for production automation

---

*Built as part of the AICO Delta Fall 2025 AI Engineering program.*

**Author:** Kathleen Hill
[Portfolio](https://khilldata.com) | [GitHub](https://github.com/kd365) | [LinkedIn](https://linkedin.com/in/kathleenhill)
