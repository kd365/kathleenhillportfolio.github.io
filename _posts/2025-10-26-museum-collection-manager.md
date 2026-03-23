---
layout: post
title: Museum Collection Manager App
image: "/posts/louvre.jpg"
tags: [flask, sqlalchemy, aws-bedrock, docker, python]
---

# Museum Collection Manager

A full-stack web application for managing museum collections, artworks, and artists with AI-powered curation features. Built with Flask, SQLAlchemy, and AWS Bedrock.

**Source Code:** [https://github.com/kd365/museum-collection-manager](https://github.com/kd365/museum-collection-manager)

---

## Overview

This application serves as a digital collection management system for museums, enabling curators to catalog artworks, track acquisitions, and manage relationships between artists, artworks, and institutions. It integrates AWS Bedrock to generate AI-powered artwork descriptions and collection overviews, demonstrating how generative AI can augment domain-specific workflows.

## Features

- **Collection Curation**: Track artworks across museums with acquisition details, accession numbers, gallery locations, and display status
- **Artist & Artwork Cataloging**: Detailed records including dimensions, valuations, art movements, signatures, and provenance
- **Museum Registry**: Museum profiles with visitor statistics, admission pricing, and location data
- **AI Artwork Descriptions**: Generate museum placard-style descriptions using Claude Sonnet 4 on AWS Bedrock
- **AI Collection Overviews**: Automatically generates a thematic collection summary when a museum accumulates 5+ artworks
- **SQL Playground**: Interactive query builder with parameterized queries and dynamic column selection
- **Role-Based Access Control**: Admin users manage the collection; visitors can browse, search, and export
- **Data Export**: Export any table to CSV for analysis

## Technology Stack

| Component | Technology |
|-----------|-----------|
| Backend | Python 3.12, Flask |
| ORM | SQLAlchemy with Flask-Migrate |
| Database | SQLite (dev), MySQL (production) |
| AI/ML | AWS Bedrock (Claude Sonnet 4) via boto3 |
| Authentication | Flask-Login with admin/viewer roles |
| Forms & Validation | Flask-WTF, WTForms with CSRF protection |
| Frontend | HTML5/CSS3, JavaScript, Jinja2 templates |
| Containerization | Docker, docker-compose |
| Production Server | Gunicorn |

## AI Integration

The application integrates AWS Bedrock to provide two AI-powered features:

**Artwork Descriptions** — Generates 2-3 sentence museum placard descriptions from structured artwork metadata. The prompt uses a curator persona with constraints to produce descriptions focused on artistic significance, technique, and historical context.

**Collection Overviews** — When a museum's collection reaches 5+ artworks, the system builds a prompt from all artwork metadata in the collection and generates a thematic overview highlighting movements, notable pieces, and curatorial narrative. This runs once per threshold and persists with the museum record.

Both features call Bedrock's `invoke_model` API with structured prompts specifying persona, output constraints, and token limits.

## Data Model

Four core entities with foreign key relationships:

- **Artists** — biographical data, nationality, art movement, primary medium
- **Artworks** — title, dimensions, medium, valuation, AI-generated description (FK to Artist)
- **Museums** — location, visitor stats, admission fee, AI-generated collection overview
- **Collections** — junction table linking artworks to museums with acquisition method, cost, status, gallery location

## Security

- Parameterized SQL queries throughout (SQLAlchemy ORM + secured SQL Playground)
- CSRF protection on all forms via Flask-WTF
- Role-based access: admin-only create/edit/delete routes
- Secure session cookies (HTTPOnly, SameSite, Secure flags)
- Environment-based secrets management

## Sample Data

The application ships with historically accurate seed data:

- 6 artists (Van Gogh, Monet, Kahlo, da Vinci, Kusama, O'Keeffe)
- 9 artworks with real dimensions, creation dates, and estimated values
- 4 museums (MoMA, Musee d'Orsay, Museo Frida Kahlo, The Louvre)
- 9 collection entries with accession numbers and acquisition details

---

**Author:** Kathleen Hill
[Portfolio](https://khilldata.com) | [GitHub](https://github.com/kd365)
