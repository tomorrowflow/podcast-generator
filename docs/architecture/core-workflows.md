# Core Workflows

**1. Text-to-Podcast Generation Flow**

This sequence diagram illustrates the core process of a user submitting text and receiving a generated podcast.

```mermaid
sequenceDiagram
    participant User
    participant FrontendApp as Web Application
    participant podcastfyBackend as podcastfy Backend API
    participant OllamaLLM as Ollama LLM Service
    participant KyutaiTTS as Kyutai TTS Service
    participant LocalStorage as Local File Storage

    User->>FrontendApp: Input text & Click "Generate Podcast"
    FrontendApp->>podcastfyBackend: POST /podcasts/generate/text ({text, templateId})
    activate podcastfyBackend
    podcastfyBackend->>+OllamaLLM: Send text for processing (e.g., script generation)
    OllamaLLM-->>-podcastfyBackend: Return processed text
    podcastfyBackend->>+KyutaiTTS: Send processed text for audio synthesis
    KyutaiTTS-->>-podcastfyBackend: Return audio stream
    podcastfyBackend->>LocalStorage: Save generated audio file (.mp3)
    podcastfyBackend-->>-FrontendApp: Return podcastId & status
    deactivate podcastfyBackend
    FrontendApp->>User: Display "Generating Podcast..." & Progress UI (FR9)
    FrontendApp->>podcastfyBackend: Poll GET /podcasts/{podcastId} (for progress and final URL)
    activate podcastfyBackend
    podcastfyBackend-->>FrontendApp: Return status updates / final podcast details (audioUrl)
    deactivate podcastfyBackend
    FrontendApp->>User: Update UI with playable podcast & download link (FR7)
```

**2. Audio-to-Podcast Generation Flow**

This sequence diagram illustrates the workflow for a user uploading an audio file for transcription and subsequent podcast generation.

```mermaid
sequenceDiagram
    participant User
    participant FrontendApp as Web Application
    participant podcastfyBackend as podcastfy Backend API
    participant WhisperASR as Whisper ASR Webservice
    participant OllamaLLM as Ollama LLM Service
    participant KyutaiTTS as Kyutai TTS Service
    participant LocalStorage as Local File Storage

    User->>FrontendApp: Upload audio file & Click "Generate Podcast from Audio"
    FrontendApp->>podcastfyBackend: POST /podcasts/generate/audio (multipart/form-data)
    activate podcastfyBackend
    podcastfyBackend->>+WhisperASR: Send audio file for transcription
    WhisperASR-->>-podcastfyBackend: Return transcribed text
    podcastfyBackend->>+OllamaLLM: Send transcribed text for processing (e.g., script generation)
    OllamaLLM-->>-podcastfyBackend: Return processed text
    podcastfyBackend->>+KyutaiTTS: Send processed text for audio synthesis
    KyutaiTTS-->>-podcastfyBackend: Return audio stream
    podcastfyBackend->>LocalStorage: Save generated audio file (.mp3)
    podcastfyBackend-->>-FrontendApp: Return podcastId & status
    deactivate podcastfyBackend
    FrontendApp->>User: Display "Generating Podcast..." & Progress UI (FR9)
    FrontendApp->>podcastfyBackend: Poll GET /podcasts/{podcastId} (for progress and final URL)
    activate podcastfyBackend
    podcastfyBackend-->>FrontendApp: Return status updates / final podcast details (audioUrl)
    deactivate podcastfyBackend
    FrontendApp->>User: Update UI with playable podcast & download link (FR7)
```
