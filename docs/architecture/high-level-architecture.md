# High Level Architecture

**Technical Summary:**
The AI Podcast Generator is an on-premises, fullstack application designed to automate audio content creation from text or audio inputs. It leverages a microservices architecture, integrating `podcastfy` as the backend, `Ollama` for LLM capabilities, `Kyutai TTS` for text-to-speech, and `whisper-asr-webservice` for speech-to-text. The frontend will be a web application built with a `21st-dev/magic-mcp` compatible framework. Deployment will be via Docker Compose on a local gaming PC, utilizing its GPU for accelerated processing, ensuring data security within the datacenter.

**Platform and Infrastructure Choice:**
Based on the PRD, the primary platform will be a local datacenter deployment on a gaming PC via Docker Compose, leveraging its GPU capabilities for accelerated AI model processing. This choice aligns with the critical non-functional requirement of maintaining 0 instances of sensitive internal data exposure to external services.

*   **Option 1 (Recommended):** Local Datacenter (Gaming PC) with Docker Compose.
    *   **Pros:** Achieves critical data security goals (0 external exposure), leverages existing hardware for cost-efficiency, provides full control over the environment, and capitalizes on GPU for performance.
    *   **Cons:** Limited scalability beyond the single machine, requires manual management of the underlying hardware, potential single point of failure if not properly redundant (though not a requirement for MVP).
    *   **Key Services:** Docker, Docker Compose, NVIDIA Container Toolkit, `podcastfy` (Python backend), `Ollama` (LLM), `Kyutai TTS` (TTS), `whisper-asr-webservice` (STT).
    *   **Deployment Host and Regions:** On-premises gaming PC within the internal datacenter.

**Repository Structure:**
**Structure:** Monorepo
**Monorepo Tool:** Will be chosen based on the selected frontend framework's compatibility, for example, Nx or Turborepo if a JavaScript/TypeScript framework. Given the Python backend (`podcastfy`) and a JavaScript/TypeScript frontend, a tool that supports polyglot monorepos would be ideal.
**Package Organization:** The monorepo will consist of:
*   `apps/`: Containing the frontend web application (e.g., `web-app`) and potentially the `podcastfy` API service wrapper.
*   `packages/`: Containing shared logic, types, and UI components accessible by both frontend and backend (e.g., `shared-types`). `podcastfy` itself will be integrated as a dependency or a direct part of the monorepo, possibly within its own `packages/` or `services/` directory, for example, `src/podcastfy` as indicated in the PRD.

This structure allows for centralized management, simplified dependency handling, and atomic commits across the tightly coupled frontend and backend as specified in the PRD.

**High Level Architecture Diagram:**

```mermaid
graph TD
    User(Internal Content Creator) -->|Web Browser| FrontendApp(Web Application)
    FrontendApp -->|API Calls (REST)| BackendAPI(podcastfy Backend API)
    BackendAPI -->|Text for LLM| Ollama(Ollama LLM Service)
    Ollama -->|Generated Text| BackendAPI
    BackendAPI -->|Text for TTS| KyutaiTTS(Kyutai TTS Service)
    KyutaiTTS -->|Audio Stream| BackendAPI
    BackendAPI -->|Upload Audio| WhisperASR(Whisper ASR Webservice)
    WhisperASR -->|Transcribed Text| BackendAPI
    BackendAPI -->|Store/Retrieve| LocalStorage(Local File Storage)

    subgraph On-Premises Gaming PC (Docker Compose)
        Ollama
        KyutaiTTS
        WhisperASR
        BackendAPI
        FrontendApp
    end

    LocalStorage --> FrontendApp
```

**Architectural Patterns:**
*   **Microservices-Oriented Architecture:** Clearly defined APIs between web application, `podcastfy` backend, and STT/TTS/LLM services. - _Rationale:_ This is explicitly stated in NFR11 of the PRD and supports modularity, scalability, and independent deployment of components within the Docker Compose environment.
*   **Component-Based UI:** React (or similar) components with TypeScript. - _Rationale:_ Promotes reusability, maintainability, and type safety for the frontend, compatible with `21st-dev/magic-mcp` tools.
*   **Repository Pattern (Backend):** Abstract data access logic within the `podcastfy` backend. - _Rationale:_ Facilitates testing and provides flexibility for future changes in how audio files or other metadata are stored.
*   **API Gateway Pattern (Implicit):** The `podcastfy` backend acts as an implicit API Gateway for the AI services. - _Rationale:_ While not a separate service, the `podcastfy` backend centralizes communication with the various AI models, providing a single point of interaction for the frontend and simplifying service orchestration.
*   **On-Premises Deployment:** All core components are deployed locally via Docker Compose. - _Rationale:_ Directly addresses NFR2 and NFR3 by ensuring data security and intellectual property protection, as all sensitive data remains within the local datacenter.
