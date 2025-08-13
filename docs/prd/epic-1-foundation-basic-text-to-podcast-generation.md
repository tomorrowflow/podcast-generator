# Epic 1: Foundation & Basic Text-to-Podcast Generation

**Expanded Goal:** This epic is focused on establishing the fundamental on-premises infrastructure for the AI Podcast Generator and enabling the primary capability of transforming raw text inputs into generated audio podcasts. It encompasses the initial setup of the monorepo structure, the containerized deployment of core AI services (`Ollama` for LLM, `Kyutai TTS`), and the development of a basic web interface to facilitate text submission and the retrieval of the synthesized audio output, thereby delivering a functional, end-to-end Minimal Viable Product for text-based podcast creation.

## Story 1.1: Set up Monorepo and Initial Project Structure

As a **Developer**,
I want to **set up the project with a monorepo structure**,
so that **all core components are centrally organized for efficient development and deployment.**

### Acceptance Criteria

1.  **1:** A Git repository is initialized as a monorepo in the root directory.
2.  **2:** The `podcastfy` project, including any planned modifications, is integrated into a designated subdirectory (e.g., `src/podcastfy`).
3.  **3:** A basic web application scaffold (e.g., using a `21st-dev/magic-mcp` compatible framework like React/Vue/Angular) is created in its own distinct subdirectory (e.g., `src/web-app`).
4.  **4:** A `docker-compose.yml` file is configured at the monorepo root to define and orchestrate the build and runtime environments for initial `podcastfy` (placeholder) and web application (placeholder) services.
5.  **5:** A simple "Hello World" or health check endpoint is implemented within the web application, accessible and verifiable when the application is run via Docker Compose.

## Story 1.2: Implement Text Submission UI and Backend Endpoint

As an **Internal Content Creator**,
I want to **submit text content through a web interface**,
so that **I can initiate the podcast generation process easily.**

### Acceptance Criteria

1.  **1:** The web application UI includes a clearly visible text input area (e.g., a multi-line textbox).
2.  **2:** A "Generate Podcast" button is present and clearly labeled in the user interface.
3.  **3:** Clicking the "Generate Podcast" button sends the text content from the input area to a dedicated `podcastfy` backend API endpoint.
4.  **4:** The `podcastfy` backend API endpoint successfully receives the text submission and returns an acknowledgment or basic status response.

## Story 1.3: Integrate Ollama for LLM and Kyutai TTS with Podcastfy Backend

As a **System Integrator**,
I want `podcastfy` to **seamlessly utilize locally deployed LLM and TTS services**,
so that **text content can be processed and converted into human-like speech securely on-premises.**

### Acceptance Criteria

1.  **1:** `Ollama` is successfully containerized via Docker and runs as a service accessible by the `podcastfy` backend.
2.  **2:** `Kyutai TTS` (from `delayed-streams-modeling`) is successfully containerized via Docker and runs as a service accessible by the `podcastfy` backend.
3.  **3:** The `podcastfy` backend is configured and capable of sending text input to `Ollama` for processing (e.g., summarization or script generation).
4.  **4:** `podcastfy` successfully integrates with `Kyutai TTS` to convert `Ollama`-generated text into an audio file.
5.  **5:** The generated audio file, along with any relevant metadata, is temporarily stored or made accessible for subsequent processing or retrieval.

## Story 1.4: Enable Podcast Generation Flow and Audio Output/Download

As an **Internal Content Creator**,
I want to **obtain and listen to the generated podcast audio**,
so that **I can immediately access and utilize the final output.**

### Acceptance Criteria

1.  **1:** The `podcastfy` backend successfully combines the LLM-processed text and TTS-generated audio into a complete, listenable podcast audio file (e.g., an MP3).
2.  **2:** Upon successful podcast generation, the web interface dynamically updates to indicate completion, and provides a clear mechanism to access the audio.
3.  **3:** Users can play the generated podcast audio directly within the web browser via an embedded audio player.
4.  **4:** Users can download the generated podcast audio file from the web interface.
5.  **5:** Basic error handling and user feedback mechanisms are implemented in the UI for failed generation processes (e.g., displaying error messages).
