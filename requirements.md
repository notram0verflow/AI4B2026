# Requirements Document: Eklavya.ai

> **Comprehensive requirements specification for an AI-powered educational technology platform**

---

## 📋 Table of Contents

1. [Introduction](#introduction)
2. [Glossary](#glossary)
3. [Feature 1: AI-Powered Educational Video Generation](#feature-1-ai-powered-educational-video-generation)
4. [Feature 2: Interactive Code Player (CodeVid)](#feature-2-interactive-code-player-codevid)
5. [Feature 3: PDF to Video Tutorials](#feature-3-pdf-to-video-tutorials)
6. [Feature 4: Live AI Screenshare Tutoring](#feature-4-live-ai-screenshare-tutoring)
7. [Feature 5: Graph RAG Document Analyzer](#feature-5-graph-rag-document-analyzer)
8. [Cross-Cutting Requirements](#cross-cutting-requirements)
9. [Non-Functional Requirements](#non-functional-requirements)
10. [Acceptance Criteria Summary](#acceptance-criteria-summary)

---

## Introduction

**Eklavya.ai** is an AI-powered educational technology solution designed to help people learn faster, work smarter, and become more productive while building or understanding technology. The platform transforms complex technical concepts into engaging, interactive learning experiences through AI-generated educational videos, interactive code environments, live AI tutoring, and intelligent document analysis.

### Vision Statement

To democratize educational content creation by enabling educators, learners, and content creators to generate professional-quality animated videos and interactive learning experiences from simple text descriptions, PDFs, and live interactions.

### Target Users

| User Type            | Primary Needs                                    |
| -------------------- | ------------------------------------------------ |
| **Educators**        | Create engaging lecture content quickly          |
| **Students**         | Learn through interactive, visual experiences    |
| **Content Creators** | Produce professional educational videos at scale |
| **Researchers**      | Communicate complex concepts visually            |
| **Developers**       | Learn coding through interactive examples        |

---

## Glossary

| Term              | Definition                                                                              |
| ----------------- | --------------------------------------------------------------------------------------- |
| **System**        | Eklavya.ai Platform                                                                     |
| **Manim**         | Mathematical Animation Engine - Python library for creating animated educational videos |
| **RAG**           | Retrieval-Augmented Generation - AI technique combining retrieval and generation        |
| **BM25**          | Best Matching 25 - ranking function for information retrieval                           |
| **Graph_RAG**     | Graph-based Retrieval-Augmented Generation for document analysis                        |
| **TTS**           | Text-to-Speech synthesis                                                                |
| **Sarvam_AI**     | Indian multilingual TTS service provider                                                |
| **Monaco_Editor** | Code editor component from Visual Studio Code                                           |
| **Sandpack**      | In-browser code execution environment                                                   |
| **WebRTC**        | Web Real-Time Communication protocol                                                    |
| **Gemini_Live**   | Google's real-time multimodal AI API                                                    |
| **Handmanim**     | Hand-drawn animation style for Manim                                                    |
| **CodeVid**       | Interactive code player format (.codevid)                                               |
| **Master_Clock**  | Audio element serving as timing reference                                               |
| **Event_Engine**  | System processing timestamped events                                                    |
| **Bedrock_KB**    | AWS Bedrock Knowledge Base for RAG                                                      |
| **Piston_API**    | Self-hosted code execution service supporting 20+ languages                             |
| **ECS_Fargate**   | AWS container orchestration service for serverless compute                              |

---

## Feature 1: AI-Powered Educational Video Generation

```
╔═══════════════════════════════════════════════════════════════════════════╗
║                    VIDEO GENERATION REQUIREMENTS                          ║
║  Transform text prompts into professional animated educational videos     ║
╚═══════════════════════════════════════════════════════════════════════════╝
```

### Requirement 1.1: Scene Planning with Educational Architecture

**User Story:** As an educator or learner, I want to generate professional animated educational videos from text prompts, so that I can create engaging visual explanations of complex concepts without manual animation work.

**Priority:** HIGH | **Type:** Functional

#### Acceptance Criteria

```
┌─────────────────────────────────────────────────────────────────────────┐
│  5-LAYER EDUCATIONAL ARCHITECTURE                                       │
├─────────────────────────────────────────────────────────────────────────┤
│  Layer 1: HOOK (15-20%)       → Create curiosity                        │
│  Layer 2: FOUNDATION (20-25%) → Build understanding                     │
│  Layer 3: MECHANISM (40-50%)  → Reveal step-by-step process             │
│  Layer 4: INSIGHT (10-15%)    → Show hidden connections                 │
│  Layer 5: SYNTHESIS (5-10%)   → Connect to universal principles         │
└─────────────────────────────────────────────────────────────────────────┘
```

1. **WHEN** a user provides a text prompt, **THE** System **SHALL** generate a structured scene description using the 5-layer educational architecture (Hook, Foundation, Mechanism, Insight, Synthesis)

2. **WHEN** scene planning is complete, **THE** System **SHALL** generate up to 3 supporting images in parallel using AI image generation services

3. **WHEN** images are generated, **THE** System **SHALL** create valid Manim Python code that incorporates the images and follows educational best practices

4. **WHERE** mood/vibe selection is provided, **THE** System **SHALL** apply the selected style (Professional, Playful, 3Blue1Brown, Dramatic) to the video generation

5. **WHERE** Handmanim style is selected, **THE** System **SHALL** generate hand-drawn animation aesthetics

---

### Requirement 1.2: Multi-Language Narration

**Priority:** HIGH | **Type:** Functional

#### Acceptance Criteria

1. **WHEN** Manim code is generated, **THE** System **SHALL** produce multilingual narration audio using Sarvam_AI TTS for 12+ Indian languages

2. **WHEN** all assets are ready, **THE** System **SHALL** render the animation using Manim engine at user-selected quality (240p-1080p)

3. **WHEN** rendering is complete, **THE** System **SHALL** merge audio with video, add watermark, and deliver the final MP4 file

4. **WHERE** background music is requested, **THE** System **SHALL** add royalty-free music to the final video

5. **WHERE** transcription is requested, **THE** System **SHALL** generate VTT subtitle files synchronized with the narration

**Supported Languages:**

```
┌──────────────┬──────────────┬──────────────┬──────────────┐
│ English (IN) │ Hindi        │ Tamil        │ Telugu       │
│ Bengali      │ Gujarati     │ Kannada      │ Malayalam    │
│ Marathi      │ Odia         │ Punjabi      │ + more       │
└──────────────┴──────────────┴──────────────┴──────────────┘
```

---

### Requirement 1.3: RAG-Enhanced Code Generation

**User Story:** As a developer using the platform, I want the AI to generate accurate Manim code based on official documentation, so that the generated animations use correct APIs and best practices.

**Priority:** HIGH | **Type:** Functional

#### Acceptance Criteria

1. **THE** System **SHALL** maintain a knowledge base of Manim documentation using AWS Bedrock_KB

2. **WHEN** generating Manim code, **THE** System **SHALL** retrieve relevant documentation chunks using BM25 ranking

3. **WHEN** documentation is retrieved, **THE** System **SHALL** augment the LLM context with retrieved information before code generation

4. **THE** System **SHALL** reduce code generation errors by at least 40% compared to non-RAG approaches

5. **THE** System **SHALL** include contextual examples from documentation in generated code comments

**RAG Pipeline:**

```
User Prompt → Query Manim KB (BM25) → Retrieve Top 5 Docs
            ↓
    Augment LLM Context → Generate Code → Validate Syntax
            ↓
    Final Manim Python Code with Documentation References
```

---

## Feature 2: Interactive Code Player (CodeVid)

```
╔═══════════════════════════════════════════════════════════════════════════╗
║                    CODEVID PLAYER REQUIREMENTS                            ║
║  Pause videos, edit code, execute it, and see results in real-time       ║
╚═══════════════════════════════════════════════════════════════════════════╝
```

### Requirement 2.1: Master Clock Architecture

**User Story:** As a learner, I want to pause educational coding videos, edit the code, run it, and see results, so that I can experiment and learn by doing rather than passive watching.

**Priority:** HIGH | **Type:** Functional

#### State Machine

```
┌──────────┐         ┌──────────┐         ┌──────────┐
│ PLAYING  │ pause   │  PAUSED  │ resume  │  RESUME  │
│ readOnly │────────▶│ readOnly │────────▶│ readOnly │
│  = true  │         │  = false │         │  = true  │
└────┬─────┘         └────┬─────┘         └────┬─────┘
     │                    │                    │
     └────────────────────┴────────────────────┘
```

#### Acceptance Criteria

1. **THE** System **SHALL** implement a custom React player component with three synchronized layers: Audio (Master_Clock), Monaco_Editor (code), and Overlays (visual elements)

2. **WHEN** the player is in PLAYING state, **THE** Event_Engine **SHALL** consume timestamped events and update the Monaco_Editor programmatically with readOnly set to true

3. **WHEN** the user pauses the video, **THE** System **SHALL** transition to PAUSED state, stop the Event_Engine, and unlock the Monaco_Editor (readOnly: false)

4. **WHILE** in PAUSED state, **THE** System **SHALL** allow the user to edit code freely and execute it using Sandpack or Piston code execution service

5. **WHEN** the user clicks resume, **THE** System **SHALL** calculate the correct code state at the current timestamp, force-reset the editor to that state (discarding user changes), and resume playback

---

### Requirement 2.2: Multi-Language Code Execution

**Priority:** HIGH | **Type:** Functional

#### Acceptance Criteria

1. **THE** System **SHALL** support 20+ programming languages through Piston API for code execution

2. **THE** System **SHALL** display execution output (stdout/stderr) in a dedicated panel below the code editor

3. **THE** System **SHALL** support export/import of .codevid files containing video reference and timestamped event data

4. **WHEN** overlays are defined in events, **THE** System **SHALL** show/hide HTML elements (images, diagrams, webcam feeds) at specified timestamps and positions

**Supported Languages:**

```
┌──────────────┬──────────────┬──────────────┬──────────────┐
│ JavaScript   │ Python       │ TypeScript   │ Java         │
│ C            │ C++          │ Rust         │ Go           │
│ Ruby         │ PHP          │ Swift        │ Kotlin       │
│ C#           │ Bash         │ SQL          │ R            │
│ Lua          │ Haskell      │ Scala        │ Perl         │
└──────────────┴──────────────┴──────────────┴──────────────┘
```

---

### Requirement 2.3: Recording Format and Data Structure

**User Story:** As a content creator, I want to create interactive code tutorials with synchronized audio, code changes, and visual overlays, so that learners can follow along with precise timing.

**Priority:** MEDIUM | **Type:** Functional

#### Acceptance Criteria

1. **THE** System **SHALL** define a Recording interface with audioUrl (string) and events (array of timestamped events)

2. **WHEN** creating events, **THE** System **SHALL** support event types: CODE_TYPE, CURSOR_MOVE, OVERLAY_SHOW, OVERLAY_HIDE

3. **WHEN** storing recordings, **THE** System **SHALL** save JSON files with timestamp (milliseconds), event type, and payload data

4. **THE** System **SHALL** use the audio element's currentTime as the master clock for all synchronization

5. **THE** System **SHALL** support frame positioning with percentage-based coordinates (x, y, width, height)

**.codevid File Format:**

```json
{
  "version": 1,
  "recording": {
    "audioUrl": "https://cdn.example.com/audio/lesson1.mp3",
    "duration": 180.5,
    "events": [
      {
        "timestamp": 1.5,
        "type": "CODE_TYPE",
        "payload": { "line": 1, "text": "from manim import *" }
      },
      {
        "timestamp": 10.0,
        "type": "OVERLAY_SHOW",
        "payload": {
          "id": "diagram1",
          "x": 60,
          "y": 10,
          "width": 35,
          "height": 40,
          "src": "/images/neural-net.png"
        }
      }
    ]
  }
}
```

---

## Feature 3: PDF to Video Tutorials

```
╔═══════════════════════════════════════════════════════════════════════════╗
║                    PDF TO VIDEO REQUIREMENTS                              ║
║  Upload PDFs and generate tutorial videos for each section               ║
╚═══════════════════════════════════════════════════════════════════════════╝
```

### Requirement 3.1: PDF Content Extraction

**User Story:** As a content creator, I want to upload PDF documentation and automatically generate short tutorial videos for each section, so that I can quickly create video courses from existing materials.

**Priority:** MEDIUM | **Type:** Functional

#### Acceptance Criteria

1. **WHEN** a user uploads a PDF document, **THE** System **SHALL** extract text content, images, tables, and structural information

2. **WHEN** PDF content is extracted, **THE** System **SHALL** analyze the document using Bedrock Claude to identify logical sections and key concepts

3. **WHEN** sections are identified, **THE** System **SHALL** present a preview showing detected sections with estimated video duration (1-3 minutes each)

4. **WHEN** the user selects sections, **THE** System **SHALL** generate scene descriptions using extracted PDF images and content

5. **WHEN** scene descriptions are ready, **THE** System **SHALL** generate short tutorial videos for each selected section in batch mode

6. **THE** System **SHALL** maintain relevancy to the uploaded PDF content by using extracted assets directly in animations

7. **THE** System **SHALL** allow language and mood selection for the generated tutorial series

**Extraction Pipeline:**

```
PDF Upload → PyMuPDF Parser → Extract (Text, Images, Tables)
          ↓
    Claude Analysis → Identify Sections → Preview UI
          ↓
    User Selection → Batch Video Generation → Video Series
```

---

## Feature 4: Live AI Screenshare Tutoring

```
╔═══════════════════════════════════════════════════════════════════════════╗
║                    SCREENSHARE TUTORING REQUIREMENTS                      ║
║  Real-time AI guidance with screen/webcam sharing and voice responses    ║
╚═══════════════════════════════════════════════════════════════════════════╝
```

1. WHEN a user initiates screenshare, THE System SHALL establish a WebRTC connection for screen/webcam streaming
2. WHEN the stream is active, THE System SHALL send video frames to Gemini_Live API for real-time analysis
3. WHEN the user speaks, THE System SHALL transcribe audio using Amazon Transcribe and send to Gemini_Live
4. WHEN Gemini_Live generates a response, THE System SHALL display text response in the chat interface
5. WHEN voice response is requested, THE System SHALL synthesize speech using Sarvam_AI TTS in the user's selected language
6. THE System SHALL support multilingual interaction for 12+ Indian languages
7. THE System SHALL process and respond to user queries within 3 seconds for real-time interaction
8. THE System SHALL maintain conversation context throughout the session

### Requirement 7: Graph RAG Document Analyzer

**User Story:** As a researcher, I want to upload research papers and ask questions that return exact page and line references, so that I can quickly find relevant information without reading entire documents.

#### Acceptance Criteria

1. WHEN a user uploads a PDF document, THE System SHALL parse the document extracting page numbers, line numbers, and section identifiers
2. WHEN parsing is complete, THE System SHALL build a graph index with nodes (paragraphs, sentences, key terms) and edges (references, relationships, sequential flow)
3. WHEN the graph is built, THE System SHALL create vector embeddings using Amazon Titan and store in Bedrock_KB with line-level metadata
4. WHEN a user submits a query, THE System SHALL perform semantic search using the vector store and graph traversal
5. WHEN results are found, THE System SHALL return highlighted passages with exact page and line numbers (e.g., "Page 5, Lines 123-128")
6. THE System SHALL display the PDF with the relevant section highlighted and scrolled into view
7. THE System SHALL show related passages from other parts of the document based on graph relationships
8. THE System SHALL reduce hallucinations by providing exact source references for all retrieved information

### Requirement 8: Real-Time Progress Tracking

**User Story:** As a user generating videos, I want to see real-time progress updates with percentage completion and current stage, so that I know how long to wait and can monitor the process.

#### Acceptance Criteria

1. WHEN a video generation job starts, THE System SHALL create a unique job_id and establish a WebSocket connection
2. WHILE processing, THE System SHALL broadcast progress updates with status, percentage (0-100), and descriptive message
3. THE System SHALL report progress through distinct stages: scene_planning (0-15%), image_generation (15-40%), code_generation (40-55%), tts_generation (55-60%), rendering (60-90%), watermarking (90-100%)
4. WHEN the job completes, THE System SHALL send a completion message with video_url and code_path
5. IF the job fails, THE System SHALL send an error message with failure details
6. THE System SHALL maintain WebSocket connections with 60-second ping/pong timeout
7. THE System SHALL provide a polling endpoint as fallback for clients that cannot use WebSockets

### Requirement 9: Multi-Provider Fallback System

**User Story:** As a system administrator, I want the platform to automatically fall back to alternative services when primary providers fail, so that the system remains reliable and available.

#### Acceptance Criteria

1. WHEN Pollinations.ai image generation fails, THE System SHALL attempt Stable Diffusion via HuggingFace
2. IF Stable Diffusion fails, THE System SHALL generate a placeholder image with text overlay
3. WHEN Gemini code generation fails, THE System SHALL attempt code generation using Groq Llama models
4. WHEN Sarvam_AI TTS is unavailable, THE System SHALL fall back to Amazon Polly for voice synthesis
5. THE System SHALL cache generated images using MD5 hash of prompts to avoid redundant generation
6. THE System SHALL log all fallback events for monitoring and debugging

### Requirement 10: Quality and Performance Standards

**User Story:** As a platform user, I want videos to generate quickly with good quality and the system to handle multiple concurrent requests, so that I can efficiently create content at scale.

#### Acceptance Criteria

1. THE System SHALL support quality presets: preview (240p, 15fps), low (480p, 15fps), medium (720p, 30fps), high (1080p, 60fps)
2. THE System SHALL generate images in parallel with maximum 3 concurrent workers
3. THE System SHALL complete video generation in under 5 minutes for standard quality (480p)
4. THE System SHALL scale horizontally using ECS Fargate workers with auto-scaling based on queue depth
5. THE System SHALL achieve 95% or higher video generation success rate
6. THE System SHALL maintain 99.9% system availability
7. THE System SHALL process code execution requests within 2 seconds for supported languages

### Requirement 11: Security and Data Management

**User Story:** As a platform administrator, I want secure storage of API keys and user data with proper access controls, so that sensitive information is protected.

#### Acceptance Criteria

1. THE System SHALL store all API keys (Gemini, Groq, Sarvam, HuggingFace) in AWS Secrets Manager
2. THE System SHALL use IAM-based authentication for AWS service access
3. THE System SHALL encrypt all data at rest using SSE-S3 for S3 storage
4. THE System SHALL store job state persistently in DynamoDB to survive server restarts
5. THE System SHALL implement lifecycle policies moving S3 objects to Glacier after 90 days
6. THE System SHALL validate and sanitize all user inputs before processing
7. THE System SHALL implement rate limiting to prevent abuse

### Requirement 12: Code Execution Sandbox

**User Story:** As a learner using the interactive code player, I want to safely execute code in multiple programming languages without security risks, so that I can experiment freely.

#### Acceptance Criteria

1. THE System SHALL execute code in isolated sandboxed environments using Piston API on ECS
2. THE System SHALL support 20+ programming languages including Python, JavaScript, TypeScript, Java, C, C++, Rust, Go, Ruby, PHP
3. THE System SHALL enforce resource limits (CPU, memory, execution time) per code execution request
4. THE System SHALL return stdout and stderr from code execution within 5 seconds
5. THE System SHALL prevent access to network, filesystem, and system resources from sandboxed code
6. IF Piston is unavailable, THE System SHALL fall back to Pyodide (Python WASM) for Python code and iframe sandbox for JavaScript

### Requirement 13: Educational Content Quality

**User Story:** As an educator, I want generated videos to follow proven pedagogical principles with clear structure and engaging presentation, so that learners can effectively understand complex topics.

#### Acceptance Criteria

1. THE System SHALL structure all educational content using the 5-layer architecture: Hook (15-20%), Foundation (20-25%), Mechanism (40-50%), Insight (10-15%), Synthesis (5-10%)
2. WHEN generating narration, THE System SHALL create curiosity-driven hooks that pose fascinating questions
3. WHEN explaining concepts, THE System SHALL use clear analogies and real-world connections
4. WHEN showing mechanisms, THE System SHALL reveal step-by-step processes with visual synchronization
5. THE System SHALL maintain consistent visual style with BLACK background, WHITE primary text, and defined color palette
6. THE System SHALL synchronize audio narration with visual animations using precise timing
7. THE System SHALL enforce safe screen boundaries (X: -6.5 to 6.5, Y: -3.5 to 3.5) for all visual elements

### Requirement 14: Multilingual Support

**User Story:** As a learner in India, I want to access educational content in my native language with natural-sounding voice narration, so that I can learn more effectively.

#### Acceptance Criteria

1. THE System SHALL support text-to-speech in 12+ languages: English (India), Hindi, Tamil, Telugu, Bengali, Gujarati, Kannada, Malayalam, Marathi, Odia, Punjabi
2. WHEN generating audio, THE System SHALL provide male and female voice options for each supported language
3. WHEN processing text for TTS, THE System SHALL normalize Unicode characters and mathematical symbols to speakable text
4. THE System SHALL handle subscript and superscript numbers in mathematical expressions
5. THE System SHALL maintain consistent voice characteristics throughout a video

### Requirement 15: Asset Management and Caching

**User Story:** As a system operator, I want efficient caching of generated assets to reduce costs and improve performance, so that the platform operates economically at scale.

#### Acceptance Criteria

1. THE System SHALL cache generated images using MD5 hash of prompts in local cache directory
2. WHEN an image is requested, THE System SHALL check cache before calling external generation APIs
3. THE System SHALL store generated videos, images, audio, and code in organized S3 bucket structure
4. THE System SHALL serve videos through CloudFront CDN for global low-latency access
5. THE System SHALL implement intelligent tiering for S3 storage to optimize costs
6. THE System SHALL clean up temporary files after successful video generation
7. THE System SHALL maintain cache hit rate above 30% for image generation requests
