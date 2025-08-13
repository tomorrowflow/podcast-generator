# AI Podcast Generator Fullstack Architecture Document

This document outlines the complete fullstack architecture for AI Podcast Generator, including backend systems, frontend implementation, and their integration. It serves as the single source of truth for AI-driven development, ensuring consistency across the entire technology stack.

This unified approach combines what would traditionally be separate backend and frontend architecture documents, streamlining the development process for modern fullstack applications where these concerns are increasingly intertwined.

### Change Log

| Date | Version | Description | Author |
| :--------- | :------ | :----------------- | :----- |
| 2025-08-12 | 1.0 | Initial draft of PRD | John |

## High Level Architecture

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

## Tech Stack

| Category | Technology | Version | Purpose | Rationale |
| :--------- | :--------- | :------ | :-------- | :-------- |
| Frontend Language | TypeScript | Latest Stable | Type-safe development | Enhances code quality, maintainability, and scalability for the web application, especially with `magic-mcp` components. |
| Frontend Framework | React | Latest Stable | Build interactive UIs | A widely adopted, component-based library compatible with `21st-dev/magic-mcp` for efficient UI development. |
| UI Component Library | 21st-dev/magic-mcp compatible components | N/A | UI component generation | Directly supports FR8 and NFR10, simplifying UI development and maintaining consistency. |
| State Management | React Context API | N/A | Localized state management | Sufficient for MVP's simplified user management and avoids external dependencies for quick prototyping. |
| Backend Language | Python | 3.9+ | Main backend logic | Explicitly stated in NFR5 for `podcastfy` backend, supporting integration with LLM/TTS services. |
| Backend Framework | FastAPI | Latest Stable | Build REST APIs | Modern, fast, and asynchronous web framework for Python, suitable for API-driven microservices architecture (implied by `fast_app.py`), easy to integrate with Docker. |
| API Style | REST | N/A | Standardized communication | Common for microservices, provides clear, stateless communication between frontend, `podcastfy` backend, and other services. |
| Database | Local File System | N/A | Audio file storage | For MVP, generated audio files will be stored directly on the local filesystem, aligning with the on-premises deployment and diagram. |
| Cache | None (for MVP) | N/A | Minimal complexity for MVP | Caching is not a critical performance bottleneck for current MVP requirements and can be added later if needed. |
| File Storage | Local File System | N/A | Persistent storage for generated podcasts | Directly supports the storage and retrieval of generated audio files within the local environment. |
| Authentication | Basic Local Authentication | N/A | Simple user management | Aligns with NFR12 for initial MVP user management, providing simple access control without external dependencies. |
| Frontend Testing | Jest, React Testing Library | Latest Stable | Unit/Component testing | Standard testing tools for React applications, ensuring component functionality and UI integrity. |
| Backend Testing | Pytest | Latest Stable | Unit/Integration testing | Widely used Python testing framework, suitable for robust testing of the `podcastfy` backend logic and integrations. |
| E2E Testing | Playwright | Latest Stable | End-to-end testing | Supports cross-browser testing for the full user journey, ensuring the complete system works as expected from UI to backend. |
| Build Tool | Docker | Latest Stable | Containerization | Central to NFR1 and NFR2, ensuring all core components are isolated, portable, and runnable in the on-premises environment. |
| Bundler | Vite | Latest Stable | Frontend asset bundling | Modern and fast build tool for the frontend, optimized for development experience and production performance. |
| IaC Tool | Docker Compose | Latest Stable | Local orchestration | Facilitates the definition and running of multi-container Docker applications on the gaming PC, streamlining local development and deployment (per NFR2). |
| CI/CD | Manual for MVP / Git hooks | N/A | Initial deployment process | For MVP, CI/CD will be manual, potentially using simple Git hooks for basic automation, with future plans for full CI/CD systems like GitHub Actions. |
| Monitoring | Basic Docker Logs | N/A | Initial monitoring | For MVP, relying on standard Docker logging mechanisms for basic insight into container health and application output. |
| Logging | Basic Python Logging / Console Logs | N/A | Application logging | Standard logging practices within Python backend and frontend console for initial debugging and operational visibility. |
| CSS Framework | Tailwind CSS | Latest Stable | Utility-first styling | Provides a flexible and efficient way to style the frontend application, complementary to React development. |

## Data Models

**Data Model: Podcast**

**Purpose:** Represents a generated audio podcast, containing the audio file itself and associated metadata.

**Key Attributes:**
*   `id`: `string` - Unique identifier for the podcast.
*   `title`: `string` - User-provided or AI-generated title of the podcast.
*   `audioUrl`: `string` - URL or path to the stored audio file.
*   `duration`: `number` - Duration of the podcast in seconds.
*   `generatedDate`: `string` (ISO 8601) - Timestamp of when the podcast was generated.
*   `sourceText`?: `string` - Original text used to generate the podcast (optional).
*   `sourceAudioUrl`?: `string` - URL or path to the original audio input (optional, if generated from audio).
*   `templateUsed`?: `string` - Identifier of the template used for generation (optional).

**TypeScript Interface:**
```typescript
interface Podcast {
  id: string;
  title: string;
  audioUrl: string;
  duration: number;
  generatedDate: string; // ISO 8601 format
  sourceText?: string;
  sourceAudioUrl?: string;
  templateUsed?: string;
}
```

**Relationships:**
*   A Podcast is generated from `Text Input` OR `Audio Input`.
*   A Podcast may be associated with a `Podcast Template`.
*   A Podcast is owned by a `User`.

**Data Model: User**

**Purpose:** Represents a user of the AI Podcast Generator, primarily for basic authentication (per NFR12).

**Key Attributes:**
*   `id`: `string` - Unique identifier for the user.
*   `username`: `string` - User's unique username for login.
*   `passwordHash`: `string` - Hashed password for security.
*   `email`?: `string` - Optional user email.

**TypeScript Interface:**
```typescript
interface User {
  id: string;
  username: string;
  passwordHash: string;
  email?: string;
}
```

**Relationships:**
*   A User can generate multiple `Podcasts`.

**Data Model: PodcastTemplate**

**Purpose:** Represents a pre-designed template that guides the generation of podcasts (per FR10, Story 3.1, Story 3.2).

**Key Attributes:**
*   `id`: `string` - Unique identifier for the template.
*   `name`: `string` - Display name of the template (e.g., "Interview", "News Update").
*   `description`: `string` - Brief explanation of what the template does.
*   `llmPromptInstructions`: `string` - Specific instructions for the LLM based on the template.
*   `ttsConfig`?: `object` - Optional configuration for TTS (e.g., specific voice, style).

**TypeScript Interface:**
```typescript
interface PodcastTemplate {
  id: string;
  name: string;
  description: string;
  llmPromptInstructions: string;
  ttsConfig?: {
    // Define specific TTS config properties here, e.g.,
    voiceId?: string;
    style?: string;
  };
}
```

**Relationships:**
*   A `Podcast` can be generated using a `PodcastTemplate`.

## API Specification

**REST API Specification**

