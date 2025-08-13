# Backend Architecture

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
