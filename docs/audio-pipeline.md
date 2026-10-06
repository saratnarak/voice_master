# Audio Pipeline

## Pipeline Lifecycle

```
[Hardware Input]
       │
       ▼
[Audio Capture] ────> [Ring Buffer] ────> [DSP Effects Chain]
                                                   │
                                                   ├──> [AI Feature Extraction / Inference]
                                                   │
                                                   └──> [Encoders / Codecs] ──> [Disk/File Storage]
                                                   │
                                                   └──> [Audio Playback] ────> [Hardware Output]
```

## Modules
- **Capture (`capture/`)**: Hardware device enumeration and audio stream input buffers.
- **Playback (`playback/`)**: Audio output rendering with low-latency synchronization.
- **Pipeline (`pipeline/`)**: Thread-safe ring buffers, graph routing, and lock-free message passing.
- **DSP (`dsp/`)**: Filters, gain control, EQ, noise reduction, and VAD (Voice Activity Detection).
- **Codecs (`codecs/`)**: Audio serialization and encoding/decoding (WAV, FLAC, Opus).
- **Platform (`platform/`)**: Platform-specific audio backends (CoreAudio for macOS, WASAPI for Windows).
