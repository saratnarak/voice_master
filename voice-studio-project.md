# Voice Studio — Vibe Coding Project Specification

## 1. Product Overview

Voice Studio is a standalone desktop application for Windows and macOS that transforms ordinary microphone recordings into clean, natural, studio-like voice audio.

Primary goals:

- Local-first processing
- Offline operation
- No account required for the core product
- No cloud/API dependency for the MVP
- High-quality speech enhancement
- Real-time and offline processing
- Simple, premium desktop UX
- Native-feeling Windows and macOS experience

The product should feel closer to a modern Google-quality productivity/creative application than traditional audio software.

Do not copy proprietary Google UI designs. Use Material 3-inspired principles: clarity, hierarchy, spacious layouts, restrained color, accessibility, and consistent components.

---

# 2. Product Philosophy

## Core Principle

> Make a cheap microphone sound dramatically better without requiring a cloud subscription.

The application should hide audio-engine complexity from normal users while still providing advanced controls for experienced users.

## Privacy

The default experience should keep recordings and AI processing on the user's computer.

The MVP must not require:

- Internet access
- User accounts
- API keys
- Cloud processing
- Remote databases

Optional cloud services can be introduced later.

---

# 3. Platforms

Primary:

- Windows 11+
- macOS

Architecture should support both platforms from one repository.

Target hardware:

- Laptop microphones
- USB microphones
- Headsets
- Professional USB/XLR audio interfaces where supported

---

# 4. Recommended Technology Stack

## UI

- Kotlin
- Compose Multiplatform/Desktop
- Material 3-inspired design system

## Application Layer

- Kotlin
- Coroutines
- StateFlow
- Clean separation between UI and audio engine

## Audio Engine

- Rust
- Native audio processing
- Real-time-safe architecture

## AI Inference

- ONNX Runtime
- Locally stored speech/audio enhancement models
- Model abstraction so models can be replaced without rewriting the application

## DSP

Rust/native DSP for:

- EQ
- Compressor
- Limiter
- Gain
- Noise gate
- De-esser
- Loudness normalization
- Filtering

## Platform Audio

Windows:

- WASAPI

macOS:

- Core Audio

## Storage

Local filesystem.

No database is required for the MVP.

## Backend

No backend is required for the MVP.

FastAPI may be introduced later for optional:

- Cloud models
- Accounts
- Licensing
- Analytics
- Model downloads
- Cloud processing

---

# 5. High-Level Architecture

```text
                    Voice Studio Desktop
                           │
              ┌────────────┴────────────┐
              │                         │
        Windows 11                  macOS
              │                         │
              └────────────┬────────────┘
                           │
                    Compose Desktop
                           │
                    Kotlin Application
                           │
              ┌────────────┴────────────┐
              │                         │
         Audio Engine              AI Engine
              │                         │
        Rust / Native             ONNX Runtime
              │                         │
              └────────────┬────────────┘
                           │
                    Audio Pipeline
                           │
       ┌───────────────────┼───────────────────┐
       ↓                   ↓                   ↓
 Noise Reduction      Dereverberation       Voice Enhance
       │                   │                   │
       └───────────────────┼───────────────────┘
                           ↓
                    EQ / Compressor
                           ↓
                         Limiter
                           ↓
                      Loudness
                           ↓
                       Output
```

---

# 6. Repository Structure

```text
voice-studio/
│
├── app/
│   └── composeApp/
│       ├── commonMain/
│       ├── windowsMain/
│       └── macosMain/
│
├── audio-engine/
│   ├── src/
│   │   ├── capture/
│   │   ├── playback/
│   │   ├── pipeline/
│   │   ├── dsp/
│   │   ├── codecs/
│   │   └── platform/
│   └── Cargo.toml
│
├── ai-engine/
│   ├── models/
│   ├── inference/
│   └── runtime/
│
├── shared/
│   ├── domain/
│   └── application/
│
├── tests/
│
├── docs/
│   ├── product.md
│   ├── architecture.md
│   ├── audio-engine.md
│   ├── ai-models.md
│   ├── ui-design.md
│   └── roadmap.md
│
├── README.md
└── LICENSE
```

Use a monorepo.

---

# 7. Architectural Boundaries

The following separation is mandatory:

```text
Compose UI
    ↓
Application Layer
    ↓
Audio API
    ↓
Rust Audio Engine
    ↓
AI / DSP
    ↓
Platform Audio
```

The UI must not directly manipulate low-level audio buffers.

The UI must not know whether processing is implemented in Rust, ONNX Runtime, CPU, or GPU.

The audio engine must expose a clean API.

Example conceptual API:

