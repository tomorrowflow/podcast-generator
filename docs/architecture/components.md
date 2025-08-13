# Components

**Web Application (Frontend)**

**Responsibility:** Provides the user interface for all interactions, including text/audio submission, template selection, progress display, podcast playback, and download management.
**Key Interfaces:**
- `podcastfy` Backend API for all data and generation requests.
- `21st-dev/magic-mcp` components for UI elements.
**Dependencies:** `podcastfy` Backend API.
**Technology Stack:** React, TypeScript, Vite, Tailwind CSS.

---

**`podcastfy` Backend API**

**Responsibility:** Orchestrates the entire podcast generation workflow. It receives requests from the frontend, coordinates with the LLM, TTS, and ASR services, manages the storage and retrieval of podcast files, and exposes the primary REST API for the frontend.
**Key Interfaces:**
- **Exposes:** `/podcasts/generate/text`, `/podcasts/generate/audio`, `/podcasts/{podcastId}`, `/podcasts/{podcastId}/download` (as defined in API Specification).
- **Consumes:** Ollama API, Kyutai TTS API, Whisper ASR API.
**Dependencies:** Ollama LLM Service, Kyutai TTS Service, Whisper ASR Webservice, Local File Storage.
**Technology Stack:** Python, FastAPI, LangChain (for internal orchestration).

---

**Ollama LLM Service**

**Responsibility:** Provides Large Language Model capabilities for text processing such as summarization, script generation, and template-driven content creation. It runs as a self-contained service.
**Key Interfaces:** OpenAI-compatible API (consumed by `podcastfy` Backend API).
**Dependencies:** None (self-contained Docker container).
**Technology Stack:** Ollama (Dockerized).

---

**Kyutai TTS Service**

**Responsibility:** Converts processed text content into high-quality audio speech. It runs as a self-contained service.
**Key Interfaces:** API for text-to-speech conversion (consumed by `podcastfy` Backend API).
**Dependencies:** None (self-contained Docker container).
**Technology Stack:** Kyutai TTS (Dockerized).

---

**Whisper ASR Webservice**

**Responsibility:** Transcribes uploaded audio content into text for further processing by the LLM. It runs as a self-contained service.
**Key Interfaces:** API for speech-to-text conversion (consumed by `podcastfy` Backend API).
**Dependencies:** None (self-contained Docker container).
**Technology Stack:** Whisper ASR Webservice (Dockerized).

---

**Local File Storage**

**Responsibility:** Provides persistent storage for all generated podcast audio files, and potentially temporary storage for uploaded source audio files during processing.
**Key Interfaces:** Standard filesystem operations (read/write access by `podcastfy` Backend API).
**Dependencies:** None (local mount/volume).
**Technology Stack:** Local filesystem.

---

**Component Diagram**

```mermaid
graph TD
    A[Web Application] --> B[podcastfy Backend API]
    B --> C[Ollama LLM Service]
    B --> D[Kyutai TTS Service]
    B --> E[Whisper ASR Webservice]
    B --> F[Local File Storage]

    subgraph Frontend "User Interface Layer"
        A
    end

    subgraph Backend "Application Logic & Orchestration"
        B
        subgraph AI Services "Containerized AI Models"
            C
            D
            E
        end
    end

    subgraph Data "Persistent Storage"
        F
    end

    A -- "Uses 21st-dev/magic-mcp" --> G(21st-dev/magic-mcp Library)
```