```yaml
openapi: 3.0.0
info:
  title: AI Podcast Generator API
  version: 1.0.0
  description: API for managing and generating podcasts
servers:
  - url: http://localhost:8000/api/v1
    description: Development server (local Docker Compose)
paths:
  /podcasts/generate/text:
    post:
      summary: Generate a podcast from text
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                text:
                  type: string
                  description: The text content to convert to a podcast.
                templateId:
                  type: string
                  nullable: true
                  description: Optional ID of a podcast template to use.
      responses:
        '200':
          description: Podcast generation initiated successfully.
          content:
            application/json:
              schema:
                type: object
                properties:
                  podcastId:
                    type: string
                    description: The ID of the newly initiated podcast generation.
                  status:
                    type: string
                    description: Current status of the generation process.
        '400':
          description: Invalid input
  /podcasts/generate/audio:
    post:
      summary: Generate a podcast from an audio file
      requestBody:
        required: true
        content:
          multipart/form-data:
            schema:
              type: object
              properties:
                audioFile:
                  type: string
                  format: binary
                  description: The audio file to transcribe and convert to a podcast.
                templateId:
                  type: string
                  nullable: true
                  description: Optional ID of a podcast template to use.
      responses:
        '200':
          description: Podcast generation initiated successfully.
          content:
            application/json:
              schema:
                type: object
                properties:
                  podcastId:
                    type: string
                    description: The ID of the newly initiated podcast generation.
                  status:
                    type: string
                    description: Current status of the generation process.
        '400':
          description: Invalid input or unsupported audio format
  /podcasts/{podcastId}:
    get:
      summary: Get podcast status and details
      parameters:
        - in: path
          name: podcastId
          schema:
            type: string
          required: true
          description: ID of the podcast to retrieve.
      responses:
        '200':
          description: Podcast details retrieved.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Podcast'
        '404':
          description: Podcast not found
  /podcasts/{podcastId}/download:
    get:
      summary: Download generated podcast audio
      parameters:
        - in: path
          name: podcastId
          schema:
            type: string
          required: true
          description: ID of the podcast to download.
      responses:
        '200':
          description: Audio file downloaded.
          content:
            audio/mpeg:
              schema:
                type: string
                format: binary
        '404':
          description: Podcast not found or audio not yet generated
components:
  schemas:
    Podcast:
      type: object
      properties:
        id:
          type: string
        title:
          type: string
        audioUrl:
          type: string
        duration:
          type: number
        generatedDate:
          type: string
          format: date-time
        sourceText:
          type: string
          nullable: true
        sourceAudioUrl:
          type: string
          nullable: true
        templateUsed:
          type: string
          nullable: true
      required:
        - id
        - title
        - audioUrl
        - duration
        - generatedDate
```

## Components

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

## External APIs

No external APIs are needed for this project. All core AI services (`Ollama`, `Kyutai TTS`, `Whisper ASR Webservice`) are designed to be deployed locally via Docker Compose and integrated directly with the `podcastfy` backend within the secure, on-premises environment. This design choice explicitly fulfills the non-functional requirement (NFR3) of maintaining zero instances of sensitive internal data exposure to external services. Therefore, there are no third-party APIs that require separate documentation, authentication, or rate limit considerations in this architecture.

## Core Workflows

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

## Database Schema

**1. Podcast Data Storage (Local File System)**

Generated podcast audio files will be stored directly on the local file system, accessible by the `podcastfy` backend. Alongside each audio file, a corresponding metadata file (e.g., JSON) will store the `Podcast` object's attributes.

*   **File Structure Example:**
    ```plaintext
    /data/podcasts/
    ├── <podcast_id>.mp3
    └── <podcast_id>.json
    ```

*   **`podcast_id.json` Content Example:**
    ```json
    {
      "id": "7b445bb2a1514503911a74a124d4a648",
      "title": "Introduction to AI Podcast Generation",
      "audioUrl": "/data/podcasts/7b445bb2a1514503911a74a124d4a648.mp3",
      "duration": 360,
      "generatedDate": "2025-08-12T10:30:00Z",
      "sourceText": "The original text content from which this podcast was generated...",
      "templateUsed": "interview-style"
    }
    ```

---

**2. User Data Management (In-Memory / Simple File-Based for MVP)**

For initial MVP user management (NFR12), user data can be stored in a simple configuration file or managed in-memory by the `podcastfy` backend or a dedicated authentication service. A simple JSON file or equivalent would suffice for a few local users.

*   **Conceptual File (e.g., `users.json`) Content Example:**
    ```json
    [
      {
        "id": "user-001",
        "username": "admin",
        "passwordHash": "hashedpassword123",
        "email": "admin@example.com"
      },
      {
        "id": "user-002",
        "username": "creator",
        "passwordHash": "hashedpasswordabc"
      }
    ]
    ```

---

**3. Podcast Template Data Management (Configuration File)**

Podcast templates, primarily consisting of LLM prompt instructions and optional TTS configurations, will likely be defined in a configuration file accessible by the `podcastfy` backend.

*   **Conceptual File (e.g., `templates.json` or `config.yaml`) Content Example:**
    ```json
    [
      {
        "id": "interview-style",
        "name": "Interview Style",
        "description": "Generates a podcast transcript formatted as an interview between two speakers.",
        "llmPromptInstructions": "Generate a podcast script as an interview. Include two distinct speakers, 'Host' and 'Guest'. Ensure realistic dialogue flow and conversational tone. The topic is: {{TEXT_INPUT}}",
        "ttsConfig": {
          "voiceId": "en-US-Standard-C",
          "style": "conversational"
        }
      },
      {
        "id": "news-update",
        "name": "News Update",
        "description": "Formats the content as a concise news update, delivered by a single announcer.",
        "llmPromptInstructions": "Summarize the following text into a news update script, ideally no longer than 300 words. Use a formal, objective tone. The content is: {{TEXT_INPUT}}",
        "ttsConfig": {
          "voiceId": "en-US-Wavenet-A",
          "style": "read-aloud"
        }
      }
    ]
    ```

## Frontend Architecture

**1. Component Architecture**

**Component Organization:**
```text
src/
├── app/                     # Main application structure
├── components/              # Reusable UI components (e.g., Button, Modal)
├── features/                # Feature-specific components and logic (e.g., PodcastGenerator, PodcastList)
│   ├── PodcastGenerator/
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── slices/          # State management slices (if using Redux Toolkit/Zustand)
│   │   └── index.ts
│   └── PodcastList/
│       ├── components/
│       └── index.ts
├── hooks/                   # General custom React hooks (e.g., useAuth, useApi)
├── pages/                   # Route-level components (e.g., HomePage, DashboardPage)
├── services/                # API client and logic for communicating with backend
├── stores/                  # Global state management (e.g., context providers, Zustand store)
├── styles/                  # Global styles, Tailwind CSS configuration
├── utils/                   # General utility functions
├── assets/                  # Static assets (images, icons)
├── types/                   # Shared TypeScript types for frontend (could be from packages/shared)
└── main.tsx                 # Entry point
```