```text
startRecording()
stopRecording()
pauseRecording()
play()
pause()
seek(position)
setNoiseReduction(amount)
setVoiceClarity(amount)
setPreset(preset)
process()
export()
```

---

# 8. Real-Time Audio Architecture

Real-time audio processing is a critical subsystem.

The audio callback/thread must remain responsive and deterministic.

Never perform the following directly on the real-time audio thread:

- Blocking file I/O
- Network calls
- Heavy memory allocation
- UI operations
- Logging that can block
- Database operations
- Long-running model loading

Use dedicated worker threads/tasks for expensive processing.

Conceptual architecture:

```text
Microphone
    ↓
Audio Callback
    ↓
Lock-free / bounded buffer
    ↓
Processing Pipeline
    ↓
Output Buffer
    ↓
Speaker
```

The UI communicates asynchronously with the audio engine.

---

# 9. Two Processing Modes

Do not force real-time and offline processing to use exactly the same pipeline.

## Real-Time Mode

Optimized for:

- Low latency
- Stable CPU usage
- Live microphone monitoring
- Virtual microphone

```text
Microphone
    ↓
Small audio chunks
    ↓
Low-latency AI
    ↓
DSP
    ↓
Output
```

## Offline Studio Mode

Optimized for:

- Maximum quality
- Longer recordings
- Larger AI models
- Multi-pass processing

```text
Audio File
    ↓
Pre-processing
    ↓
High-quality AI
    ↓
Dereverb
    ↓
DSP
    ↓
Loudness
    ↓
Export
```

---

# 10. AI Model Strategy

Do not train a proprietary model for the MVP.

Start with commercially usable/open-source models after verifying the exact license of both the software and model weights.

Potential technologies to evaluate:

- DeepFilterNet
- Silero VAD
- SpeechBrain speech-enhancement models
- ONNX Runtime

Do not assume that every model or model weight has identical licensing.

Before shipping commercially, audit:

- Source code license
- Model weight license
- Dataset restrictions
- Redistribution rights
- Commercial-use rights
- Attribution requirements

## AI Abstraction

Create an interface similar to:

```text
SpeechEnhancementModel
    ├── load()
    ├── process()
    ├── unload()
    └── metadata()
```

This allows the model to be replaced without changing the application architecture.

---

# 11. AI Model Storage

Keep AI models separate from application code.

Conceptual structure:

```text
models/
├── core/
│   ├── vad/
│   └── noise-reduction/
│
└── optional/
    ├── studio/
    ├── dereverb/
    └── voice-enhancement/
```

The application should support lazy-loading models.

Do not load every model into memory at application startup.

---

# 12. Storage Targets

Target installed application size:

- Ideal: ~150–250 MB
- Acceptable: ~250–450 MB

Keep optional AI models separate where practical.

User recordings should be stored separately from application resources.

Example:

```text
Voice Studio/
├── Projects/
├── Recordings/
│   ├── Original/
│   └── Enhanced/
├── Models/
├── Presets/
└── Cache/
```

Avoid unnecessary duplication of large audio files.

---

# 13. Audio Formats

Recommended recording formats:

- WAV
- FLAC

Optional export:

- MP3
- AAC where platform/licensing considerations permit

For high-quality voice recording, support:

- Mono
- 48 kHz
- 24-bit where supported

Do not force every recording to stereo.

---

# 14. Main Product Modes

## Mode 1 — Record

Record microphone input.

Features:

- Microphone selection
- Input level
- Monitoring
- Noise reduction
- Real-time waveform
- Pause/resume
- Save recording

## Mode 2 — Enhance

Import existing audio and improve it.

Features:

- Noise reduction
- Room echo reduction
- Voice clarity
- Warmth
- Loudness
- A/B comparison
- Presets
- Export

## Mode 3 — Live Mic

Create a processed virtual microphone.

Concept:

```text
Microphone
    ↓
Voice Studio
    ↓
AI Enhancement
    ↓
Virtual Microphone
    ↓
Zoom / Discord / OBS / Meet / Other Apps
```

This should be a later milestone.

---

# 15. UI Design Direction

The UI should be inspired by modern Google/Material 3 design principles.

Do not copy Google products.

The application should feel:

- Clean
- Calm
- Premium
- Friendly
- Professional
- Spacious
- Fast
- Technical without being intimidating

Avoid traditional dense audio-editor interfaces.

Avoid:

- Tiny buttons everywhere
- Excessive toolbars
- Excessive gradients
- Excessive shadows
- Glossy effects
- Visual clutter
- Too many controls on the main screen

---

# 16. Main Navigation

Keep navigation minimal.

Recommended:

```text
Home
Projects
Record
Enhance
Settings
```

