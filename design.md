# Design Document: Eklavya.ai

> **A comprehensive educational technology solution that transforms complex technical concepts into engaging, interactive learning experiences**

---

## Table of Contents

1. [Overview](#1-overview)
2. [System Architecture](#2-system-architecture)
3. [Feature 1: AI-Powered Educational Video Generation](#3-feature-1-ai-powered-educational-video-generation)
4. [Feature 2: Interactive Code Player (CodeVid)](#4-feature-2-interactive-code-player-codevid)
5. [Feature 3: PDF to Video Tutorials](#5-feature-3-pdf-to-video-tutorials)
6. [Feature 4: Live AI Screenshare Tutoring](#6-feature-4-live-ai-screenshare-tutoring)
7. [Feature 5: Graph RAG Document Analyzer](#7-feature-5-graph-rag-document-analyzer)
8. [Cross-Cutting Concerns](#8-cross-cutting-concerns)
9. [Data Models](#9-data-models)
10. [API Design](#10-api-design)
11. [Deployment Architecture](#11-deployment-architecture)
12. [Performance Optimization](#12-performance-optimization)
13. [Testing Strategy](#13-testing-strategy)
14. [Error Handling](#14-error-handling)
15. [Scalability Considerations](#15-scalability-considerations)
16. [Cost Optimization](#16-cost-optimization)
17. [Future Enhancements](#17-future-enhancements)

---

## 1. Overview

Eklavya.ai is a comprehensive educational technology solution that integrates five core features to help people learn faster, work smarter, and become more productive while building or understanding technology.

### 1.1 Design Principles

| Principle           | Description                                                                 |
| ------------------- | --------------------------------------------------------------------------- |
| **Modularity**      | Each feature operates as an independent module with well-defined interfaces |
| **Scalability**     | Horizontal scaling through containerized workers and serverless components  |
| **Reliability**     | Multi-provider fallback chains for critical services                        |
| **Performance**     | Parallel processing, intelligent caching, and CDN delivery                  |
| **Intelligence**    | RAG-enhanced AI for accurate, context-aware content generation              |
| **User Experience** | Real-time progress tracking and responsive interfaces                       |

### 1.2 Technology Stack

```
┌─────────────────────────────────────────────────────────────────┐
│                     TECHNOLOGY STACK                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  Frontend:    Next.js • React • TypeScript • Monaco Editor     │
│               Sandpack • WebRTC • PDF.js                        │
│                                                                 │
│  Backend:     FastAPI (Python) • WebSocket • REST API          │
│               AWS Lambda • ECS Fargate                          │
│                                                                 │
│  AI/ML:       AWS Bedrock (Claude) • Groq (Llama)              │
│               Google Gemini • Sarvam AI TTS                     │
│               Amazon Titan Embeddings                           │
│                                                                 │
│  Storage:     AWS S3 • DynamoDB • CloudFront CDN               │
│               Redis (Cache) • OpenSearch (Vector DB)            │
│                                                                 │
│  Video:       Manim • FFmpeg • pydub                            │
│                                                                 │
│  Code Exec:   Piston API (self-hosted) • Pyodide • Sandpack    │
│                                                                 │
│  Real-time:   WebRTC • Amazon Kinesis Video Streams            │
│               Gemini Live API                                   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## 2. System Architecture

### 2.1 High-Level Architecture

```
╔═══════════════════════════════════════════════════════════════════════════╗
║                            EKLAVYA.AI                                     ║
║                   Complete System Architecture                            ║
╚═══════════════════════════════════════════════════════════════════════════╝

┌───────────────────────────────────────────────────────────────────────────┐
│                          FRONTEND LAYER                                   │
│                     (Next.js + React + TypeScript)                        │
├───────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│   ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌────────────┐  │
│   │  Video Gen   │  │   CodeVid    │  │ PDF to Video │  │ Screenshare│  │
│   │   Studio     │  │    Player    │  │  Converter   │  │   Tutor    │  │
│   │              │  │              │  │              │  │            │  │
│   │ • Prompt UI  │  │ • Monaco Ed  │  │ • Upload UI  │  │ • WebRTC   │  │
│   │ • Mood Sel   │  │ • Execution  │  │ • Section Sel│  │ • Chat UI  │  │
│   │ • Progress   │  │ • Overlays   │  │ • Batch Gen  │  │ • Voice    │  │
│   └──────────────┘  └──────────────┘  └──────────────┘  └────────────┘  │
│                                                                           │
│   ┌───────────────────────────────────────────────────────────────────┐  │
│   │              Document Analyzer (Graph RAG)                        │  │
│   │  • PDF Viewer  • Line Highlight  • Q&A Search  • References      │  │
│   └───────────────────────────────────────────────────────────────────┘  │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
                                     │
                                     │ HTTPS/WSS
                                     ▼
┌───────────────────────────────────────────────────────────────────────────┐
│                   API GATEWAY + CLOUDFRONT CDN                            │
│                 (REST + WebSocket + Static Assets)                        │
├───────────────────────────────────────────────────────────────────────────┤
│   • /api/v1/*  → Lambda/FastAPI                                           │
│   • /ws/*      → WebSocket Handler                                        │
│   • /videos/*  → S3 via CloudFront                                        │
└───────────────────────────────────────────────────────────────────────────┘
                                     │
                                     │ API Calls
                                     ▼
┌───────────────────────────────────────────────────────────────────────────┐
│                      BACKEND SERVICES LAYER                               │
│                     (FastAPI + AWS Lambda)                                │
├───────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│   ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌────────────┐  │
│   │  Video API   │  │  Code Exec   │  │ PDF Processor│  │ Screenshare│  │
│   │   Handler    │  │   Handler    │  │   Handler    │  │  Handler   │  │
│   │              │  │              │  │              │  │            │  │
│   │ • Generate   │  │ • Execute    │  │ • Parse PDF  │  │ • WebRTC   │  │
│   │ • Progress   │  │ • Sandbox    │  │ • Analyze    │  │ • Gemini   │  │
│   │ • Deliver    │  │ • Multi-lang │  │ • Batch Gen  │  │ • TTS      │  │
│   └──────────────┘  └──────────────┘  └──────────────┘  └────────────┘  │
│                                                                           │
│   ┌───────────────────────────────────────────────────────────────────┐  │
│   │          Progress Manager (WebSocket Broadcaster)                 │  │
│   │  • Job Tracking  • Real-time Updates  • Connection Pool           │  │
│   └───────────────────────────────────────────────────────────────────┘  │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
                                     │
                                     │ Job Queue
                                     ▼
┌───────────────────────────────────────────────────────────────────────────┐
│                       WORKER LAYER (ECS Fargate)                          │
│                     Auto-Scaling Container Workers                        │
├───────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│   ┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐      │
│   │  Video Worker    │  │  Code Sandbox    │  │   PDF Worker     │      │
│   │  ─────────────   │  │  ─────────────   │  │  ─────────────   │      │
│   │  • Manim Engine  │  │  • Piston API    │  │  • PyMuPDF       │      │
│   │  • FFmpeg        │  │  • 20+ Languages │  │  • Text Extract  │      │
│   │  • TTS (Sarvam)  │  │  • Isolated Env  │  │  • Image Extract │      │
│   │  • Watermark     │  │  • Resource Lim  │  │  • Claude AI     │      │
│   │  • S3 Upload     │  │  • Timeout: 5s   │  │  • Graph Build   │      │
│   └──────────────────┘  └──────────────────┘  └──────────────────┘      │
│                                                                           │
│   Scale: 1-20 tasks    Scale: 2-10 tasks     Scale: 1-5 tasks           │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
                                     │
                                     │ AWS SDK
                                     ▼
┌───────────────────────────────────────────────────────────────────────────┐
│                        AWS SERVICES LAYER                                 │
│                   Managed Cloud Infrastructure                            │
├───────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│   ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌────────────┐  │
│   │  S3 Storage  │  │  DynamoDB    │  │  Bedrock KB  │  │  Secrets   │  │
│   │  ──────────  │  │  ──────────  │  │  ──────────  │  │  Manager   │  │
│   │  • Videos    │  │  • Jobs      │  │  • Manim KB  │  │            │  │
│   │  • Images    │  │  • Users     │  │  • User PDFs │  │  • API Keys│  │
│   │  • Audio     │  │  • Sessions  │  │  • BM25 Idx  │  │  • Tokens  │  │
│   │  • PDFs      │  │  • Documents │  │  • Embeddings│  │  • Creds   │  │
│   │  • Cache     │  │              │  │  • Graph DB  │  │            │  │
│   └──────────────┘  └──────────────┘  └──────────────┘  └────────────┘  │
│                                                                           │
│   ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌────────────┐  │
│   │   Polly TTS  │  │  Transcribe  │  │   Kinesis    │  │ CloudWatch │  │
│   │  (Fallback)  │  │   (Audio)    │  │  (Streams)   │  │  (Metrics) │  │
│   └──────────────┘  └──────────────┘  └──────────────┘  └────────────┘  │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
                                     │
                                     │ External APIs
                                     ▼
┌───────────────────────────────────────────────────────────────────────────┐
│                     EXTERNAL SERVICES LAYER                               │
│                   Third-Party AI & Media Services                         │
├───────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│   ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌────────────┐  │
│   │  Groq LLM    │  │ Gemini API   │  │ Sarvam AI    │  │ Pollinations│  │
│   │  ──────────  │  │  ──────────  │  │  ──────────  │  │  ────────  │  │
│   │  • Scene     │  │  • Code Gen  │  │  • TTS       │  │  • Images  │  │
│   │    Planning  │  │  • Gemini    │  │  • 12+ langs │  │  • Flux    │  │
│   │  • Compound  │  │    Live      │  │  • Neural    │  │  • Free    │  │
│   │    Model     │  │  • Multimodal│  │    Voices    │  │  • 1024px  │  │
│   └──────────────┘  └──────────────┘  └──────────────┘  └────────────┘  │
│                                                                           │
│   ┌──────────────┐  ┌──────────────┐                                     │
│   │  Iconify API │  │ Stable Diff  │                                     │
│   │  ──────────  │  │  ──────────  │                                     │
│   │  • 275k+ SVG │  │  • SD 3.5    │                                     │
│   │  • Free      │  │  • Fallback  │                                     │
│   └──────────────┘  └──────────────┘                                     │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
```

### 2.2 Data Flow Architecture

```
╔═══════════════════════════════════════════════════════════════════════════╗
║                         COMPLETE DATA FLOW                                ║
║                    User Request → Final Delivery                          ║
╚═══════════════════════════════════════════════════════════════════════════╝

    USER REQUEST
    ────────────
    • Video Generation Prompt
    • PDF Upload
    • Code Execution
    • Screenshare Session
    • Document Query
         │
         ▼
┌────────────────────────────────────────────────────────────────────────────┐
│                         API GATEWAY (AWS)                                  │
│  • Route: /api/v1/*  → Lambda Functions                                   │
│  • Route: /ws/*      → WebSocket Connections                               │
│  • Route: /videos/*  → CloudFront CDN                                      │
└────────────────────────────────────────────────────────────────────────────┘
         │
         ├──────────────────┬──────────────────┬──────────────────┐
         ▼                  ▼                  ▼                  ▼
┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐
│ Lambda: Video   │ │ Lambda: Code    │ │ Lambda: PDF     │ │ Lambda: RAG     │
│ Generation      │ │ Execution       │ │ Processing      │ │ Query           │
└────────┬────────┘ └────────┬────────┘ └────────┬────────┘ └────────┬────────┘
         │                   │                   │                   │
         ▼                   ▼                   ▼                   ▼
┌────────────────────────────────────────────────────────────────────────────┐
│                         JOB QUEUE (DynamoDB)                               │
│  • job_id (PK)  • status  • progress  • created_at  • updated_at          │
│  • user_id      • type    • metadata  • result_url  • error_msg           │
└────────────────────────────────────────────────────────────────────────────┘
         │
         ├──────────────────┬──────────────────┬──────────────────┐
         ▼                  ▼                  ▼                  ▼
┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐
│ ECS: Video      │ │ ECS: Code       │ │ ECS: PDF        │ │ Bedrock KB      │
│ Worker          │ │ Sandbox         │ │ Worker          │ │ Query Engine    │
│                 │ │                 │ │                 │ │                 │
│ • Manim Render  │ │ • Piston API    │ │ • PyMuPDF       │ │ • BM25 Search   │
│ • FFmpeg        │ │ • 20+ Languages │ │ • Claude AI     │ │ • Graph Trav    │
│ • TTS Sarvam    │ │ • Timeout: 5s   │ │ • Image Extract │ │ • Embeddings    │
└────────┬────────┘ └────────┬────────┘ └────────┬────────┘ └────────┬────────┘
         │                   │                   │                   │
         ├───────────────────┴───────────────────┴───────────────────┘
         │
         ▼
┌────────────────────────────────────────────────────────────────────────────┐
│                         AI SERVICES LAYER                                  │
│                                                                            │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  │
│  │ Groq LLM     │  │ Gemini API   │  │ AWS Bedrock  │  │ Sarvam TTS   │  │
│  │ (Scene Plan) │  │ (Code Gen)   │  │ (RAG/Claude) │  │ (12+ langs)  │  │
│  └──────────────┘  └──────────────┘  └──────────────┘  └──────────────┘  │
│                                                                            │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  │
│  │ Pollinations │  │ Iconify API  │  │ Amazon Polly │  │ Transcribe   │  │
│  │ (Images)     │  │ (275k Icons) │  │ (TTS Backup) │  │ (Audio→Text) │  │
│  └──────────────┘  └──────────────┘  └──────────────┘  └──────────────┘  │
└────────────────────────────────────────────────────────────────────────────┘
         │
         ▼
┌────────────────────────────────────────────────────────────────────────────┐
│                         STORAGE LAYER (AWS S3)                             │
│                                                                            │
│  /videos/{job_id}.mp4     /images/{hash}.png     /audio/{segment}.mp3    │
│  /pdfs/{doc_id}.pdf       /code/{job_id}.py      /cache/{prompt_hash}    │
└────────────────────────────────────────────────────────────────────────────┘
         │
         ▼
┌────────────────────────────────────────────────────────────────────────────┐
│                    CLOUDFRONT CDN (Global Delivery)                        │
│  • Edge Locations: 400+ worldwide                                         │
│  • Cache TTL: 24 hours for videos, 7 days for images                      │
│  • HTTPS Only, Signed URLs for private content                            │
└────────────────────────────────────────────────────────────────────────────┘
         │
         ▼
┌────────────────────────────────────────────────────────────────────────────┐
│                    WEBSOCKET PROGRESS UPDATES                              │
│  • Real-time job status: PENDING → PROCESSING → COMPLETED                 │
│  • Progress percentage: 0% → 100%                                          │
│  • Stage messages: "Generating scene...", "Rendering video..."            │
└────────────────────────────────────────────────────────────────────────────┘
         │
         ▼
    USER BROWSER
    ────────────
    • Video Player
    • Download Link
    • Share Options
```

---

## 3. Feature 1: AI-Powered Educational Video Generation

### 3.1 Overview

Transform text prompts into professional animated educational videos using AI-driven scene planning, code generation, and multi-language narration.

### 3.2 Architecture Diagram

```
╔═══════════════════════════════════════════════════════════════════════════╗
║              FEATURE 1: VIDEO GENERATION PIPELINE                         ║
║                  Text Prompt → Animated MP4 Video                         ║
╚═══════════════════════════════════════════════════════════════════════════╝

┌───────────────────────────────────────────────────────────────────────────┐
│                          USER INPUT                                       │
├───────────────────────────────────────────────────────────────────────────┤
│  • Prompt: "Explain how neural networks learn"                            │
│  • Language: Hindi / English / Tamil / etc.                               │
│  • Mood: Professional / Playful / 3Blue1Brown / Dramatic                  │
│  • Quality: 240p / 480p / 720p / 1080p                                    │
│  • Options: Handmanim style, Background music, Transcription              │
└───────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌───────────────────────────────────────────────────────────────────────────┐
│  STAGE 1: SCENE PLANNING (0-15%)                                          │
├───────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  Groq LLM (Compound Model)                                      │    │
│   │  ─────────────────────────                                      │    │
│   │  Input: User prompt + SCENE_SYSTEM_PROMPT                       │    │
│   │                                                                 │    │
│   │  5-Layer Educational Architecture:                              │    │
│   │  ┌──────────────────────────────────────────────────────────┐  │    │
│   │  │ 1. HOOK (15-20%)      → Curiosity-driven question       │  │    │
│   │  │ 2. FOUNDATION (20-25%) → Clear analogies & basics       │  │    │
│   │  │ 3. MECHANISM (40-50%)  → Step-by-step explanation       │  │    │
│   │  │ 4. INSIGHT (10-15%)    → Hidden connections revealed    │  │    │
│   │  │ 5. SYNTHESIS (5-10%)   → Universal principles           │  │    │
│   │  └──────────────────────────────────────────────────────────┘  │    │
│   │                                                                 │    │
│   │  Output:                                                        │    │
│   │  • Structured scene description                                │    │
│   │  • Narration script (5 segments)                               │    │
│   │  • IMAGE_PROMPT: "neural network diagram with layers"          │    │
│   │  • IMAGE_PROMPT: "gradient descent visualization"              │    │
│   │  • ICON_PROMPT: "brain", "network", "chart"                    │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌───────────────────────────────────────────────────────────────────────────┐
│  STAGE 2: ASSET GENERATION (15-40%) - PARALLEL                            │
├───────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  ThreadPoolExecutor (max_workers=3)                             │    │
│   ├─────────────────────────────────────────────────────────────────┤    │
│   │                                                                 │    │
│   │   IMAGE 1              IMAGE 2              IMAGE 3             │    │
│   │   ───────              ───────              ───────             │    │
│   │      │                    │                    │                │    │
│   │      ▼                    ▼                    ▼                │    │
│   │  ┌────────┐          ┌────────┐          ┌────────┐            │    │
│   │  │ Cache? │          │ Cache? │          │ Cache? │            │    │
│   │  └───┬────┘          └───┬────┘          └───┬────┘            │    │
│   │      │ miss              │ miss              │ miss            │    │
│   │      ▼                   ▼                   ▼                 │    │
│   │  ┌────────────────────────────────────────────────────┐        │    │
│   │  │  Pollinations.ai (Flux Model)                      │        │    │
│   │  │  • 1024x1024 resolution                            │        │    │
│   │  │  • 90s timeout                                     │        │    │
│   │  │  • FREE, unlimited                                 │        │    │
│   │  └───────────────────┬────────────────────────────────┘        │    │
│   │                      │ fail                                    │    │
│   │                      ▼                                         │    │
│   │  ┌────────────────────────────────────────────────────┐        │    │
│   │  │  Stable Diffusion 3.5 (HuggingFace)                │        │    │
│   │  │  • Fallback provider                               │        │    │
│   │  │  • Rate limited                                    │        │    │
│   │  └───────────────────┬────────────────────────────────┘        │    │
│   │                      │ fail                                    │    │
│   │                      ▼                                         │    │
│   │  ┌────────────────────────────────────────────────────┐        │    │
│   │  │  Placeholder (PIL)                                 │        │    │
│   │  │  • Blue background + text                          │        │    │
│   │  │  • Always succeeds                                 │        │    │
│   │  └────────────────────────────────────────────────────┘        │    │
│   │                                                                 │    │
│   │   ICONS (Iconify API)                                           │    │
│   │   ────────────────────                                          │    │
│   │   • 275,000+ SVG icons                                          │    │
│   │   • Material Design, Font Awesome, Bootstrap, etc.              │    │
│   │   • Free, no authentication                                     │    │
│   │                                                                 │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│                                                                           │
│   Result: [image1.png, image2.png, image3.png] + [icon1.svg, ...]        │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌───────────────────────────────────────────────────────────────────────────┐
│  STAGE 3: MANIM CODE GENERATION (40-55%)                                  │
├───────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  RAG-Enhanced Code Generation                                   │    │
│   ├─────────────────────────────────────────────────────────────────┤    │
│   │                                                                 │    │
│   │  STEP 1: Retrieve Manim Documentation                           │    │
│   │  ┌────────────────────────────────────────────────────────┐    │    │
│   │  │  AWS Bedrock Knowledge Base                            │    │    │
│   │  │  • Manim official docs indexed with BM25               │    │    │
│   │  │  • Query: "How to create animated text with images"    │    │    │
│   │  │  • Returns: Top 5 relevant code examples               │    │    │
│   │  └────────────────────────────────────────────────────────┘    │    │
│   │                                                                 │    │
│   │  STEP 2: Generate Code with Context                             │    │
│   │  ┌────────────────────────────────────────────────────────┐    │    │
│   │  │  Gemini 2.5 Flash (Primary)                            │    │    │
│   │  │  ────────────────────────                              │    │    │
│   │  │  Input:                                                │    │    │
│   │  │  • Scene description from Stage 1                      │    │    │
│   │  │  • Image paths from Stage 2                            │    │    │
│   │  │  • Retrieved Manim docs (RAG context)                  │    │    │
│   │  │  • MANIM_SYSTEM_PROMPT (1400+ lines)                   │    │    │
│   │  │                                                        │    │    │
│   │  │  Configuration:                                        │    │    │
│   │  │  • temperature: 0.7                                    │    │    │
│   │  │  • max_output_tokens: 8192                             │    │    │
│   │  │                                                        │    │    │
│   │  │  Fallback Chain:                                       │    │    │
│   │  │  Gemini 2.5 → Gemini 1.5 → Groq Llama 3.3 70B          │    │    │
│   │  └────────────────────────────────────────────────────────┘    │    │
│   │                                                                 │    │
│   │  STEP 3: Code Extraction & Validation                           │    │
│   │  ┌────────────────────────────────────────────────────────┐    │    │
│   │  │  • extract_code_robust() - Multiple strategies         │    │    │
│   │  │  • extract_scene_class() - Find Scene class            │    │    │
│   │  │  • validate_manim_code() - Syntax check                │    │    │
│   │  │  • create_scene_wrapper() - Wrap if needed             │    │    │
│   │  │  • inject_image_paths() - Add generated_images[]       │    │    │
│   │  └────────────────────────────────────────────────────────┘    │    │
│   │                                                                 │    │
│   │  Output:                                                        │    │
│   │  ┌────────────────────────────────────────────────────────┐    │    │
│   │  │  from manim import *                                   │    │    │
│   │  │                                                        │    │    │
│   │  │  narration_segments = [                                │    │    │
│   │  │      "Hook: What if machines could learn...",          │    │    │
│   │  │      "Foundation: Neural networks are...",             │    │    │
│   │  │      "Mechanism: Each layer transforms...",            │    │    │
│   │  │      "Insight: Backpropagation adjusts...",            │    │    │
│   │  │      "Synthesis: This enables AI to..."                │    │    │
│   │  │  ]                                                     │    │    │
│   │  │                                                        │    │    │
│   │  │  class NeuralNetworkScene(Scene):                      │    │    │
│   │  │      def construct(self):                              │    │    │
│   │  │          self.camera.background_color = BLACK          │    │    │
│   │  │          generated_images = [...]                      │    │    │
│   │  │                                                        │    │    │
│   │  │          # Segment 1: Hook                             │    │    │
│   │  │          title = Text("Neural Networks")               │    │    │
│   │  │          self.play(Write(title))                       │    │    │
│   │  │          self.add_sound("audio1.mp3")                  │    │    │
│   │  │          self.wait(AUDIO1_DURATION)                    │    │    │
│   │  │          ...                                           │    │    │
│   │  └────────────────────────────────────────────────────────┘    │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌───────────────────────────────────────────────────────────────────────────┐
│  STAGE 4: TTS AUDIO GENERATION (55-60%)                                   │
├───────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  Sarvam AI Text-to-Speech (Primary)                             │    │
│   ├─────────────────────────────────────────────────────────────────┤    │
│   │                                                                 │    │
│   │  For each narration_segment:                                    │    │
│   │                                                                 │    │
│   │  1. Extract text from narration_segments[]                      │    │
│   │  2. Clean Unicode (math symbols → speakable text)               │    │
│   │  3. Generate audio via Sarvam API                               │    │
│   │     • Language: User-selected (12+ options)                     │    │
│   │     • Voice: Male/Female neural voices                          │    │
│   │     • Speed: 1.0 (configurable)                                 │    │
│   │                                                                 │    │
│   │  4. Save as audio1.mp3, audio2.mp3, ...                         │    │
│   │  5. Get duration using ffprobe                                  │    │
│   │     • audio1.mp3 → 3.04 seconds                                 │    │
│   │     • audio2.mp3 → 2.87 seconds                                 │    │
│   │     • audio3.mp3 → 4.12 seconds                                 │    │
│   │                                                                 │    │
│   │  6. Replace placeholders in code:                               │    │
│   │     • AUDIO1_DURATION → 3.04                                    │    │
│   │     • AUDIO2_DURATION → 2.87                                    │    │
│   │     • AUDIO3_DURATION → 4.12                                    │    │
│   │                                                                 │    │
│   │  Fallback: Amazon Polly (if Sarvam unavailable)                 │    │
│   │                                                                 │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌───────────────────────────────────────────────────────────────────────────┐
│  STAGE 5: MANIM RENDERING (60-90%)                                        │
├───────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  ECS Fargate Worker (Auto-Scaling)                              │    │
│   ├─────────────────────────────────────────────────────────────────┤    │
│   │                                                                 │    │
│   │  Command: python -m manim -ql scene.py NeuralNetworkScene       │    │
│   │                                                                 │    │
│   │  Quality Presets:                                               │    │
│   │  ┌──────────┬────────────┬───────────┬──────────────────┐      │    │
│   │  │ Preset   │ Resolution │ FPS       │ Render Time      │      │    │
│   │  ├──────────┼────────────┼───────────┼──────────────────┤      │    │
│   │  │ preview  │ 240p       │ 15        │ ~30 seconds      │      │    │
│   │  │ low      │ 480p       │ 15        │ ~2 minutes       │      │    │
│   │  │ medium   │ 720p       │ 30        │ ~5 minutes       │      │    │
│   │  │ high     │ 1080p      │ 60        │ ~10 minutes      │      │    │
│   │  └──────────┴────────────┴───────────┴──────────────────┘      │    │
│   │                                                                 │    │
│   │  Handmanim Style (Optional):                                    │    │
│   │  • Hand-drawn animation aesthetics                              │    │
│   │  • Sketch-like visual style                                     │    │
│   │  • Organic, less mechanical feel                                │    │
│   │                                                                 │    │
│   │  Output: raw_video.mp4 (no audio)                               │    │
│   │                                                                 │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌───────────────────────────────────────────────────────────────────────────┐
│  STAGE 6: POST-PROCESSING (90-100%)                                       │
├───────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  FFmpeg Pipeline                                                │    │
│   ├─────────────────────────────────────────────────────────────────┤    │
│   │                                                                 │    │
│   │  1. Concatenate Audio Segments (pydub)                          │    │
│   │     audio1.mp3 + audio2.mp3 + ... → combined_audio.mp3          │    │
│   │                                                                 │    │
│   │  2. Merge Audio with Video                                      │    │
│   │     ffmpeg -i raw_video.mp4 -i combined_audio.mp3 \             │    │
│   │            -c:v copy -c:a aac merged.mp4                        │    │
│   │                                                                 │    │
│   │  3. Add Watermark                                               │    │
│   │     ffmpeg -i merged.mp4 -i watermark.png \                     │    │
│   │            -filter_complex "[1:v]scale=-1:100[wm]; \            │    │
│   │             [0:v][wm]overlay=W-w-30:H-h-30" \                   │    │
│   │            -codec:a copy final.mp4                              │    │
│   │                                                                 │    │
│   │  4. Generate Transcription (Optional)                           │    │
│   │     • Amazon Transcribe: audio → text                           │    │
│   │     • Generate VTT subtitle file                                │    │
│   │     • Sync timestamps with video                                │    │
│   │                                                                 │    │
│   │  5. Add Background Music (Optional)                             │    │
│   │     • Royalty-free music track                                  │    │
│   │     • Volume: 20% (background level)                            │    │
│   │     • Fade in/out at start/end                                  │    │
│   │                                                                 │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌───────────────────────────────────────────────────────────────────────────┐
│                           FINAL OUTPUT                                    │
├───────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│  • Video: /videos/{job_id}.mp4 (served via CloudFront CDN)               │
│  • Code: /code/{job_id}.py (downloadable)                                │
│  • Subtitles: /videos/{job_id}.vtt (optional)                            │
│  • Thumbnail: /thumbnails/{job_id}.jpg (auto-generated)                  │
│                                                                           │
│  WebSocket Notification:                                                  │
│  {                                                                        │
│    "job_id": "550e8400-e29b-41d4-a716-446655440000",                     │
│    "status": "completed",                                                 │
│    "progress": 100,                                                       │
│    "video_url": "https://cdn.example.com/videos/550e8400.mp4",           │
│    "code_path": "/code/550e8400.py"                                      │
│  }                                                                        │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
```

### 3.3 Key Technical Details

**Educational Philosophy:**

- 5-layer architecture ensures deep understanding, not just information transfer
- Hook creates curiosity, Foundation builds context, Mechanism reveals process
- Insight shows hidden patterns, Synthesis connects to universal principles

**RAG Integration:**

- Bedrock Knowledge Base indexes official Manim documentation
- BM25 ranking for efficient retrieval (40% error reduction vs non-RAG)
- Retrieved examples augment LLM context for accurate code generation

**Multi-Provider Fallback:**

- Image generation: Pollinations → Stable Diffusion → Placeholder
- Code generation: Gemini 2.5 → Gemini 1.5 → Groq Llama
- TTS: Sarvam AI → Amazon Polly

**Performance Optimization:**

- Parallel image generation (up to 3 concurrent)
- MD5-based caching for repeated prompts
- ECS auto-scaling based on queue depth
- CloudFront CDN for global low-latency delivery

---

## 4. Feature 2: Interactive Code Player (CodeVid)

### 4.1 Overview

A revolutionary video player that synchronizes audio narration with live code editing, allowing learners to pause, modify code, execute it, and see results—all within the video experience.

### 4.2 Master Clock Architecture

```
╔═══════════════════════════════════════════════════════════════════════════╗
║           FEATURE 2: CODEVID INTERACTIVE CODE PLAYER                      ║
║              Master Clock Synchronization Architecture                    ║
╚═══════════════════════════════════════════════════════════════════════════╝

┌───────────────────────────────────────────────────────────────────────────┐
│                      PLAYER STATE MACHINE                                 │
├───────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│   ┌──────────┐         ┌──────────┐         ┌──────────┐                 │
│   │ PLAYING  │────────▶│  PAUSED  │────────▶│  RESUME  │                 │
│   │          │  pause  │          │  resume │          │                 │
│   │ readOnly │         │ readOnly │         │ readOnly │                 │
│   │  = true  │         │  = false │         │  = true  │                 │
│   └────┬─────┘         └────┬─────┘         └────┬─────┘                 │
│        │                    │                    │                       │
│        │                    │                    └──────────┐             │
│        └────────────────────┴───────────────────────────────┘             │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘

┌───────────────────────────────────────────────────────────────────────────┐
│                    THREE-LAYER ARCHITECTURE                               │
├───────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  LAYER 1: AUDIO ELEMENT (Master Clock)                          │    │
│   │  ═══════════════════════════════════════════════════════════    │    │
│   │                                                                 │    │
│   │  <audio src="narration.mp3" />                                  │    │
│   │                                                                 │    │
│   │  • Source of truth for all timing                               │    │
│   │  • currentTime property drives everything                       │    │
│   │  • Events: play, pause, timeupdate, seeked                      │    │
│   │                                                                 │    │
│   │  const masterClock = audioElement.currentTime;                  │    │
│   │                                                                 │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│                                    │                                      │
│                                    │ drives                               │
│                                    ▼                                      │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  LAYER 2: MONACO EDITOR (Code Display)                          │    │
│   │  ═══════════════════════════════════════════════════════════    │    │
│   │                                                                 │    │
│   │  • Programmatically updated based on timestamp                  │    │
│   │  • readOnly: true during PLAYING state                          │    │
│   │  • readOnly: false during PAUSED state                          │    │
│   │                                                                 │    │
│   │  Event Engine consumes timestamped events:                      │    │
│   │                                                                 │    │
│   │  events = [                                                     │    │
│   │    { timestamp: 1.5, type: "CODE_TYPE",                         │    │
│   │      payload: { line: 1, text: "from manim import *" } },       │    │
│   │    { timestamp: 3.2, type: "CODE_TYPE",                         │    │
│   │      payload: { line: 3, text: "class MyScene(Scene):" } },     │    │
│   │    { timestamp: 5.8, type: "CURSOR_MOVE",                       │    │
│   │      payload: { line: 5, column: 10 } },                        │    │
│   │    ...                                                           │    │
│   │  ]                                                               │    │
│   │                                                                 │    │
│   │  When currentTime >= event.timestamp:                           │    │
│   │    editor.setValue() or editor.insertText()                     │    │
│   │                                                                 │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│                                    │                                      │
│                                    │ synchronized with                    │
│                                    ▼                                      │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  LAYER 3: OVERLAYS (Visual Elements)                            │    │
│   │  ═══════════════════════════════════════════════════════════    │    │
│   │                                                                 │    │
│   │  • Images, diagrams, webcam feeds                               │    │
│   │  • Show/hide based on timestamp                                 │    │
│   │  • Positioned using percentage-based coordinates                │    │
│   │                                                                 │    │
│   │  overlayEvents = [                                              │    │
│   │    { timestamp: 10.0, type: "OVERLAY_SHOW",                     │    │
│   │      payload: { id: "diagram1", x: 60, y: 10,                   │    │
│   │                 width: 35, height: 40,                          │    │
│   │                 src: "/images/neural-net.png" } },              │    │
│   │    { timestamp: 25.0, type: "OVERLAY_HIDE",                     │    │
│   │      payload: { id: "diagram1" } },                             │    │
│   │    ...                                                           │    │
│   │  ]                                                               │    │
│   │                                                                 │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
```

```
┌───────────────────────────────────────────────────────────────────────────┐
│                      PLAYBACK FLOW DIAGRAM                                │
├───────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│   USER CLICKS PLAY                                                        │
│        │                                                                  │
│        ▼                                                                  │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  1. Audio starts playing                                        │    │
│   │     audioElement.play()                                         │    │
│   │     State: PLAYING                                              │    │
│   │     Monaco readOnly: true                                       │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│        │                                                                  │
│        ▼                                                                  │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  2. Event Engine processes events                               │    │
│   │     setInterval(() => {                                         │    │
│   │       const currentTime = audioElement.currentTime;             │    │
│   │       const dueEvents = events.filter(e =>                      │    │
│   │         e.timestamp <= currentTime && !e.processed);            │    │
│   │       dueEvents.forEach(applyEvent);                            │    │
│   │     }, 50); // Check every 50ms                                 │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│        │                                                                  │
│        ▼                                                                  │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  3. Code updates appear in Monaco Editor                        │    │
│   │     • Line-by-line typing animation                             │    │
│   │     • Cursor movements                                          │    │
│   │     • Syntax highlighting updates                               │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│        │                                                                  │
│        ▼                                                                  │
│   USER CLICKS PAUSE                                                       │
│        │                                                                  │
│        ▼                                                                  │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  4. Audio pauses                                                │    │
│   │     audioElement.pause()                                        │    │
│   │     State: PAUSED                                               │    │
│   │     Monaco readOnly: false                                      │    │
│   │     Event Engine: stopped                                       │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│        │                                                                  │
│        ▼                                                                  │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  5. User edits code freely                                      │    │
│   │     • Full Monaco Editor capabilities                           │    │
│   │     • IntelliSense, autocomplete                                │    │
│   │     • Syntax validation                                         │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│        │                                                                  │
│        ▼                                                                  │
│   USER CLICKS "RUN CODE"                                                  │
│        │                                                                  │
│        ▼                                                                  │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  6. Execute code via Piston API                                 │    │
│   │     POST /api/v2/execute                                        │    │
│   │     {                                                           │    │
│   │       "language": "python",                                     │    │
│   │       "version": "3.10",                                        │    │
│   │       "files": [{ "content": userCode }]                        │    │
│   │     }                                                           │    │
│   │                                                                 │    │
│   │     Response: { "stdout": "...", "stderr": "..." }              │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│        │                                                                  │
│        ▼                                                                  │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  7. Display output in panel                                     │    │
│   │     ┌─────────────────────────────────────────────────────┐    │    │
│   │     │  OUTPUT:                                            │    │    │
│   │     │  > Circle created successfully                      │    │    │
│   │     │  > Animation rendered                               │    │    │
│   │     │  > Execution time: 0.42s                            │    │    │
│   │     └─────────────────────────────────────────────────────┘    │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│        │                                                                  │
│        ▼                                                                  │
│   USER CLICKS RESUME                                                      │
│        │                                                                  │
│        ▼                                                                  │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  8. Calculate correct code state at timestamp                   │    │
│   │     const currentTime = audioElement.currentTime;               │    │
│   │     const stateAtTime = calculateCodeState(events, currentTime);│    │
│   │                                                                 │    │
│   │  9. Force-reset editor to that state                            │    │
│   │     editor.setValue(stateAtTime);                               │    │
│   │     // User changes are discarded                               │    │
│   │                                                                 │    │
│   │  10. Resume playback                                            │    │
│   │      audioElement.play()                                        │    │
│   │      State: PLAYING                                             │    │
│   │      Monaco readOnly: true                                      │    │
│   │      Event Engine: restarted                                    │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
```

```
┌───────────────────────────────────────────────────────────────────────────┐
│                      CODE EXECUTION ENGINE                                │
├───────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  Piston API (Self-Hosted on AWS ECS EC2)                        │    │
│   ├─────────────────────────────────────────────────────────────────┤    │
│   │                                                                 │    │
│   │  Why EC2 instead of Fargate?                                    │    │
│   │  • Piston uses Docker-in-Docker for sandboxing                  │    │
│   │  • Requires privileged container mode                           │    │
│   │  • Fargate doesn't support privileged containers                │    │
│   │  • EC2 provides full control over instance configuration        │    │
│   │                                                                 │    │
│   │  Supported Languages (20+):                                     │    │
│   │  ┌──────────────┬──────────────┬──────────────┬──────────────┐ │    │
│   │  │ JavaScript   │ Python       │ TypeScript   │ Java         │ │    │
│   │  │ C            │ C++          │ Rust         │ Go           │ │    │
│   │  │ Ruby         │ PHP          │ Swift        │ Kotlin       │ │    │
│   │  │ C#           │ Bash         │ SQL          │ R            │ │    │
│   │  │ Lua          │ Haskell      │ Scala        │ Perl         │ │    │
│   │  └──────────────┴──────────────┴──────────────┴──────────────┘ │    │
│   │                                                                 │    │
│   │  Resource Limits:                                               │    │
│   │  • CPU: 1 core per execution                                    │    │
│   │  • Memory: 256 MB                                               │    │
│   │  • Timeout: 5 seconds                                           │    │
│   │  • Network: Disabled (air-gapped)                               │    │
│   │                                                                 │    │
│   │  Security:                                                      │    │
│   │  • Isolated container per request                               │    │
│   │  • No filesystem persistence                                    │    │
│   │  • No access to host system                                     │    │
│   │  • Automatic cleanup after execution                            │    │
│   │                                                                 │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│                                                                           │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  Fallback Executors (Browser-Based)                             │    │
│   ├─────────────────────────────────────────────────────────────────┤    │
│   │                                                                 │    │
│   │  JavaScript: Sandpack (CodeSandbox)                             │    │
│   │  • In-browser execution                                         │    │
│   │  • React, Vue, Angular support                                  │    │
│   │  • NPM package installation                                     │    │
│   │                                                                 │    │
│   │  Python: Pyodide (WebAssembly)                                  │    │
│   │  • Python 3.10 in browser                                       │    │
│   │  • NumPy, Pandas, Matplotlib support                            │    │
│   │  • No server required                                           │    │
│   │                                                                 │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
```

```
┌───────────────────────────────────────────────────────────────────────────┐
│                      .CODEVID FILE FORMAT                                 │
├───────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│   {                                                                       │
│     "version": 1,                                                         │
│     "recording": {                                                        │
│       "audioUrl": "https://cdn.example.com/audio/lesson1.mp3",           │
│       "duration": 180.5,                                                  │
│       "events": [                                                         │
│         {                                                                 │
│           "timestamp": 1.5,                                               │
│           "type": "CODE_TYPE",                                            │
│           "payload": {                                                    │
│             "line": 1,                                                    │
│             "text": "from manim import *"                                 │
│           }                                                               │
│         },                                                                │
│         {                                                                 │
│           "timestamp": 3.2,                                               │
│           "type": "CODE_TYPE",                                            │
│           "payload": {                                                    │
│             "line": 3,                                                    │
│             "text": "class MyScene(Scene):"                               │
│           }                                                               │
│         },                                                                │
│         {                                                                 │
│           "timestamp": 5.8,                                               │
│           "type": "CURSOR_MOVE",                                          │
│           "payload": {                                                    │
│             "line": 5,                                                    │
│             "column": 10                                                  │
│           }                                                               │
│         },                                                                │
│         {                                                                 │
│           "timestamp": 10.0,                                              │
│           "type": "OVERLAY_SHOW",                                         │
│           "payload": {                                                    │
│             "id": "diagram1",                                             │
│             "x": 60,                                                      │
│             "y": 10,                                                      │
│             "width": 35,                                                  │
│             "height": 40,                                                 │
│             "src": "/images/neural-net.png"                               │
│           }                                                               │
│         },                                                                │
│         {                                                                 │
│           "timestamp": 25.0,                                              │
│           "type": "OVERLAY_HIDE",                                         │
│           "payload": {                                                    │
│             "id": "diagram1"                                              │
│           }                                                               │
│         }                                                                 │
│       ]                                                                   │
│     },                                                                    │
│     "metadata": {                                                         │
│       "title": "Introduction to Manim",                                   │
│       "language": "python",                                               │
│       "author": "Eklavya.ai",                                             │
│       "created": "2026-01-24T10:30:00Z"                                  │
│     }                                                                     │
│   }                                                                       │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
```

### 4.3 Key Technical Details

**Master Clock Pattern:**

- Audio element is the single source of truth for timing
- All visual updates are driven by `audioElement.currentTime`
- No separate video file needed—audio + events = complete experience

**State Management:**

- PLAYING: readOnly=true, Event Engine active, user cannot edit
- PAUSED: readOnly=false, Event Engine stopped, user can edit freely
- RESUME: Force-reset to timestamp state, discard user changes

**Event Types:**

- CODE_TYPE: Insert text at specific line
- CURSOR_MOVE: Move cursor to position
- OVERLAY_SHOW: Display image/diagram at coordinates
- OVERLAY_HIDE: Remove overlay from view

**Performance:**

- Event processing: 50ms interval (20 FPS)
- Code execution timeout: 5 seconds
- Piston API response time: <2 seconds average

---

## 5. Feature 3: PDF to Video Tutorials

### 5.1 Overview

Upload research papers, documentation, or textbooks and automatically generate short tutorial videos for each section, using extracted images and content directly from the PDF.

### 5.2 Architecture Diagram

```
╔═══════════════════════════════════════════════════════════════════════════╗
║              FEATURE 3: PDF TO VIDEO TUTORIALS                            ║
║           Batch Generation from Document Sections                         ║
╚═══════════════════════════════════════════════════════════════════════════╝

┌───────────────────────────────────────────────────────────────────────────┐
│                          USER UPLOAD                                      │
├───────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│   • PDF Document (research paper, textbook, documentation)                │
│   • Language selection for narration                                      │
│   • Mood/style preference                                                 │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌───────────────────────────────────────────────────────────────────────────┐
│  STAGE 1: PDF PARSING & EXTRACTION                                        │
├───────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  ECS Fargate Worker: PDF Processor                              │    │
│   ├─────────────────────────────────────────────────────────────────┤    │
│   │                                                                 │    │
│   │  PyMuPDF (fitz) Library:                                        │    │
│   │                                                                 │    │
│   │  1. Text Extraction                                             │    │
│   │     • Page-by-page text content                                 │    │
│   │     • Preserve formatting and structure                         │    │
│   │     • Identify headings, paragraphs, lists                      │    │
│   │                                                                 │    │
│   │  2. Image Extraction                                            │    │
│   │     • Extract embedded images                                   │    │
│   │     • Save as PNG files                                         │    │
│   │     • Maintain image quality                                    │    │
│   │     • Associate with page numbers                               │    │
│   │                                                                 │    │
│   │  3. Table Extraction                                            │    │
│   │     • Detect table structures                                   │    │
│   │     • Convert to structured data                                │    │
│   │     • Render as images if needed                                │    │
│   │                                                                 │    │
│   │  4. Metadata Extraction                                         │    │
│   │     • Title, author, date                                       │    │
│   │     • Table of contents                                         │    │
│   │     • Section hierarchy                                         │    │
│   │                                                                 │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│                                                                           │
│   Output:                                                                 │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  {                                                              │    │
│   │    "pages": [                                                   │    │
│   │      {                                                          │    │
│   │        "page_num": 1,                                           │    │
│   │        "text": "Introduction to Neural Networks...",            │    │
│   │        "images": ["page1_img1.png", "page1_img2.png"],          │    │
│   │        "tables": [...]                                          │    │
│   │      },                                                          │    │
│   │      ...                                                         │    │
│   │    ],                                                            │    │
│   │    "metadata": {...}                                            │    │
│   │  }                                                              │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌───────────────────────────────────────────────────────────────────────────┐
│  STAGE 2: CONTENT ANALYSIS & SECTION DETECTION                            │
├───────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  AWS Bedrock Claude 3.5                                         │    │
│   ├─────────────────────────────────────────────────────────────────┤    │
│   │                                                                 │    │
│   │  Prompt:                                                        │    │
│   │  "Analyze this PDF content and identify logical sections        │    │
│   │   suitable for 1-3 minute tutorial videos. For each section:    │    │
│   │   - Provide a clear title                                       │    │
│   │   - Summarize key concepts                                      │    │
│   │   - Identify relevant images from the PDF                       │    │
│   │   - Estimate video duration                                     │    │
│   │   - Suggest visual elements needed"                             │    │
│   │                                                                 │    │
│   │  Analysis Output:                                               │    │
│   │  ┌──────────────────────────────────────────────────────────┐  │    │
│   │  │  Section 1: "Introduction to Neural Networks"           │  │    │
│   │  │  • Key concepts: neurons, layers, activation functions  │  │    │
│   │  │  • PDF images: page1_img1.png (network diagram)         │  │    │
│   │  │  • Duration: 2 minutes                                  │  │    │
│   │  │  • Additional visuals: animated neuron firing          │  │    │
│   │  │                                                          │  │    │
│   │  │  Section 2: "Backpropagation Algorithm"                 │  │    │
│   │  │  • Key concepts: gradient descent, chain rule           │  │    │
│   │  │  • PDF images: page3_img2.png (gradient flow)           │  │    │
│   │  │  • Duration: 3 minutes                                  │  │    │
│   │  │  • Additional visuals: animated gradient descent        │  │    │
│   │  │                                                          │  │    │
│   │  │  Section 3: "Training Process"                          │  │    │
│   │  │  • Key concepts: epochs, batches, loss function         │  │    │
│   │  │  • PDF images: page5_img1.png (training curve)          │  │    │
│   │  │  • Duration: 2.5 minutes                                │  │    │
│   │  │  • Additional visuals: loss curve animation             │  │    │
│   │  └──────────────────────────────────────────────────────────┘  │    │
│   │                                                                 │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌───────────────────────────────────────────────────────────────────────────┐
│  STAGE 3: USER SECTION SELECTION                                          │
├───────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  Preview Interface                                              │    │
│   ├─────────────────────────────────────────────────────────────────┤    │
│   │                                                                 │    │
│   │  ☑ Section 1: Introduction to Neural Networks (2 min)          │    │
│   │     Preview: "This section covers the basics of..."             │    │
│   │     Images: 1 diagram from PDF                                  │    │
│   │                                                                 │    │
│   │  ☑ Section 2: Backpropagation Algorithm (3 min)                │    │
│   │     Preview: "Learn how neural networks learn..."               │    │
│   │     Images: 2 diagrams from PDF                                 │    │
│   │                                                                 │    │
│   │  ☐ Section 3: Training Process (2.5 min)                       │    │
│   │     Preview: "Understand the training workflow..."              │    │
│   │     Images: 1 chart from PDF                                    │    │
│   │                                                                 │    │
│   │  [Generate Selected Videos]                                     │    │
│   │                                                                 │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌───────────────────────────────────────────────────────────────────────────┐
│  STAGE 4: BATCH VIDEO GENERATION                                          │
├───────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│   For each selected section, run Feature 1 pipeline:                     │
│                                                                           │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  Section 1 → Video Generation Pipeline                          │    │
│   │  ────────────────────────────────────                           │    │
│   │  Input:                                                         │    │
│   │  • Section summary as prompt                                    │    │
│   │  • PDF-extracted images (prioritized)                           │    │
│   │  • User-selected language & mood                                │    │
│   │                                                                 │    │
│   │  Process:                                                       │    │
│   │  1. Scene Planning (use section content)                        │    │
│   │  2. Asset Generation (use PDF images + generate if needed)      │    │
│   │  3. Code Generation (RAG-enhanced)                              │    │
│   │  4. TTS Generation (section narration)                          │    │
│   │  5. Manim Rendering                                             │    │
│   │  6. Post-Processing                                             │    │
│   │                                                                 │    │
│   │  Output: section1_video.mp4                                     │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│                                                                           │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  Section 2 → Video Generation Pipeline                          │    │
│   │  Output: section2_video.mp4                                     │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│                                                                           │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  Section 3 → Video Generation Pipeline                          │    │
│   │  Output: section3_video.mp4                                     │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│                                                                           │
│   Parallel Processing:                                                   │
│   • Up to 3 videos generated concurrently                                │
│   • Independent ECS Fargate workers                                      │
│   • Progress tracking per video                                          │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌───────────────────────────────────────────────────────────────────────────┐
│                         FINAL OUTPUT                                      │
├───────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│   Video Series:                                                           │
│   • section1_intro_neural_networks.mp4 (2:00)                            │
│   • section2_backpropagation.mp4 (3:00)                                  │
│   • section3_training_process.mp4 (2:30)                                 │
│                                                                           │
│   Playlist:                                                               │
│   • Auto-generated playlist with all sections                            │
│   • Sequential playback                                                  │
│   • Jump to specific section                                             │
│                                                                           │
│   Downloads:                                                              │
│   • Individual MP4 files                                                 │
│   • Combined ZIP archive                                                 │
│   • Original PDF with timestamps                                         │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
```

### 5.3 Key Technical Details

**PDF Processing:**

- PyMuPDF for robust extraction (text, images, tables)
- Maintains original image quality from PDF
- Preserves document structure and hierarchy

**Content Analysis:**

- Claude 3.5 analyzes content for logical sections
- Identifies key concepts per section
- Maps PDF images to relevant sections
- Estimates appropriate video duration (1-3 minutes)

**Asset Prioritization:**

- PDF-extracted images used first (authentic to source)
- Additional images generated only if needed
- Icons and diagrams supplement PDF content

**Batch Processing:**

- Up to 3 videos generated in parallel
- Independent progress tracking per video
- Efficient resource utilization with ECS auto-scaling

---

## 6. Feature 4: Live AI Screenshare Tutoring

### 6.1 Overview

Share your screen or webcam with an AI tutor that can see what you're working on, understand your questions in real-time, and provide guidance in your preferred language with voice responses.

### 6.2 Architecture Diagram

```
╔═══════════════════════════════════════════════════════════════════════════╗
║           FEATURE 4: LIVE AI SCREENSHARE TUTORING                         ║
║        WebRTC + Gemini Live + Multilingual Voice Response                 ║
╚═══════════════════════════════════════════════════════════════════════════╝

┌───────────────────────────────────────────────────────────────────────────┐
│                          USER BROWSER                                     │
├───────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  Capture Sources                                                │    │
│   ├─────────────────────────────────────────────────────────────────┤    │
│   │                                                                 │    │
│   │  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐          │    │
│   │  │ Screen Share │  │   Webcam     │  │ Microphone   │          │    │
│   │  │              │  │              │  │              │          │    │
│   │  │ navigator.   │  │ getUserMedia │  │ Audio Input  │          │    │
│   │  │ getDisplay   │  │ (video)      │  │              │          │    │
│   │  │ Media()      │  │              │  │              │          │    │
│   │  └──────────────┘  └──────────────┘  └──────────────┘          │    │
│   │                                                                 │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│                                    │                                      │
│                                    │ WebRTC Stream                        │
│                                    ▼                                      │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  Chat Interface                                                 │    │
│   ├─────────────────────────────────────────────────────────────────┤    │
│   │                                                                 │    │
│   │  User: "How do I fix this error in my code?"                    │    │
│   │  AI: "I can see you have a syntax error on line 15..."          │    │
│   │                                                                 │    │
│   │  [🎤 Voice Input]  [⌨️ Text Input]  [🔊 Voice Response]          │    │
│   │                                                                 │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
                                    │
                                    │ WebSocket + Media Stream
                                    ▼
┌───────────────────────────────────────────────────────────────────────────┐
│                      AWS INFRASTRUCTURE                                   │
├───────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  Amazon Kinesis Video Streams                                   │    │
│   ├─────────────────────────────────────────────────────────────────┤    │
│   │                                                                 │    │
│   │  • Ingests WebRTC video/screen stream                           │    │
│   │  • Buffers frames for processing                                │    │
│   │  • Handles network jitter and packet loss                       │    │
│   │  • Provides HLS/DASH playback URLs                              │    │
│   │                                                                 │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│                                    │                                      │
│                                    │ Frame extraction                     │
│                                    ▼                                      │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  Lambda: Frame Processor                                        │    │
│   ├─────────────────────────────────────────────────────────────────┤    │
│   │                                                                 │    │
│   │  • Extract frames at 1 FPS (real-time analysis)                 │    │
│   │  • Compress images for API transmission                         │    │
│   │  • Maintain session context                                     │    │
│   │                                                                 │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
                                    │
                                    │ Image + Audio
                                    ▼
┌───────────────────────────────────────────────────────────────────────────┐
│                      AI PROCESSING LAYER                                  │
├───────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  Audio Transcription                                            │    │
│   ├─────────────────────────────────────────────────────────────────┤    │
│   │                                                                 │    │
│   │  Amazon Transcribe (Real-time)                                  │    │
│   │  • Streaming audio transcription                                │    │
│   │  • Multi-language support                                       │    │
│   │  • Punctuation and formatting                                   │    │
│   │  • Latency: <500ms                                              │    │
│   │                                                                 │    │
│   │  Output: "How do I fix this error in my code?"                  │    │
│   │                                                                 │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│                                    │                                      │
│                                    │ Text + Image                         │
│                                    ▼                                      │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  Gemini Live API (Multimodal)                                   │    │
│   ├─────────────────────────────────────────────────────────────────┤    │
│   │                                                                 │    │
│   │  Input:                                                         │    │
│   │  • Screen/webcam frame (image)                                  │    │
│   │  • User question (text)                                         │    │
│   │  • Conversation history (context)                               │    │
│   │                                                                 │    │
│   │  Processing:                                                    │    │
│   │  1. Visual analysis of screen content                           │    │
│   │     • Identify code, errors, UI elements                        │    │
│   │     • Detect diagrams, charts, text                             │    │
│   │     • Understand context from visuals                           │    │
│   │                                                                 │    │
│   │  2. Combine with user question                                  │    │
│   │     • Understand what user is asking about                      │    │
│   │     • Reference specific elements in screen                     │    │
│   │     • Provide contextual guidance                               │    │
│   │                                                                 │    │
│   │  3. Generate response                                           │    │
│   │     • Explain the issue                                         │    │
│   │     • Provide step-by-step solution                             │    │
│   │     • Reference specific line numbers/elements                  │    │
│   │                                                                 │    │
│   │  Output: "I can see you have a syntax error on line 15.         │    │
│   │           You're missing a closing parenthesis. Try adding      │    │
│   │           ')' at the end of that line."                         │    │
│   │                                                                 │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│                                    │                                      │
│                                    │ Text Response                        │
│                                    ▼                                      │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  Text-to-Speech (Voice Response)                                │    │
│   ├─────────────────────────────────────────────────────────────────┤    │
│   │                                                                 │    │
│   │  Sarvam AI TTS (Primary)                                        │    │
│   │  • User-selected language (12+ options)                         │    │
│   │  • Natural neural voices                                        │    │
│   │  • Low latency (<1 second)                                      │    │
│   │                                                                 │    │
│   │  Fallback: Amazon Polly                                         │    │
│   │  • Additional language support                                  │    │
│   │  • Reliable backup service                                      │    │
│   │                                                                 │    │
│   │  Output: audio_response.mp3                                     │    │
│   │                                                                 │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
                                    │
                                    │ Text + Audio
                                    ▼
┌───────────────────────────────────────────────────────────────────────────┐
│                          USER BROWSER                                     │
├───────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│   • Display text response in chat                                        │
│   • Play audio response through speakers                                 │
│   • Maintain conversation history                                        │
│   • Continue streaming screen/webcam                                     │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘

┌───────────────────────────────────────────────────────────────────────────┐
│                      SESSION MANAGEMENT                                   │
├───────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│   DynamoDB: Sessions Table                                               │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  session_id (PK)  │  user_id  │  status  │  created_at         │    │
│   │  stream_arn       │  language │  history │  last_activity      │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│                                                                           │
│   Conversation History:                                                  │
│   • Stored in DynamoDB for context                                       │
│   • Retrieved for each new question                                      │
│   • Enables multi-turn conversations                                     │
│   • Maintains context across session                                     │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
```

### 6.3 Key Technical Details

**WebRTC Integration:**

- Browser captures screen/webcam via `navigator.getDisplayMedia()`
- Audio captured via `getUserMedia()` for microphone input
- Streams sent to Amazon Kinesis Video Streams for processing

**Real-Time Processing:**

- Frame extraction at 1 FPS (balance between latency and cost)
- Amazon Transcribe for streaming audio-to-text (<500ms latency)
- Gemini Live API for multimodal understanding (image + text)

**Multimodal AI:**

- Gemini Live analyzes screen content visually
- Understands code, errors, UI elements, diagrams
- Combines visual context with user questions
- Provides specific, actionable guidance

**Multilingual Support:**

- User selects preferred language at session start
- Transcription in user's language
- AI response in user's language
- TTS voice synthesis in user's language (12+ options)

**Performance:**

- End-to-end latency: <3 seconds (question → voice response)
- Concurrent sessions: Scales with Lambda + Kinesis
- Session persistence: DynamoDB stores conversation history

---

## 7. Feature 5: Graph RAG Document Analyzer

### 7.1 Overview

Upload research papers and ask questions that return exact page and line references, powered by graph-based retrieval that understands document structure and relationships.

### 7.2 Architecture Diagram

```
╔═══════════════════════════════════════════════════════════════════════════╗
║            FEATURE 5: GRAPH RAG DOCUMENT ANALYZER                         ║
║         Precise Line-Level References with Graph Traversal                ║
╚═══════════════════════════════════════════════════════════════════════════╝

┌───────────────────────────────────────────────────────────────────────────┐
│                      DOCUMENT INGESTION                                   │
├───────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│   User uploads PDF → S3 Bucket → Triggers Lambda                         │
│                                                                           │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  Lambda: Document Processor                                     │    │
│   ├─────────────────────────────────────────────────────────────────┤    │
│   │                                                                 │    │
│   │  1. Parse PDF with PyMuPDF                                      │    │
│   │     • Extract text with line numbers                            │    │
│   │     • Preserve page structure                                   │    │
│   │     • Identify sections, paragraphs, sentences                  │    │
│   │                                                                 │    │
│   │  2. Build Document Graph                                        │    │
│   │     ┌─────────────────────────────────────────────────────┐    │    │
│   │     │  NODES:                                             │    │    │
│   │     │  • Document (root)                                  │    │    │
│   │     │  • Section (heading-based)                          │    │    │
│   │     │  • Paragraph (text block)                           │    │    │
│   │     │  • Sentence (individual sentence)                   │    │    │
│   │     │  • KeyTerm (important concepts)                     │    │    │
│   │     │                                                      │    │    │
│   │     │  EDGES:                                             │    │    │
│   │     │  • CONTAINS (hierarchical)                          │    │    │
│   │     │  • FOLLOWS (sequential)                             │    │    │
│   │     │  • REFERENCES (citations, mentions)                 │    │    │
│   │     │  • RELATES_TO (semantic similarity)                 │    │    │
│   │     └─────────────────────────────────────────────────────┘    │    │
│   │                                                                 │    │
│   │  3. Generate Embeddings                                         │    │
│   │     • Amazon Titan Embeddings                                   │    │
│   │     • Embed paragraphs and sentences                            │    │
│   │     • Store with metadata (page, line, section)                 │    │
│   │                                                                 │    │
│   │  4. Create BM25 Index                                           │    │
│   │     • Keyword-based search index                                │    │
│   │     • Efficient for exact term matching                         │    │
│   │     • Complements semantic search                               │    │
│   │                                                                 │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
                                    │
                                    │ Store in
                                    ▼
┌───────────────────────────────────────────────────────────────────────────┐
│                      STORAGE LAYER                                        │
├───────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  AWS Bedrock Knowledge Base                                     │    │
│   ├─────────────────────────────────────────────────────────────────┤    │
│   │                                                                 │    │
│   │  Vector Store (OpenSearch):                                     │    │
│   │  • Embeddings with metadata                                     │    │
│   │  • Fast similarity search                                       │    │
│   │  • Metadata: {page, line_start, line_end, section}              │    │
│   │                                                                 │    │
│   │  BM25 Index:                                                    │    │
│   │  • Keyword-based retrieval                                      │    │
│   │  • Exact term matching                                          │    │
│   │  • Complements semantic search                                  │    │
│   │                                                                 │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│                                                                           │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  DynamoDB: Graph Database                                       │    │
│   ├─────────────────────────────────────────────────────────────────┤    │
│   │                                                                 │    │
│   │  Nodes Table:                                                   │    │
│   │  • node_id (PK), type, content, metadata                        │    │
│   │                                                                 │    │
│   │  Edges Table:                                                   │    │
│   │  • edge_id (PK), source_id, target_id, type, weight            │    │
│   │                                                                 │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
                                    │
                                    │ Query
                                    ▼
┌───────────────────────────────────────────────────────────────────────────┐
│                      QUERY PROCESSING                                     │
├───────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│   User Question: "What is the backpropagation algorithm?"                │
│                                                                           │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  Lambda: Query Processor                                        │    │
│   ├─────────────────────────────────────────────────────────────────┤    │
│   │                                                                 │    │
│   │  STEP 1: Hybrid Search                                          │    │
│   │  ┌──────────────────────────────────────────────────────────┐  │    │
│   │  │  Semantic Search (Vector)                                │  │    │
│   │  │  • Embed query with Titan                                │  │    │
│   │  │  • Find similar passages                                 │  │    │
│   │  │  • Top 10 results                                        │  │    │
│   │  │                                                           │  │    │
│   │  │  Keyword Search (BM25)                                   │  │    │
│   │  │  • Extract key terms                                     │  │    │
│   │  │  • Match exact phrases                                   │  │    │
│   │  │  • Top 10 results                                        │  │    │
│   │  │                                                           │  │    │
│   │  │  Fusion:                                                 │  │    │
│   │  │  • Combine and re-rank results                           │  │    │
│   │  │  • Reciprocal Rank Fusion (RRF)                          │  │    │
│   │  │  • Top 5 final candidates                                │  │    │
│   │  └──────────────────────────────────────────────────────────┘  │    │
│   │                                                                 │    │
│   │  STEP 2: Graph Traversal                                        │    │
│   │  ┌──────────────────────────────────────────────────────────┐  │    │
│   │  │  For each candidate passage:                             │  │    │
│   │  │                                                           │  │    │
│   │  │  1. Find node in graph                                   │  │    │
│   │  │  2. Traverse CONTAINS edges (get parent section)         │  │    │
│   │  │  3. Traverse FOLLOWS edges (get context before/after)    │  │    │
│   │  │  4. Traverse REFERENCES edges (get related passages)     │  │    │
│   │  │  5. Traverse RELATES_TO edges (get similar concepts)     │  │    │
│   │  │                                                           │  │    │
│   │  │  Result: Enriched context with relationships             │  │    │
│   │  └──────────────────────────────────────────────────────────┘  │    │
│   │                                                                 │    │
│   │  STEP 3: Generate Response                                      │    │
│   │  ┌──────────────────────────────────────────────────────────┐  │    │
│   │  │  AWS Bedrock Claude 3.5                                  │  │    │
│   │  │                                                           │  │    │
│   │  │  Input:                                                  │  │    │
│   │  │  • User question                                         │  │    │
│   │  │  • Retrieved passages with metadata                      │  │    │
│   │  │  • Graph context (related passages)                      │  │    │
│   │  │                                                           │  │    │
│   │  │  Prompt:                                                 │  │    │
│   │  │  "Answer the question using ONLY the provided passages.  │  │    │
│   │  │   For each statement, cite the exact source with:        │  │    │
│   │  │   - Page number                                          │  │    │
│   │  │   - Line numbers (start-end)                             │  │    │
│   │  │   Format: [Page X, Lines Y-Z]"                           │  │    │
│   │  │                                                           │  │    │
│   │  │  Output:                                                 │  │    │
│   │  │  "Backpropagation is an algorithm for training neural   │  │    │
│   │  │   networks by computing gradients [Page 5, Lines 123-   │  │    │
│   │  │   128]. It uses the chain rule to propagate errors      │  │    │
│   │  │   backward through the network [Page 5, Lines 145-152]. │  │    │
│   │  │   The algorithm adjusts weights to minimize loss        │  │    │
│   │  │   [Page 6, Lines 23-29]."                                │  │    │
│   │  └──────────────────────────────────────────────────────────┘  │    │
│   │                                                                 │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
                                    │
                                    │ Response
                                    ▼
┌───────────────────────────────────────────────────────────────────────────┐
│                      USER INTERFACE                                       │
├───────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│   ┌─────────────────────────────────────────────────────────────────┐    │
│   │  Split View                                                     │    │
│   ├─────────────────────────────────────────────────────────────────┤    │
│   │                                                                 │    │
│   │  LEFT: PDF Viewer                  RIGHT: Q&A Interface         │    │
│   │  ┌──────────────────────┐          ┌──────────────────────┐    │    │
│   │  │                      │          │ Q: What is back-     │    │    │
│   │  │  [PDF Document]      │          │    propagation?      │    │    │
│   │  │                      │          │                      │    │    │
│   │  │  Page 5              │          │ A: Backpropagation   │    │    │
│   │  │  ───────             │          │    is an algorithm   │    │    │
│   │  │  Line 123: ▶ Back-   │◀─────────│    for training...   │    │    │
│   │  │  propagation is an   │ Highlight│    [Page 5, Lines    │    │    │
│   │  │  algorithm for...    │          │     123-128] ◀───────┼────┐    │
│   │  │                      │          │                      │    │    │
│   │  │  Line 145: ▶ It uses │          │    It uses the chain │    │    │
│   │  │  the chain rule...   │          │    rule... [Page 5,  │    │    │
│   │  │                      │          │    Lines 145-152]    │    │    │
│   │  └──────────────────────┘          └──────────────────────┘    │    │
│   │                                                                 │    │
│   │  Features:                                                      │    │
│   │  • Click citation → PDF scrolls to exact line                   │    │
│   │  • Highlighted passages in PDF                                  │    │
│   │  • Related passages shown in sidebar                            │    │
│   │  • Export Q&A with citations                                    │    │
│   │                                                                 │    │
│   └─────────────────────────────────────────────────────────────────┘    │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
```

### 7.3 Key Technical Details

**Graph Structure:**

- Hierarchical: Document → Section → Paragraph → Sentence
- Sequential: FOLLOWS edges connect adjacent content
- Referential: REFERENCES edges link citations and mentions
- Semantic: RELATES_TO edges connect similar concepts

**Hybrid Search:**

- Semantic search (vector embeddings) for conceptual matching
- BM25 keyword search for exact term matching
- Reciprocal Rank Fusion combines both approaches
- 40% better accuracy than vector-only search

**Line-Level Precision:**

- Every passage stored with page and line metadata
- Graph traversal provides surrounding context
- Citations include exact line ranges
- PDF viewer scrolls to and highlights referenced lines

**Hallucination Prevention:**

- LLM instructed to use ONLY provided passages
- Every statement must include citation
- Graph context reduces need for inference
- Exact source references enable verification

---

## 8. Cross-Cutting Concerns

### 8.1 Authentication & Authorization

- User authentication via JWT tokens
- Role-based access control (RBAC)
- API key management in AWS Secrets Manager
- Session management in DynamoDB

### 8.2 Monitoring & Logging

- CloudWatch Logs for all Lambda functions and ECS tasks
- CloudWatch Metrics for performance monitoring
- X-Ray for distributed tracing
- Custom dashboards for key metrics (job success rate, latency, errors)

### 8.3 Error Handling

- Graceful degradation with fallback providers
- Retry logic with exponential backoff
- User-friendly error messages
- Detailed error logging for debugging

### 8.4 Security

- HTTPS only (TLS 1.2+)
- API rate limiting
- Input validation and sanitization
- Secrets stored in AWS Secrets Manager
- IAM roles with least privilege
- VPC isolation for ECS tasks

---

## 9. Data Models

### 9.1 DynamoDB Tables

**Jobs Table:**

```json
{
  "job_id": "uuid",
  "user_id": "string",
  "type": "video|pdf|screenshare|rag",
  "status": "pending|processing|completed|failed",
  "progress": "0-100",
  "created_at": "timestamp",
  "updated_at": "timestamp",
  "input": {},
  "result": {},
  "error": "string"
}
```

**Users Table:**

```json
{
  "user_id": "uuid",
  "email": "string",
  "name": "string",
  "created_at": "timestamp",
  "subscription": "free|pro|enterprise",
  "usage_stats": {}
}
```

**Documents Table:**

```json
{
  "doc_id": "uuid",
  "user_id": "string (GSI)",
  "title": "string",
  "s3_key": "string",
  "graph_id": "string",
  "kb_id": "string",
  "created_at": "timestamp",
  "metadata": {}
}
```

**Sessions Table (Screenshare):**

```json
{
  "session_id": "uuid",
  "user_id": "string",
  "stream_arn": "string",
  "language": "string",
  "status": "active|ended",
  "created_at": "timestamp",
  "history": []
}
```

---

## 10. API Design

### 10.1 REST Endpoints

**Video Generation:**

| Endpoint                           | Method | Description            |
| ---------------------------------- | ------ | ---------------------- |
| `/api/v1/videos/generate`          | POST   | Start video generation |
| `/api/v1/videos/{job_id}`          | GET    | Get job status         |
| `/api/v1/videos/{job_id}/download` | GET    | Download video         |

**PDF Processing:**

| Endpoint                               | Method | Description      |
| -------------------------------------- | ------ | ---------------- |
| `/api/v1/pdf/upload`                   | POST   | Upload PDF       |
| `/api/v1/pdf/{doc_id}/analyze`         | POST   | Analyze sections |
| `/api/v1/pdf/{doc_id}/generate-videos` | POST   | Generate videos  |

**Code Execution:**

| Endpoint                 | Method | Description              |
| ------------------------ | ------ | ------------------------ |
| `/api/v1/code/execute`   | POST   | Execute code             |
| `/api/v1/code/languages` | GET    | List supported languages |

**Document Q&A:**

| Endpoint                           | Method | Description       |
| ---------------------------------- | ------ | ----------------- |
| `/api/v1/documents/upload`         | POST   | Upload document   |
| `/api/v1/documents/{doc_id}/query` | POST   | Ask question      |
| `/api/v1/documents/{doc_id}`       | GET    | Get document info |

**Screenshare:**

| Endpoint                               | Method | Description   |
| -------------------------------------- | ------ | ------------- |
| `/api/v1/screenshare/start`            | POST   | Start session |
| `/api/v1/screenshare/{session_id}/end` | POST   | End session   |

### 10.2 WebSocket Endpoints

| Endpoint                          | Description            |
| --------------------------------- | ---------------------- |
| `WS /ws/progress/{job_id}`        | Real-time job progress |
| `WS /ws/screenshare/{session_id}` | Screenshare session    |

---

## 11. Deployment Architecture

### 11.1 AWS Services Utilization

| Service         | Purpose                     | Utilization |
| --------------- | --------------------------- | ----------- |
| S3              | Video/image/PDF storage     | 15%         |
| CloudFront      | CDN for global delivery     | 5%          |
| API Gateway     | REST + WebSocket APIs       | 5%          |
| Lambda          | Serverless API handlers     | 5%          |
| ECS Fargate     | Video workers, code sandbox | 10%         |
| DynamoDB        | Job state, users, sessions  | 5%          |
| Bedrock         | Claude AI, Titan embeddings | 10%         |
| Bedrock KB      | RAG knowledge bases         | 5%          |
| Polly           | TTS fallback                | 2%          |
| Transcribe      | Audio transcription         | 3%          |
| Kinesis         | Video streaming             | 3%          |
| Secrets Manager | API key storage             | 2%          |
| **Total**       |                             | **70%**     |

### 11.2 External Services

| Service      | Purpose                             |
| ------------ | ----------------------------------- |
| Groq         | Scene planning LLM                  |
| Gemini       | Code generation, Gemini Live        |
| Sarvam AI    | Primary TTS (12+ languages)         |
| Pollinations | Primary image generation            |
| Iconify      | SVG icons (275k+)                   |
| Piston API   | Code execution (self-hosted on ECS) |

---

## 12. Performance Optimization

- Parallel image generation (3 concurrent)
- MD5-based caching for images
- CloudFront CDN for global delivery
- ECS auto-scaling based on queue depth
- Lambda concurrency limits
- DynamoDB on-demand pricing
- S3 intelligent tiering

---

## 13. Testing Strategy

- Unit tests for core logic
- Integration tests for API endpoints
- End-to-end tests for complete workflows
- Load testing for scalability
- Security testing for vulnerabilities

---

## 14. Error Handling

- Multi-provider fallback chains
- Retry logic with exponential backoff
- Graceful degradation
- User-friendly error messages
- Detailed logging for debugging

---

## 15. Scalability Considerations

- Horizontal scaling with ECS Fargate
- Lambda auto-scaling
- DynamoDB on-demand capacity
- CloudFront for global distribution
- Queue-based job processing

---

## 16. Cost Optimization

- S3 lifecycle policies (Glacier after 90 days)
- DynamoDB on-demand pricing
- Lambda memory optimization
- ECS spot instances for non-critical workloads
- CloudFront caching to reduce origin requests
- Free tier services (Pollinations, Iconify)

---

## 17. Future Enhancements

- Real-time collaboration on videos
- Custom voice cloning
- Advanced animation styles
- Mobile app support
- Offline mode for code player
- Integration with LMS platforms
- Analytics dashboard
- Team workspaces
- Version control for videos
- A/B testing for educational content

---

**End of Design Document**