**Component Template:**
```typescript
// src/components/ExampleComponent/ExampleComponent.tsx
import React from 'react';

interface ExampleComponentProps {
  // Define component props here
  title: string;
  description?: string;
}

const ExampleComponent: React.FC<ExampleComponentProps> = ({ title, description }) => {
  return (
    <div className="p-4 bg-white shadow rounded-lg">
      <h2 className="text-xl font-semibold mb-2">{title}</h2>
      {description && <p className="text-gray-600">{description}</p>}
      {/* Additional UI elements */}
    </div>
  );
};

export default ExampleComponent;

// src/components/ExampleComponent/index.ts (barrel file for easy import)
export { default } from './ExampleComponent';
export type { ExampleComponentProps } from './ExampleComponent';
```

---

**2. State Management Architecture**

**State Structure:**
```typescript
// src/stores/podcastStore.ts (Example using a Zustand-like pattern)
import create from 'zustand';
import { Podcast } from '../types'; // Assuming shared types are in `src/types` or `packages/shared/types`

interface PodcastState {
  podcasts: Podcast[];
  currentGeneratingPodcastId: string | null;
  generationProgress: {
    podcastId: string;
    stage: string;
    percentage: number;
    logs: string[];
  } | null;
  addPodcast: (podcast: Podcast) => void;
  updateGenerationProgress: (podcastId: string, stage: string, percentage: number, newLog?: string) => void;
  resetGenerationProgress: () => void;
  // ... other actions like fetchPodcasts, deletePodcast
}

export const usePodcastStore = create<PodcastState>((set) => ({
  podcasts: [],
  currentGeneratingPodcastId: null,
  generationProgress: null,
  addPodcast: (podcast) => set((state) => ({ podcasts: [...state.podcasts, podcast] })),
  updateGenerationProgress: (podcastId, stage, percentage, newLog) => set((state) => ({
    currentGeneratingPodcastId: podcastId,
    generationProgress: {
      podcastId,
      stage,
      percentage,
      logs: newLog ? [...(state.generationProgress?.logs || []), newLog] : (state.generationProgress?.logs || []),
    },
  })),
  resetGenerationProgress: () => set({ generationProgress: null, currentGeneratingPodcastId: null }),
}));

// src/stores/authStore.ts (for basic user state)
interface AuthState {
  user: { id: string; username: string } | null;
  isLoggedIn: boolean;
  login: (user: { id: string; username: string }) => void;
  logout: () => void;
}

export const useAuthStore = create<AuthState>((set) => ({
  user: null,
  isLoggedIn: false,
  login: (user) => set({ user, isLoggedIn: true }),
  logout: () => set({ user: null, isLoggedIn: false }),
}));
```

**State Management Patterns:**
- **Centralized Store:** A single source of truth for application state (e.g., `usePodcastStore`, `useAuthStore`).
- **Updater Functions:** State updates are done via explicit updater functions (e.g., `addPodcast`, `updateGenerationProgress`) rather than direct state mutation, ensuring immutability.
- **Derived State:** Compute derived state (e.g., `isLoggedIn`) from raw state for efficiency and consistency.
- **Asynchronous Actions:** Handle API calls and other asynchronous operations within the store actions or separate service layers that then update the store.

---

**3. Routing Architecture**

**Route Organization:**
```text
src/
├── pages/
│   ├── index.tsx          # Homepage/Dashboard (e.g., displays recent podcasts)
│   ├── generate/
│   │   ├── index.tsx      # Text/Audio submission form
│   │   └── [id].tsx       # Dynamic route for podcast generation progress/details
│   ├── podcasts/
│   │   ├── index.tsx      # List of generated podcasts (e.g., for management)
│   │   └── [id].tsx       # Individual podcast playback/download page
│   ├── auth/
│   │   ├── login.tsx
│   │   └── register.tsx
│   └── _app.tsx           # Global App component/layout
└── AppRouter.tsx          # Centralized routing configuration
```

**Protected Route Pattern:**
```typescript
// src/components/ProtectedRoute.tsx
import React from 'react';
import { useAuthStore } from '../stores/authStore';
import { Navigate, Outlet } from 'react-router-dom'; // Assuming React Router Dom

interface ProtectedRouteProps {
  allowedRoles?: string[]; // Optional role-based access
}

const ProtectedRoute: React.FC<ProtectedRouteProps> = ({ allowedRoles }) => {
  const isLoggedIn = useAuthStore((state) => state.isLoggedIn);
  const user = useAuthStore((state) => state.user); // Get user to check roles

  if (!isLoggedIn) {
    // Redirect unauthenticated users to login page
    return <Navigate to="/auth/login" replace />;
  }

  // Optional: Check for roles if allowedRoles is provided
  // if (allowedRoles && user && !allowedRoles.includes(user.role)) {
  //   return <Navigate to="/unauthorized" replace />;
  // }

  return <Outlet />; // Render child routes if authenticated
};

export default ProtectedRoute;

// Usage in AppRouter.tsx
// <Route element={<ProtectedRoute />}>
//   <Route path="/generate" element={<GeneratePodcastPage />} />
//   <Route path="/podcasts" element={<PodcastListPage />} />
// </Route>
```

---

**4. Frontend Services Layer**

**API Client Setup:**
```typescript
// src/services/api.ts
import axios from 'axios';

const API_BASE_URL = import.meta.env.VITE_API_BASE_URL || 'http://localhost:8000/api/v1';

const apiClient = axios.create({
  baseURL: API_BASE_URL,
  headers: {
    'Content-Type': 'application/json',
    // 'Authorization': `Bearer ${token}` // Add token if authentication implemented
  },
});

// Interceptor for handling global errors (e.g., network issues)
apiClient.interceptors.response.use(
  response => response,
  error => {
    console.error('API call error:', error);
    // You could show a global error notification here
    return Promise.reject(error);
  }
);

export default apiClient;
```

**Service Example:**
```typescript
// src/services/podcastService.ts
import apiClient from './api';
import { Podcast } from '../types'; // Shared type

interface GenerateTextRequest {
  text: string;
  templateId?: string;
}

interface GenerateAudioFormData extends FormData {
  audioFile: File;
  templateId?: string;
}

interface PodcastGenerationResponse {
  podcastId: string;
  status: string;
}

const podcastService = {
  generatePodcastFromText: async (data: GenerateTextRequest): Promise<PodcastGenerationResponse> => {
    const response = await apiClient.post<PodcastGenerationResponse>('/podcasts/generate/text', data);
    return response.data;
  },

  generatePodcastFromAudio: async (data: GenerateAudioFormData): Promise<PodcastGenerationResponse> => {
    const response = await apiClient.post<PodcastGenerationResponse>('/podcasts/generate/audio', data, {
      headers: {
        'Content-Type': 'multipart/form-data', // Important for file uploads
      },
    });
    return response.data;
  },

  getPodcastDetails: async (podcastId: string): Promise<Podcast> => {
    const response = await apiClient.get<Podcast>(`/podcasts/${podcastId}`);
    return response.data;
  },

  downloadPodcast: async (podcastId: string): Promise<Blob> => {
    const response = await apiClient.get(`/podcasts/${podcastId}/download`, {
      responseType: 'blob', // Important for binary data like audio
    });
    return response.data;
  },

  // Example for streaming logs/progress (conceptually, actual implementation might use WebSockets/SSE)
  // watchPodcastGeneration: (podcastId: string, onUpdate: (progress: any) => void) => {
  //   // This would typically involve WebSockets or Server-Sent Events (SSE)
  //   // For MVP polling on /podcasts/{podcastId} is assumed sufficient (FR9)
  // }
};

export default podcastService;
```