Use a persistent navigation rail/sidebar.

---

# 17. Home Screen

The home screen should immediately answer:

> What do I want to do?

Concept:

```text
Good morning

What would you like to do?

┌──────────────────┐  ┌──────────────────┐
│                  │  │                  │
│   Record         │  │   Enhance        │
│                  │  │                  │
│   Record voice   │  │   Improve audio  │
│                  │  │                  │
└──────────────────┘  └──────────────────┘

Recent projects
```

Keep the screen simple.

---

# 18. Record Screen

The recording screen should focus attention on recording.

Concept:

```text
Studio Recording

       ● 00:02:43

─────────────────────────────

       AUDIO WAVEFORM

─────────────────────────────

Microphone
MacBook Microphone

Noise reduction     ON
Monitoring          ON

           ●
         RECORD
```

Use a large recording control.

Waveform animation should be smooth but restrained.

---

# 19. Enhancement Screen

This is one of the most important screens.

Concept:

```text
Enhance Voice

Original              Enhanced

──────────────        ──────────────

          ▶ Compare

Preset

○ Natural
● Studio
○ Podcast
○ Voice Over

Noise Reduction     ━━━━━●━━━━
Room Echo           ━━━━●━━━━━━
Voice Clarity       ━━━━━━━●━━━
Warmth              ━━━━●━━━━━━

       [ Enhance Voice ]
```

---

# 20. A/B Comparison

The application must provide an obvious way to compare:

```text
Original ↔ Enhanced
```

Users should be able to hear the difference immediately.

This should be one of the strongest UX features.

---

# 21. Presets

Initial presets:

- Natural
- Studio
- Podcast
- Voice Over
- Clean Speech

Presets should be understandable to non-technical users.

Advanced controls should be available but not dominate the primary workflow.

---

# 22. Progressive Disclosure

Primary controls:

- Noise Reduction
- Voice Clarity
- Room Echo
- Warmth

Advanced controls:

- EQ
- Compressor
- Limiter
- Gate
- De-esser
- Gain
- Loudness

Do not expose every technical control by default.

---

# 23. UI Components

Build a reusable design system.

Suggested components:

```text
AppShell
NavigationRail
TopBar
PrimaryButton
SecondaryButton
AudioWaveform
AudioPlayer
RecordButton
PresetCard
Slider
ProcessingIndicator
ABCompare
ProjectCard
EmptyState
ErrorState
```

Before creating a new component:

1. Search for an existing component.
2. Reuse existing design tokens.
3. Follow existing spacing and typography.
4. Avoid one-off implementations.

---

# 24. Visual Design

Use:

- Rounded surfaces
- Subtle borders
- Minimal elevation
- Clear hierarchy
- Generous spacing
- Modern typography
- One primary accent color

Support:

- Light theme
- Dark theme

Do not use arbitrary colors for every component.

Use semantic colors for:

- Recording
- Processing
- Success
- Warning
- Error

Do not rely only on color to communicate state.

---

# 25. Accessibility

Support:

- Keyboard navigation
- Visible focus states
- Sufficient contrast
- Screen-reader-friendly labels
- Large click targets
- Window resizing
- High-DPI displays

---

# 26. Animation

Use subtle animations for:

- Recording state
- Processing progress
- Page transitions
- Waveform updates
- A/B switching

Never block user interaction because of animation.

Avoid excessive motion.

---

# 27. Error Handling

Errors must explain:

1. What happened.
2. Why it happened, when known.
3. What the user can do next.

Avoid exposing technical stack traces to normal users.

Provide diagnostic details behind an optional advanced/debug view.

---

# 28. AI Coding Rules

The coding agent must follow these rules.

## Before changing code

1. Inspect the existing project structure.
2. Read relevant documentation.
3. Search for existing implementations.
4. Follow existing conventions.
5. Avoid unnecessary architectural changes.

## Before adding a dependency

Ask:

- Is the dependency necessary?
- Is there already an existing solution?
- Is the license compatible?
- Does it support Windows and macOS?
- Does it increase application size significantly?
- Does it affect offline operation?

## Before creating a UI component

Search for reusable components first.

## Before changing the audio engine

Understand:

- Threading
- Buffer ownership
- Latency
- Sample rate
- Channel count
- Memory allocation
- Platform audio behavior

Do not make speculative changes to real-time audio code.

## Do not

- Put audio processing inside Compose UI
- Block the UI thread
- Block the real-time audio thread
- Hard-code platform paths
- Hard-code secrets
- Add cloud dependencies to the MVP
- Add unnecessary dependencies
- Rewrite unrelated files
- Introduce a framework just because it is popular

---

