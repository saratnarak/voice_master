# Voice Studio

A modular desktop studio application for real-time voice processing, audio recording, and AI inference.

## Project Structure

```
voice-studio/
│
├── app/
│   ├── composeApp/
│   │   ├── commonMain/
│   │   ├── windowsMain/
│   │   └── macosMain/
│   │
│   └── resources/
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
│   ├── architecture.md
│   ├── audio-pipeline.md
│   └── ui.md
│
└── README.md
```

## Modules

- **`app/`**: Compose Multiplatform application for desktop (Windows and macOS).
- **`audio-engine/`**: High-performance Rust audio processing engine (hardware capture, DSP graph, playback, codecs).
- **`ai-engine/`**: Machine learning models and real-time inference runtime.
- **`shared/`**: Common business domain logic and application coordination layer.
- **`tests/`**: Integration and cross-module test suites.
- **`docs/`**: Technical design and architectural documentation.
# voice_master