## Backend Architecture

**1. Service Architecture**

*   **Function Organization (for an implied serverless/microservice pattern within Docker):**

    ```text
    src/
    ├── podcastfy/               # The main podcastfy backend application
    │   ├── api/                 # FastAPI application and API routes
    │   │   ├── __init__.py
    │   │   └── fast_app.py      # Main FastAPI app
    │   │   └── routes/          # API endpoint definitions (e.g., generate, retrieve)
    │   ├── core/                # Core business logic and service orchestration
    │   │   └── __init__.py
    │   │   └── generation_service.py # Orchestrates LLM, TTS, ASR interactions
    │   │   └── podcast_management.py # Handles podcast metadata and file storage
    │   ├── tts/                 # Interface for Text-to-Speech providers
    │   │   ├── __init__.py
    │   │   ├── base.py
    │   │   └── factory.py       # Chooses TTS provider based on config
    │   │   └── providers/       # Specific TTS provider implementations (e.g., kyutai_tts_provider.py)
    │   ├── llm/                 # Interface for LLM providers
    │   │   ├── __init__.py
    │   │   └── ollama_client.py # Client for Ollama API
    │   ├── asr/                 # Interface for ASR providers
    │   │   ├── __init__.py
    │   │   └── whisper_client.py # Client for Whisper ASR Webservice
    │   ├── utils/               # Utility functions (logger, config management)
    │   ├── config.py            # Application configurations
    │   └── dependencies.py      # Dependency injection setup (e.g., for FastAPI)
    ├── docker-compose.yml       # Root level for all service definitions
    └── Dockerfile_api           # Dockerfile for the podcastfy backend API
    ```

---

**2. Database Architecture**

*   **Schema Design (Conceptual - for Filesystem Data):**

    *   **Podcast Metadata (JSON files):**
        ```json
        {
          "id": "string",
          "title": "string",
          "audioUrl": "string",
          "duration": "number",
          "generatedDate": "string (ISO 8601)",
          "sourceText": "string (nullable)",
          "sourceAudioUrl": "string (nullable)",
          "templateUsed": "string (nullable)"
        }
        ```

    *   **User Configuration (JSON file for MVP):**
        ```json
        [
          {"id": "string", "username": "string", "passwordHash": "string", "email": "string (nullable)"}
        ]
        ```

    *   **Template Configuration (JSON/YAML file):**
        ```yaml
        - id: "string"
          name: "string"
          description: "string"
          llmPromptInstructions: "string"
          ttsConfig: # Optional
            voiceId: "string"
            style: "string"
        ```

*   **Data Access Layer (Conceptual - within `podcastfy` backend):**

    ```typescript
    // src/podcastfy/core/podcast_management.py (Conceptual - Python)

    class PodcastManager:
        def __init__(self, storage_path="/data/podcasts"):
            self.storage_path = storage_path

        def save_podcast(self, podcast_id: str, audio_data: bytes, metadata: dict):
            # Save .mp3 file
            with open(f"{self.storage_path}/{podcast_id}.mp3", "wb") as f:
                f.write(audio_data)
            # Save .json metadata file
            with open(f"{self.storage_path}/{podcast_id}.json", "w") as f:
                json.dump(metadata, f, indent=2)

        def get_podcast_metadata(self, podcast_id: str) -> dict:
            # Load .json metadata
            with open(f"{self.storage_path}/{podcast_id}.json", "r") as f:
                return json.load(f)

        def stream_podcast_audio(self, podcast_id: str):
            # Stream .mp3 file directly (e.g., using FastAPI StreamingResponse)
            pass

    # Similar conceptual classes for UserManager and TemplateManager
    ```

---

**3. Authentication and Authorization**

*   **Auth Flow (Conceptual):**

    ```mermaid
    sequenceDiagram
        actor User
        participant FrontendApp as Web Application
        participant podcastfyBackend as podcastfy Backend API

        User->>FrontendApp: Enter Username/Password
        FrontendApp->>podcastfyBackend: POST /auth/login ({username, password})
        activate podcastfyBackend
        podcastfyBackend->>podcastfyBackend: Validate credentials (e.g., against users.json)
        alt Successful Login
            podcastfyBackend-->>FrontendApp: Return Session Token (JWT)
            FrontendApp->>User: Redirect to Dashboard / Set Session
            User->>FrontendApp: Access Protected Route
            FrontendApp->>podcastfyBackend: Request with Session Token
            podcastfyBackend->>podcastfyBackend: Verify Session Token
            podcastfyBackend-->>FrontendApp: Grant Access / Data
        else Failed Login
            podcastfyBackend-->>FrontendApp: Return 401 Unauthorized / Error Message
            FrontendApp->>User: Display Login Error
        end
    ```

*   **Middleware/Guards (Conceptual - Python/FastAPI):**

    ```typescript
    # src/podcastfy/api/dependencies.py (Conceptual)
    from fastapi import Depends, HTTPException, Header, status

    async def get_current_user_id(authorization: str = Header(...)):
        # For MVP, a very basic token verification
        # In production, this would validate JWT signature, expiry, etc.
        if authorization == "Bearer VALID_TOKEN": # Replace with actual token validation logic
            return "user-001" # Return actual user ID
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Invalid authentication credentials",
            headers={"WWW-Authenticate": "Bearer"},
        )

    # Usage in FastAPI route:
    # @router.post("/podcasts/generate/text")
    # async def generate_text_podcast(user_id: str = Depends(get_current_user_id)):
    #     # ... logic
    ```

## Unified Project Structure