# 29. Testing Strategy

Test at multiple levels.

## Kotlin

- Unit tests
- Application logic tests
- UI state tests

## Rust

- Audio pipeline tests
- DSP tests
- Buffer tests
- Concurrency tests
- Format conversion tests

## AI

Test:

- Clean speech
- Background fan
- Traffic
- Keyboard
- Room echo
- Low microphone volume
- Clipping
- Multiple speakers

## End-to-End

Test:

```text
Record
→ Process
→ Preview
→ Export
→ Re-import
```

---

# 30. Development Roadmap

## V0.1 — Proof of Concept

Build only:

```text
Import WAV
    ↓
AI Noise Reduction
    ↓
Preview
    ↓
A/B Compare
    ↓
Export WAV
```

Goal:

Prove that the enhancement quality is good enough.

Do not build accounts, cloud services, or advanced UI.

---

## V0.2 — Desktop Recording

Add:

- Microphone selection
- Recording
- Pause/resume
- Waveform
- Local project saving
- Basic presets

---

## V0.3 — Studio Processing

Add:

- Dereverb
- Voice clarity
- EQ
- Compressor
- Limiter
- Loudness normalization
- Advanced controls

---

## V0.4 — Real-Time Enhancement

Add:

- Low-latency processing
- Live monitoring
- Real-time AI
- CPU/GPU optimization

---

## V0.5 — Virtual Microphone

Add:

```text
Microphone
    ↓
Voice Studio
    ↓
AI Enhancement
    ↓
Virtual Microphone
```

Target:

- Zoom
- Discord
- OBS
- Google Meet
- Other desktop applications

---

## V1.0 — Product Release

Add:

- Windows installer
- macOS installer
- Auto-update strategy
- Crash reporting only if explicitly enabled
- Model management
- Preset management
- Documentation
- Commercial license audit
- Performance optimization
- Accessibility review

---

# 31. Backend Strategy

Do not build FastAPI into the MVP unless there is a concrete requirement.

The first release should be:

```text
Windows/macOS
      │
      ├── Compose UI
      ├── Kotlin
      ├── Rust Audio Engine
      ├── ONNX Runtime
      └── Local AI Models
```

Later, optional backend services can be added:

```text
Voice Studio
     │
     ├── Local Engine
     │
     └── Optional Cloud
             │
           FastAPI
             │
         PostgreSQL
```

Possible future backend functions:

- User accounts
- Licensing
- Subscription
- Cloud processing
- Model distribution
- Sync
- Usage analytics
- Optional premium AI

The core local product should remain functional without the backend.

---

# 32. Privacy Principle

Default behavior:

```text
Audio
  ↓
Local Processing
  ↓
Local Result
```

No recording should leave the device unless the user explicitly chooses a cloud feature.

Do not collect audio recordings by default.

---

# 33. Product Positioning

The core product idea:

> Turn an ordinary microphone into a studio-quality voice.

Potential positioning:

- Studio voice from any microphone
- Private local AI
- No upload required
- Real-time voice enhancement
- Windows + macOS
- Simple enough for beginners
- Powerful enough for creators

---

# 34. Long-Term Architecture

Eventually:

```text
                         Voice Studio
                              │
               ┌──────────────┴──────────────┐
               │                             │
          Local Engine                  Cloud Services
               │                             │
        ┌──────┴──────┐                ┌─────┴─────┐
        │             │                │           │
      Windows        macOS          FastAPI    PostgreSQL
        │             │                │
        └──────┬──────┘                │
               │                        │
          Local AI                     Cloud AI
```

The local engine remains the foundation.

---

# 35. Critical Engineering Principle

AI coding tools can generate a large amount of code quickly.

That does not mean architecture should be delegated blindly.

The following areas require deliberate engineering:

- Audio thread design
- Buffer management
- Real-time latency
- Memory ownership
- AI model lifecycle
- Cross-platform audio APIs
- CPU/GPU scheduling
- Model licensing
- Application security
- Installer and update behavior

AI should accelerate implementation, not replace architectural judgment.

---

# 36. First Development Task

Do not start by building the complete UI.

First build a minimal technical prototype:

```text
Microphone
    ↓
Rust Audio Capture
    ↓
Audio Buffer
    ↓
Basic Processing
    ↓
Audio Output
```

Then add:

```text
Audio Input
    ↓
ONNX Runtime
    ↓
Speech Enhancement Model
    ↓
Audio Output
```

Once quality and latency are acceptable, build the Compose UI around the stable audio API.

The first success criterion is:

> A normal laptop microphone recording should sound noticeably cleaner and more professional after processing without introducing obvious robotic artifacts or unacceptable latency.
