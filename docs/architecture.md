# Architecture Overview

## Overview
Voice Studio is a modular desktop application for voice recording, processing, and AI-driven audio workflows.

## System Components

```
┌────────────────────────────────────────────────────────┐
│                   Compose Multiplatform UI             │
│        (commonMain / windowsMain / macosMain)          │
└───────────────────────────┬────────────────────────────┘
                            │
┌───────────────────────────▼────────────────────────────┐
│                      Shared Layer                      │
│                (Domain & Application Logic)            │
└───────────────┬────────────────────────┬───────────────┘
                │                        │
┌───────────────▼──────────────┐ ┌───────▼──────────────┐
│         Audio Engine         │ │       AI Engine      │
│   (Capture, DSP, Playback)   │ │ (Inference & Models) │
└──────────────────────────────┘ └──────────────────────┘
```

### 1. App (`app/`)
- Compose Multiplatform desktop UI client.
- Targets macOS and Windows native windowing and platform hooks.

### 2. Audio Engine (`audio-engine/`)
- Low-latency native audio processing written in Rust.
- Manages audio hardware streams, DSP effects, format codecs, and pipeline dispatch.

### 3. AI Engine (`ai-engine/`)
- Handles model loading, local inference runtimes (e.g., ONNX / TensorRT / CoreML), and audio feature extraction.

### 4. Shared (`shared/`)
- Domain entities, state management, and orchestration between UI and engines.