```plaintext
AI-Podcast-Generator/
├── .github/                    # CI/CD workflows (if implemented later)
│   └── workflows/
│       ├── ci.yaml
│       └── deploy.yaml
├── apps/                       # Application packages
│   ├── web-app/                # Frontend application (React with Vite/Tailwind)
│   │   ├── src/
│   │   │   ├── components/     # UI components (potentially enhanced by 21st-dev/magic-mcp)
│   │   │   ├── features/       # Feature-specific components and logic
│   │   │   ├── hooks/          # Custom React hooks
│   │   │   ├── pages/          # Route-level components
│   │   │   ├── services/       # API client services (e.g., podcastService.ts)
│   │   │   ├── stores/         # State management (e.g., Zustand stores)
│   │   │   ├── styles/         # Global styles/themes, Tailwind CSS config
│   │   │   ├── utils/          # Frontend utilities
│   │   │   └── main.tsx        # Entry point
│   │   ├── public/             # Static assets
│   │   ├── tests/              # Frontend tests (Jest, React Testing Library)
│   │   ├── package.json        # Frontend package manifest
│   │   └── vite.config.ts      # Vite configuration
│   └── api/                    # API gateway / orchestration layer for podcastfy backend (FastAPI)
│       ├── src/
│       │   ├── routes/         # API routes/controllers (FastAPI routers)
│       │   ├── core/           # Business logic, generation orchestration
│       │   ├── tts/            # TTS provider interfaces
│       │   ├── llm/            # LLM provider interfaces
│       │   ├── asr/            # ASR provider interfaces
│       │   ├── config/         # Backend configurations
│       │   ├── dependencies.py # FastAPI dependency injection
│       │   └── main.py         # FastAPI application entry point
│       ├── tests/              # Backend tests (Pytest)
│       ├── requirements.txt    # Python dependencies for API
│       └── Dockerfile          # Dockerfile for FastAPI service
├── packages/                   # Shared packages
│   ├── shared-types/           # Shared TypeScript interfaces and utility types
│   │   ├── src/
│   │   │   ├── index.ts
│   │   │   └── types.ts        # e.g., Podcast.ts, User.ts
│   │   └── package.json
│   ├── ui-components/          # Shared UI components (if needed, e.g., via Storybook)
│   │   ├── src/
│   │   └── package.json
│   └── config/                 # Shared configurations (e.g., ESLint, Prettier, TSConfig)
│       ├── eslint/
│       ├── typescript/
│       └── jest/
├── services/                   # Dockerized AI services and podcastfy core
│   ├── podcastfy/              # Original podcastfy source (potentially as a separate package/submodule or directly copied)
│   │   ├── podcastfy/
│   │   ├── Dockerfile          # Original podcastfy Dockerfile
│   │   └── ...                 # All other podcastfy source files
│   ├── ollama/                 # Ollama service definition (e.g., custom models, config)
│   │   └── Dockerfile          # Ollama Docker setup (if custom)
│   └── whisper-asr-webservice/ # Whisper ASR service (Dockerized)
│       └── Dockerfile          # Whisper ASR Docker setup
├── infrastructure/             # IaC definitions (e.g., for cloud deployment if expanded)
│   └── docker-compose.yml      # Main Docker Compose file at monorepo root
├── scripts/                    # Build, deploy, and utility scripts
├── tools/                      # Monorepo management tools (e.g., Nx, Turborepo configs)
├── .env.example                # Environment variables template
├── package.json                # Root package.json (for monorepo tool and shared scripts)
├── {{monorepo_config}}         # Monorepo configuration file (e.g., turborepo.json, nx.json)
└── README.md
```

## Development Workflow

**1. Local Development Setup**

**Prerequisites:**
```bash
# General system tools
brew install git # macOS
sudo apt-get install git # Debian/Ubuntu

# Install Docker Desktop (includes Docker Engine and Docker Compose)
# Follow instructions: https://docs.docker.com/desktop/install/
# Ensure Docker Desktop is running.

# Install NVIDIA Container Toolkit (for GPU access within Docker)
# Follow instructions: https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html
# Verify installation: docker run --rm --gpus all ubuntu nvidia-smi

# Node.js (for frontend development and monorepo tooling)
# Use nvm for flexible version management
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
nvm install --lts # Install latest LTS version
nvm use --lts
node -v
npm -v

# Python (for backend development)
# Use pyenv for flexible version management if not relying solely on Docker for dev
# curl https://pyenv.run | bash
# pyenv install 3.9 # Or desired Python version
# pyenv global 3.9
python3 -V
pip3 -V

# (Optional, based on chosen monorepo tool - e.g., Turborepo, Nx)
npm install -g turbo # or nx
```

**Initial Setup:**
```bash
# 1. Clone the monorepo
git clone https://github.com/your-org/AI-Podcast-Generator.git # Or your actual repo URL
cd AI-Podcast-Generator

# 2. Install root monorepo dependencies (if using a JavaScript-based monorepo tool)
npm install # Or `yarn install`, `pnpm install`

# 3. Build shared packages (if necessary for internal dependencies - e.g., shared-types)
# turbo run build --filter=shared-types # Example for Turborepo
# or a simple npm/yarn/pnpm build script in packages/shared-types/package.json

# 4. Copy environment variable template
cp .env.example .env

# 5. Build and start all Docker services for the first time
docker compose up --build -d # --build ensures images are fresh, -d runs in detached mode
```

**Development Commands:**
```bash
# Start all services (frontend, backend, AI services) in detached mode
docker compose up -d

# Stop all running services
docker compose down

# Rebuild and restart specific services (e.g., after code changes in backend)
docker compose up --build -d web-app api # Replace with actual service names from docker-compose.yml

# Check logs for a specific service (e.g., podcastfy backend API)
docker compose logs -f api

# Run frontend development server (if not run via docker-compose)
# cd apps/web-app
# npm run dev # Or `yarn dev`, `pnpm dev`

# Run backend development server (if not run via docker-compose - usually started with docker-compose)
# cd apps/api
# poetry run uvicorn src.main:app --reload --port 8000 # Example for FastAPI

# Run tests for a specific application (e.g., frontend unit tests)
# cd apps/web-app && npm test
# cd apps/api && poetry run pytest # Example for Python backend

# Run end-to-end tests (requires all services to be up)
# cd apps/web-app && npm run test:e2e # Or equivalent script
```

---

**2. Environment Configuration**

**Required Environment Variables:**
```bash
# --- Shared Environment Variables (monorepo root .env file) ---
# This file should NOT be committed to version control.
# Create it by copying .env.example

# Base URL for the podcastfy backend API
VITE_API_BASE_URL=http://localhost:8000/api/v1 # For frontend (Vite-specific prefix)
API_BASE_URL=http://localhost:8000/api/v1     # For backend (FastAPI)

# Local ollama service URL
OLLAMA_HOST=http://ollama-service:11434 # Used by podcastfy backend inside Docker network

# Local kyutai TTS service URL
KYUTAI_TTS_HOST=http://kyutai-tts-service:5002 # Used by podcastfy backend inside Docker network

# Local whisper ASR webservice URL
WHISPER_ASR_HOST=http://whisper-asr-service:9000 # Used by podcastfy backend inside Docker network

# Secret for authentication (for MVP, a simple static key)
APP_SECRET_KEY=YOUR_SUPER_SECRET_KEY_HERE # CHANGE THIS IN PRODUCTION

# --- Frontend specific (.env.local in web-app directory) ---
# Can be used for frontend-only environment variables if needed
# VITE_SOME_FRONTEND_SETTING=value

# --- Backend specific (e.g., in apps/api/.env or managed by Poetry/FastAPI settings) ---
# Can be used for backend-only environment variables if needed
# PYTHON_DEBUG=1
```

## Deployment Architecture

**1. Deployment Strategy**

Deployment will leverage Docker Compose for orchestrating the multi-container application on the local gaming PC.

*   **Frontend Deployment (`web-app`):**
    *   **Platform:** Docker Container (within local Docker Compose)
    *   **Build Command:** `npm run build` (executed within the Dockerfile or build step, producing static assets)
    *   **Output Directory:** `dist/` (standard for Vite builds)
    *   **Serving Mechanism:** A lightweight web server (e.g., Nginx or directly served by the frontend framework's build output) within its Docker container.
    *   **CDN/Edge:** N/A (local deployment, no CDN involved).

*   **Backend Deployment (`api` - FastAPI/podcastfy wrapper):**
    *   **Platform:** Docker Container (within local Docker Compose)
    *   **Build Command:** `poetry install --no-root --no-dev && poetry build` (or `pip install -r requirements.txt`) followed by copying application code into the Docker image.
    *   **Deployment Method:** Run as a long-running Uvicorn server process inside its Docker container, managed by Docker Compose.

---

**2. CI/CD Pipeline**

For the MVP, CI/CD will be manual, primarily managed via direct Docker Compose commands. This approach is practical for a single-machine, internal-facing application. Future iterations could integrate more sophisticated CI/CD tooling like GitHub Actions or Jenkins.

*   **CI/CD Pipeline Configuration (Conceptual - Manual / Scripted):**

    ```yaml
    # Example of a conceptual build and deploy script (NOT a full YAML pipeline)
    # This would reside in scripts/deploy.sh or similar

    # Manual Steps for MVP CI/CD:
    # 1. Developer pushes code to Git repository (e.g., main branch).
    # 2. Developer pulls latest changes on the Gaming PC: `git pull origin main`
    # 3. Developer navigates to project root: `cd AI-Podcast-Generator`
    # 4. Build and deploy all services: `docker compose up --build -d --force-recreate`
    #    - `--build`: ensures Docker images are rebuilt from latest code.
    #    - `-d`: runs containers in detached mode.
    #    - `--force-recreate`: forces recreation of containers to pick up new images if old ones are found

    # Or for a specific service update:
    # `docker compose up --build -d <service_name>` (e.g., `api` or `web-app`)
    ```

---

**3. Environments**

For the MVP, there are two primary environments: a local development environment (local machine where developers code) and the single "production" like environment on the gaming PC. While not strictly separate "environments" in the cloud sense, it's important to define their access points.

*   **Environments Table:**

| Environment | Frontend URL        | Backend URL           | Purpose                                  |
| :---------- | :------------------ | :-------------------- | :--------------------------------------- |
| Development | `http://localhost:3000` (or Vite's default) | `http://localhost:8000/api/v1` | Local coding, testing, and debugging. |
| Production  | `http://<gaming-pc-ip>:3000` | `http://<gaming-pc-ip>:8000/api/v1` | Stable, internal deployment for actual use. |

## Security and Performance

**1. Security Requirements**

*   **Frontend Security:**
    *   **CSP Headers:** Will be configured minimally for the MVP, primarily allowing scripts and styles from the application's own domain. This will be integrated into the web server serving the frontend (e.g., Nginx in a Docker container).
        *   _Recommendation:_ `Content-Security-Policy: default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline';` (Fine-tune `unsafe-inline` if necessary).
    *   **XSS Prevention:** Frontend framework (React) inherently provides protection against XSS by escaping rendered content. Additional measures will involve strict input sanitization on both frontend and backend for any user-generated content.
    *   **Secure Storage:** Authentication tokens (JWTs) will be stored in `HttpOnly` cookies to prevent JavaScript access and XSS attacks. Sensitive user data will not be stored client-side.

*   **Backend Security:**
    *   **Input Validation:** Strict server-side input validation will be enforced for all API endpoints using FastAPI's Pydantic models. This is critical for preventing injection attacks and ensuring data integrity.
    *   **Rate Limiting:** IP-based rate limiting will be implemented on key API endpoints (e.g., `/podcasts/generate/text`, `/auth/login`) to prevent abuse and denial-of-service attacks.
        *   _Implementation:_ FastAPI middleware or integrated with a reverse proxy (e.g., Nginx).
    *   **CORS Policy:** A strict CORS policy will be configured to allow requests only from the frontend application's origin(s) (`http://localhost:3000` for development, `http://<gaming-pc-ip>:3000` for "production").

*   **Authentication Security:**
    *   **Token Storage:** Authentication tokens will be stored in `HttpOnly` and `Secure` cookies. This ensures tokens are not accessible via client-side JavaScript, mitigating XSS risks, and are only sent over HTTPS.
    *   **Session Management:** Token-based session management (likely JWTs as discussed) will be stateless on the backend, relying on token validation for each request. Token expiration will be enforced.
    *   **Password Policy:** For MVP, a basic password policy will involve hashing and salting passwords (e.g., using `bcrypt`). Future iterations can enforce complexity requirements (min length, special chars, etc.).

*   **Infrastructure Security Considerations:**
    *   **Network Segmentation:**
        *   **External Access:** The `web-app` container will expose necessary HTTP/HTTPS ports (e.g., 80/443) only. Access should be restricted to internal network ranges or specific IPs.
        *   **Internal Communication:** `podcastfy` backend and AI services (Ollama, Kyutai TTS, Whisper ASR) will communicate over Docker's internal bridge network, which is isolated from the host and external networks by default. No ports for these internal services should be exposed directly to the host or external networks.
    *   **Host-Level Firewall:** Implement a host-level firewall (e.g., `ufw` on Linux, macOS built-in firewall) to only allow inbound connections on ports required by the `web-app` (e.g., 80/443, 22 for SSH management) and explicitly block all other incoming traffic.
    *   **Service Isolation and Least Privilege:**
        *   **Container Users:** Containers will run with non-root users where possible. Dockerfiles will specify `USER` instruction when appropriate base images support it.
        *   **Volume Permissions:** Mount volumes with the principle of least privilege, ensuring `podcastfy` backend container only has read/write access to its designated `/data/podcasts` volume.
        *   **Secrets Management:** Environment variables containing sensitive information (e.g., `APP_SECRET_KEY`) will be passed to containers securely via Docker Compose environment variables, `.env` files (for local dev), and never hardcoded.
        *   **Resource Limits:** Docker Compose configurations will define appropriate CPU and memory limits for containers to prevent resource exhaustion attacks.

---

**2. Performance Optimization**

*   **Frontend Performance:**
    *   **Bundle Size Target:** Aim for smaller JavaScript bundles (e.g., < 500KB gzipped) to ensure fast initial page loads. This will be managed through code splitting, lazy loading, and efficient bundling (Vite helps here).
    *   **Loading Strategy:** Implement lazy loading for routes and components that are not critical for initial render. Display loading indicators for asynchronous operations (e.g., podcast generation progress bar).
    *   **Caching Strategy:** Utilize browser caching for static assets (JavaScript, CSS, images) with appropriate cache-control headers. No aggressive caching of dynamic data for MVP.

*   **Backend Performance:**
    *   **Response Time Target:** Aim for API response times (excluding long-running AI generation) under 200-300ms for routine operations (e.g., fetching podcast list, user authentication).
    *   **Identified Bottlenecks:**
        *   **AI Model Inference:** The primary bottleneck is expected to be the time taken by Ollama, Kyutai TTS, and Whisper ASR for processing input and generating output, heavily reliant on GPU performance.
        *   **Audio File I/O:** Reading/writing large audio files to local storage can be a bottleneck.
    *   **Database Optimization:** For local file storage, optimize read/write operations by reading/writing only necessary data and performing any filtering/sorting in-memory for smaller datasets. For scaling beyond MVP, consider indexing and database-specific optimizations if a formal database is introduced.
    *   **Caching Strategy:** For MVP, no explicit backend caching (beyond OS-level file caching). If performance bottlenecks arise, consider in-memory caches (e.g., Redis) or database query caching.
    *   **Load Balancing (Future/Scaling):** For a single-machine MVP, load balancing is N/A. In a scaled deployment, internal load balancing (e.g., Nginx, Envoy proxy) would distribute API requests across multiple `podcastfy` backend instances.
    *   **Horizontal and Vertical Scaling Strategies (Future):**
        *   **Vertical Scaling:** Upgrade the underlying gaming PC's CPU, RAM, and especially GPU (VRAM) as initial scaling.
        *   **Horizontal Scaling:**
            *   **Backend API:** Deploy multiple `podcastfy` backend API instances behind a load balancer. FastAPI is designed for concurrency.
            *   **AI Services:** Deploy multiple instances of Ollama, Kyutai TTS, and Whisper ASR containers, each capable of utilizing GPU resources. This would require intelligent routing/load management at the `podcastfy` backend level.
            *   **Queuing:** Introduce a message queue (e.g., RabbitMQ, Kafka) for asynchronous processing of podcast generation requests, decoupling the request from the long-running AI tasks. This allows the backend API to respond quickly and process requests in the background.
    *   **Resource Sizing Recommendations (MVP):**
        *   **Gaming PC:** Minimum 8 CPU cores, 16 GB RAM, 8 GB VRAM (for GPU-accelerated AI models).
        *   **Disk Space:** Initial 100GB for audio file storage, scalable as needed.

---

**3. Data Security Details**

To enhance data security and address critical gaps, the following approaches will be adopted:

*   **Data Encryption:**
    *   **At Rest:** Local file storage (for podcast MP3s and JSON metadata) will be encrypted at the filesystem layer of the host gaming PC (e.g., using macOS FileVault, Linux dm-crypt, or Windows BitLocker). Application-level encryption will be considered for highly sensitive metadata, if any is identified beyond current scope.
    *   **In Transit:** All internal API communication within the Docker network will rely on Docker's default secure networking. External access to the web application will be secured via HTTPS (e.g., through an Nginx proxy with SSL termination). Data transmitted to AI services (Ollama, KyutaiTTS, Whisper ASR) will occur over secure internal Docker networks.

*   **Sensitive Data Handling Procedures:**
    *   **Identification:** Regularly review data inputs (source text, transcribed audio) for sensitive internal information (PII, confidential company data).
    *   **Minimization:** Only process and store the minimum necessary sensitive data.
    *   **Temporary Storage:** Transcribed audio and intermediate LLM outputs containing sensitive data will be purged immediately after successful podcast generation, not retained.
    *   **Access Control:** Access to raw audio inputs, transcribed texts, and generated podcasts on the local file system will be restricted to authorized `podcastfy` backend processes only, enforced at the operating system level.
    *   **Sanitization/Redaction:** Explore capabilities within the LLM (Ollama) to redact or anonymize sensitive information from generated podcast scripts as a proactive measure, if identiied by PRD or future requirements.

*   **Data Retention and Purging Policies:**
    *   **Generated Podcasts:** MP3 audio files and their metadata will be retained for a configurable period (e.g., 30 days default for MVP) to allow for user retrieval. Beyond this, an automated purging process will securely delete older files.
    *   **Source Data:** Original text inputs or uploaded audio files will be deleted immediately after successful processing, aligning with data minimization and security goals. Transcribed texts will be purged immediately after LLM processing.
    *   **User Data:** User credentials (passwords) will be hashed and salted indefinitely. Other user metadata (e.g., email) will adhere to a 90-day retention policy after user account deletion.

*   **Backup Encryption:**
    *   **Strategy:** Implement regular backups of generated podcasts and critical configuration files (users.json, templates.json/yaml) to an encrypted storage location on the local network or a physically secured external drive.
    *   **Encryption at Rest:** Backups will be encrypted using strong, industry-standard encryption methods (e.g., AES-256) on the backup target.
    *   **Frequency:** Daily backups of configuration, weekly backups of generated podcasts (or as needed, based on volume).
    *   **Recovery:** Document a detailed procedure for restoring the application and its data from backups in case of disaster, including decryption steps.

## Testing Strategy

**1. Testing Pyramid**

```text
          E2E Tests
         /        \
    Integration Tests
       /            \
  Frontend Unit  Backend Unit
```

---

**2. Test Organization**

*   **Frontend Tests:**
    ```text
    web-app/
    ├── src/
    │   ├── components/
    │   │   ├── MyComponent.test.tsx   # Unit tests for individual components
    │   │   └── MyComponent.stories.tsx # Storybook for component isolation/testing (also visual regression baseline)
    │   ├── features/
    │   │   ├── MyFeature/
    │   │   │   ├── components/
    │   │   │   │   └── SubComponent.test.tsx
    │   │   │   └── MyFeature.test.tsx # Integration tests for feature logic/components
    │   ├── services/
    │   │   └── podcastService.test.ts # Unit tests for API service layer
    │   ├── hooks/
    │   │   └── useMyHook.test.ts      # Unit tests for custom hooks
    │   ├── __mocks__/                 # Centralized API mocks for isolated frontend testing (e.g., Mock Service Worker)
    │   │   ├── handlers.ts
    │   │   └── server.ts
    │   └── test-data/                 # Reusable test data generators/factories (e.g., Faker.js)
    │       ├── podcast-factory.ts
    │       └── user-factory.ts
    ├── tests/ # End-to-End and Visual Regression tests
    │   ├── e2e/
    │   │   ├── auth.spec.ts
    │   │   └── podcastGeneration.spec.ts
    │   └── visual-regression/         # Dedicated folder for visual regression snapshots/configurations
    │       ├── homepage.spec.ts
    │       └── component.spec.ts
    ├── jest.config.ts # Jest configuration for unit/integration tests
    └── playwright.config.ts # Playwright configuration for E2E and Visual Regression
    ```
    *   **Visual Regression Testing:** Integrated with Playwright for full-page snapshot comparisons or Storybook addons for component-level visual tests. This ensures UI consistency across changes and device sizes.
    *   **Accessibility Testing:** Automated checks using tools like `axe-core` integrated into Jest (e.g., `jest-axe`) or Playwright tests, focusing on WCAG guidelines and semantic HTML during component development.
    *   **Frontend Test Data Management:** Utilize Mock Service Worker (MSW) or similar for network level mocking of API responses. Employ reusable data factories (e.g., `faker.js` based) in `test-data/` to generate consistent, realistic, and varied test data for components and integration tests.

*   **Backend Tests:**
    ```text
    api/
    ├── src/
    │   ├── api/
    │   │   └── routes/
    │   │       └── podcast_routes.test.py # Integration tests for API endpoints
    │   ├── core/
    │   │   ├── generation_service.test.py # Unit/Integration tests for core logic
    │   │   └── podcast_management.test.py
    │   ├── tts/
    │   │   └── providers/
    │   │       └── kyutai_tts_provider.test.py # Unit tests for provider interfaces
    │   └── llm/
    │       └── ollama_client.test.py
    ├── tests/ # Root for backend tests if not co-located
    │   └── conftest.py # Pytest fixtures
    └── pytest.ini # Pytest configuration
    ```

*   **E2E Tests:**
    ```text
    # Located in web-app/tests/e2e/ (as shown above)
    web-app/
    ├── tests/
    │   └── e2e/
    │       ├── fixtures/ # Test data, helper functions
    │       ├── reporters/ # Custom reporters
    │       ├── screenshots/ # Output for screenshot comparisons
    │       ├── videos/ # Output for video recordings of tests
    │       ├── auth.spec.ts # User authentication flow
    │       ├── podcastGeneration.spec.ts # Text-to-podcast and Audio-to-podcast flows
    │       └── management.spec.ts # Podcast listing and management
    └── playwright.config.ts # Playwright configuration
    ```

---
**3. Test Examples**

*   **Frontend Component Test (React Testing Library/Jest):**
    ```typescript
    // web-app/src/components/PodcastCard/PodcastCard.test.tsx
    import React from 'react';
    import { render, screen } from '@testing-library/react';
    import PodcastCard from './PodcastCard';
    import { Podcast } from '../../../packages/shared-types/types'; // Using shared type

    const mockPodcast: Podcast = {
      id: '123',
      title: 'Test Podcast',
      audioUrl: 'http://example.com/audio.mp3',
      duration: 120,
      generatedDate: '2025-01-01T10:00:00Z',
    };

    describe('PodcastCard', () => {
      it('renders podcast title and duration', () => {
        render(<PodcastCard podcast={mockPodcast} />);
        expect(screen.getByText('Test Podcast')).toBeInTheDocument();
        expect(screen.getByText('2 min')).toBeInTheDocument(); // Assuming formatting util
      });

      it('displays play and download buttons', () => {
        render(<PodcastCard podcast={mockPodcast} />);
        expect(screen.getByRole('button', { name: /play/i })).toBeInTheDocument();
        expect(screen.getByRole('link', { name: /download/i })).toHaveAttribute('href', mockPodcast.audioUrl);
      });

      // Add more tests for interactivity, missing data, etc.
    });
    ```

*   **Backend API Test (Pytest/FastAPI TestClient):**
    ```typescript
    # api/src/api/routes/podcast_routes.test.py
    from fastapi.testclient import TestClient
    from main import app # Assuming main.py is the FastAPI app entry
    import pytest

    client = TestClient(app)

    # For simplicity, mocking external services like Ollama, KyutaiTTS, WhisperASR
    # In a real scenario, use monkeypatching or dedicated test containers.
    # For MVP, direct unit tests of generate functions is probably sufficient.

    def test_generate_podcast_from_text_success():
        response = client.post(
            "/podcasts/generate/text",
            json={"text": "This is a test script.", "templateId": "default"},
        )
        assert response.status_code == 200
        assert "podcastId" in response.json()
        assert response.json()["status"] == "processing"

    def test_generate_podcast_from_text_invalid_input():
        response = client.post(
            "/podcasts/generate/text",
            json={"invalid_field": "oops"},
        )
        assert response.status_code == 422 # FastAPI validation error
        assert "detail" in response.json()

    # Add more tests for audio generation, retrieval, download, error cases etc.
    ```

*   **E2E Test (Playwright):**
    ```typescript
    // web-app/tests/e2e/podcastGeneration.spec.ts
    import { test, expect } from '@playwright/test';

    test.describe('Podcast Generation E2E Flow', () => {
      test('should generate a podcast from text and allow playback', async ({ page }) => {
        await page.goto('/'); // Assuming base URL is configured in playwright.config.ts

        // 1. Login (if authentication is enabled)
        // await page.fill('input[name="username"]', 'testuser');
        // await page.fill('input[name="password"]', 'testpassword');
        // await page.click('button:has-text("Login")');
        // await expect(page).toHaveURL('/dashboard');

        // 2. Navigate to Text-to-Podcast generation page
        await page.click('button:has-text("Generate Podcast")');
        await expect(page).toHaveURL('/generate');

        // 3. Input text and select template
        await page.fill('textarea[placeholder="Enter your script here..."]', 'This is an automated test to generate a short podcast.');
        await page.selectOption('select[name="template"]', { label: 'News Update' }); // Assuming a template selector

        // 4. Submit for generation
        await page.click('button:has-text("Generate Podcast")');

        // 5. Verify progress bar appears (FR9)
        await expect(page.locator('div.progress-bar')).toBeVisible();
        await expect(page.locator('div.progress-logs')).toBeVisible();

        // 6. Wait for success message (or for a specific element to appear, like playback button)
        await expect(page.locator('text=Podcast generated successfully!')).toBeVisible({ timeout: 60000 }); // Wait up to 60 seconds

        // 7. Verify playback and download options (FR7)
        await expect(page.locator('audio')).toBeVisible();
        await expect(
---

**5. Advanced Resilience Patterns**

For increased operational readiness beyond the MVP, the following resilience patterns should be considered and integrated, especially for interactions with the AI core services:

*   **Retry Policies:**
    *   **Mechanism:** Implement retry logic for transient failures (e.g., network glitches, temporary service unavailability) when `podcastfy` backend communicates with Ollama, Kyutai TTS, and Whisper ASR.
    *   **Approach:** Use exponential backoff with a maximum number of retries (`e.g., 3-5 attempts`). Differentiate between retriable (e.g., 500, 503, connection errors) and non-retriable (e.g., 400, 403, 404) errors.
    *   **Implementation:** Libraries like `tenacity` (Python) can be used to easily apply retry decorators to service calls.

*   **Circuit Breakers:**
    *   **Mechanism:** Apply circuit breaker patterns to calls to external or internal critical services (Ollama, Kyutai TTS, Whisper ASR) to prevent cascading failures.
    *   **Approach:** If a service consistently fails or becomes unresponsive, the circuit opens, preventing further calls for a defined period. After a timeout, it can transition to a half-open state to test if the service has recovered.
    *   **Implementation:** Consider libraries such as `pybreaker` (Python) or built-in frameworks features if available.

*   **Graceful Degradation:**
    *   **Mechanism:** Define how the application behaves if certain non-critical features or services become unavailable to maintain core functionality.
    *   **Approach:** For example, if a specific podcast template lookup fails, the system could fall back to a "default" podcast generation style instead of failing the entire request. If the transcription service is slow, provide clearer feedback to the user on the delay.
    *   **Considerations:** Prioritize core "text-to-audio" and "audio-to-text" flows. Non-essential features should fail gracefully (e.g., disable template selection if template service is down, or use a cached version if up-to-date templates cannot be fetched).
    *   **User Feedback:** Enhance the progress bar (FR9) to clearly indicate when a fallback mechanism is active or if a specific sub-process is experiencing delays, managing user expectations.

*   **Automated Recovery:**
    *   **Mechanism:** For long-running processes like podcast generation, implement mechanisms to checkpoint progress or store intermediate results, allowing for resuming failed operations.
    *   **Approach:** If a generation process fails at the TTS stage, store the LLM-generated text and allow the system (or user) to re-initiate from that point rather than restarting from raw input.
    *   **Implementation:** This might involve defining a state machine for podcast generation requests that tracks stages and allows for retries at specific points.