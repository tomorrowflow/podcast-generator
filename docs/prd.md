# AI Podcast Generator Product Requirements Document (PRD)

## Goals and Background Context

### Goals

*   Enhance Internal Content Production Efficiency: Achieve a 50% reduction in time taken to produce audio content from text sources within 6 months of MVP deployment.
*   Strengthen Data Security & IP Protection: Maintain 0 instances of sensitive internal data exposure to external services related to podcast generation.
*   Increase Internal Knowledge Dissemination: Boost engagement with internal communications and training materials by creating an additional 2 hours of audio content per week, leading to a 20% increase in content consumption by relevant internal teams within 9 months.
*   User Adoption Rate: 75% of target internal content creators utilize the AI Podcast Generator at least once per month.
*   Content Generation Satisfaction: Achieve an average user satisfaction score of 4 out of 5 for generated podcast quality and ease of use.
*   Time Savings per Podcast: Average 30% reduction in time spent by users on podcast creation compared to manual methods.

### Background Context

This project addresses the inefficiencies and security risks associated with current internal audio content creation processes, particularly podcasts. Manual methods lead to slow turnaround times, and reliance on external cloud AI services poses significant threats to data privacy, intellectual property, and compliance due to sensitive internal data exposure. The AI Podcast Generator aims to resolve these issues by providing an on-premises, secure, and automated solution that transforms internal information into high-quality audio podcasts, ensuring data remains within the secure datacenter environment and boosting internal team productivity. This solution leverages open-source components like `podcastfy`, `Kyutai TTS`, `Whisper ASR Webservice`, and `Ollama`, deployed via Docker Compose on a gaming PC for MVP due to its GPU capabilities, ensuring full control over sensitive information and streamlined content production through a web application with `21st-dev/magic-mcp` components.

### Change Log

| Date | Version | Description | Author |
| :--------- | :------ | :----------------- | :----- |
| 2025-08-12 | 1.0     | Initial draft of PRD | John |

## Requirements

### Functional

1.  FR1: The system shall allow users to submit text content via a web interface for podcast generation.
2.  FR2: The system shall process submitted text content using Ollama (LLM) and Kyutai TTS to generate audio podcasts.
3.  FR3: The system shall allow users to upload meeting audio (or similar spoken-word audio) via a web interface.
4.  FR4: The system shall transcribe uploaded audio using `whisper-asr-webservice`.
5.  FR5: The system shall process transcribed text to generate podcasts using Ollama (LLM) and Kyutai TTS.
6.  FR6: The system shall provide a web interface for initiating podcast generation.
7.  FR7: The system shall provide a web interface for downloading and playing generated audio.
8.  FR8: The web interface shall be built with `21st-dev/magic-mcp` basic components.
9.  FR9: The web UI shall display a detailed processing progress bar, indicating real-time logs of the podcast generation pipeline.
10. FR10: The system shall allow users to choose from a library of pre-designed podcast templates (e.g., interview, news update, storytelling) through the UI.

### Non Functional

1.  NFR1: All core components (`podcastfy` backend, `whisper-asr-webservice`, Ollama, Kyutai TTS) shall be containerized.
2.  NFR2: The system shall be deployed locally within our datacenter on a gaming PC via Docker Compose, leveraging its GPU for accelerated processing.
3.  NFR3: The system shall maintain 0 instances of sensitive internal data exposure to external services related to podcast generation.
4.  NFR4: The system shall demonstrate stable operation and acceptable performance metrics on the gaming PC deployment for typical usage volumes.
5.  NFR5: The `podcastfy` backend shall be developed in Python and capable of integrating Ollama (LLM) and Kyutai TTS as local providers.
6.  NFR6: The system shall utilize `LangChain` (or similar orchestration frameworks) for integrations with Ollama and Kyutai TTS.
7.  NFR7: The system shall employ `whisper-asr-webservice` for Speech-to-Text capabilities.
8.  NFR8: The system shall utilize `Kyutai TTS` (from `delayed-streams-modeling`) for Text-to-Speech synthesis.
9.  NFR9: The system shall use `Ollama` for Large Language Model (LLM) functionalities, providing an OpenAI-compatible API.
10. NFR10: The web application's UI framework shall be compatible with `21st-dev/magic-mcp`.
11. NFR11: The system shall ideally follow a microservices-oriented architecture with clearly defined APIs between the web application, `podcastfy` backend, and STT/TTS/LLM services.
12. NFR12: Initial MVP user management shall be simple (e.g., local access or basic internal authentication).

## User Interface Design Goals

### Overall UX Vision

The UX vision for the AI Podcast Generator is to provide a highly intuitive and efficient web-based interface that simplifies the process of creating professional-quality audio content from diverse internal data sources. The design will prioritize ease of use, clear feedback on processing status, and a streamlined workflow that empowers internal content creators to quickly generate and disseminate engaging podcasts securely within the organization.

### Key Interaction Paradigms

The primary interaction paradigm will revolve around clear input mechanisms for text and audio content, followed by a guided process for initiating podcast generation. A critical interactive element will be the real-time, detailed progress bar, offering transparency into the backend processing. Users will interact with pre-defined templates through intuitive selection menus, and content consumption will involve simple playback and download functionalities. The interface will adhere to standard web application navigation patterns.

### Core Screens and Views

*   **Homepage/Dashboard:** A central entry point for users to initiate new podcast generation, view recent activities, or access previously generated podcasts.
*   **Content Submission View:** Dedicated interface for uploading text or audio files, providing clear instructions and options for content types.
*   **Template Selection View:** A screen allowing users to browse and select from available podcast templates.
*   **Generation Progress View:** Displays the detailed processing progress bar and real-time logs during podcast generation.
*   **Podcast Playback/Download View:** Interface for listening to generated podcasts and options for downloading the audio files.

### Accessibility: None

### Branding

The MVP will focus on a functional and clear interface, assuming adherence to standard internal corporate branding guidelines if any exist. There is no specific, unique branding element mentioned in the Project Brief that would require custom UI/UX replication beyond typical design standards. (Assumption Made)

### Target Device and Platforms: Web Responsive

## Technical Assumptions

### Repository Structure: Monorepo

The project will adopt a Monorepo structure, incorporating the `podcastfy` core modifications and the new web application within a single repository. This approach is chosen given the user's intent to integrate the forked `podcastfy` into the existing repository, simplifying collaboration, dependency management, and atomic commits across tightly coupled components for this internal tool.

### Service Architecture

The system will operate with a microservices-oriented architecture. This involves defining clear APIs between the web application, the `podcastfy` backend, and the STT/TTS/LLM services. This decision is based on the Project Brief's "Architecture Considerations" which states a preference for "Microservices-oriented, with clearly defined APIs."

### Testing Requirements

The Project Brief does not explicitly detail testing requirements beyond a general assumption of compatibility and performance. Given the project's nature and the need for a robust system, the testing strategy should encompass a Full Testing Pyramid, including unit, integration, and end-to-end tests to ensure comprehensive coverage and stability. (Assumption Made)

### Additional Technical Assumptions and Requests

*   **Core Backend Technologies:** Python (`podcastfy`), extending to integrate Ollama (LLM) and Kyutai TTS (via Delayed Streams Modeling) as local providers. `LangChain` (or similar orchestration frameworks specifically for these integrations) is preferred.
*   **Speech-to-Text (STT):** `whisper-asr-webservice` (Dockerized).
*   **Text-to-Speech (TTS):** `Kyutai TTS` (from `delayed-streams-modeling`), Dockerized.
*   **Large Language Model (LLM):** `Ollama` (Dockerized, providing OpenAI-compatible API).
*   **User Interface (UI):** Web-based application, utilizing `21st-dev/magic-mcp` for UI component generation. A compatible framework (e.g., React, Vue, Angular) needs to be selected.
*   **Deployment:** Docker and Docker Compose for containerization and orchestration, leveraging NVIDIA Container Toolkit for GPU utilization.
*   **GPU Compatibility:** The chosen open-source AI models and their Dockerized implementations are assumed to be fully compatible with the gaming PC's GPU and associated drivers (e.g., NVIDIA CUDA).
*   **Performance on Gaming PC:** The gaming PC (with 8GB VRAM) is assumed to provide sufficient performance for MVP workloads despite potential limitations for very large models or extremely high throughput.
*   **Ollama Stability:** Ollama, when running locally and providing an OpenAI-compatible API, is assumed to be stable and reliable enough for core LLM functionalities within the MVP.
*   **Open-Source Maturity & Support:** Sufficient community support and documentation are assumed to exist for `podcastfy`, `delayed-streams-modeling` (Kyutai TTS), and `whisper-asr-webservice` for successful integration and ongoing maintenance.
*   **`21st-dev/magic-mcp` Utility:** The `21st-dev/magic-mcp` tool is assumed to effectively accelerate UI development and provide suitable components.

## Epic List

Here is a high-level list of proposed epics for the AI Podcast Generator, designed to be logically sequential and deliver distinct, valuable increments of functionality.

*   **Epic 1: Foundation & Basic Text-to-Podcast Generation**
    *   **Goal:** Establish the foundational on-premises infrastructure and enable the core capability of generating podcasts from text input, delivering a functional end-to-end MVP.
    *   **Rationale:** This epic focuses on setting up the local deployment environment (Docker, GPU access) and integrating the essential components (`podcastfy`, Ollama, Kyutai TTS) to produce a podcast from text. It includes a basic web interface for text input and audio output, ensuring that the critical "text-to-podcast" feature is operational and deployable early. This sets the stage for all subsequent functionality.

*   **Epic 2: Audio-to-Podcast Generation & Enhanced UI Feedback**
    *   **Goal:** Extend the system to support audio inputs by integrating speech-to-text functionality and enhance the user interface with comprehensive progress visualization for all generation processes.
    *   **Rationale:** Building on Epic 1's foundation, this epic introduces the ability to process audio files (using `whisper-asr-webservice`) considerably improves the user experience by providing a detailed, real-time progress bar. This delivers another core feature and enhances transparency for users.

*   **Epic 3: Template Integration & Basic Content Management**
    *   **Goal:** Implement the pre-designed podcast templates feature, allowing users to select different podcast formats, and lay the groundwork for basic content organization within the application.
    *   **Rationale:** This epic leverages the existing generation capabilities to provide more structured and varied podcast output through templates. It also encompasses any basic UI/UX elements needed to manage these templates or the generated content, increasing the system's utility and ease of use for diverse content creation.

## Epic 1: Foundation & Basic Text-to-Podcast Generation

**Expanded Goal:** This epic is focused on establishing the fundamental on-premises infrastructure for the AI Podcast Generator and enabling the primary capability of transforming raw text inputs into generated audio podcasts. It encompasses the initial setup of the monorepo structure, the containerized deployment of core AI services (`Ollama` for LLM, `Kyutai TTS`), and the development of a basic web interface to facilitate text submission and the retrieval of the synthesized audio output, thereby delivering a functional, end-to-end Minimal Viable Product for text-based podcast creation.

### Story 1.1: Set up Monorepo and Initial Project Structure

As a **Developer**,
I want to **set up the project with a monorepo structure**,
so that **all core components are centrally organized for efficient development and deployment.**

#### Acceptance Criteria

1.  **1:** A Git repository is initialized as a monorepo in the root directory.
2.  **2:** The `podcastfy` project, including any planned modifications, is integrated into a designated subdirectory (e.g., `src/podcastfy`).
3.  **3:** A basic web application scaffold (e.g., using a `21st-dev/magic-mcp` compatible framework like React/Vue/Angular) is created in its own distinct subdirectory (e.g., `src/web-app`).
4.  **4:** A `docker-compose.yml` file is configured at the monorepo root to define and orchestrate the build and runtime environments for initial `podcastfy` (placeholder) and web application (placeholder) services.
5.  **5:** A simple "Hello World" or health check endpoint is implemented within the web application, accessible and verifiable when the application is run via Docker Compose.

### Story 1.2: Implement Text Submission UI and Backend Endpoint

As an **Internal Content Creator**,
I want to **submit text content through a web interface**,
so that **I can initiate the podcast generation process easily.**

#### Acceptance Criteria

1.  **1:** The web application UI includes a clearly visible text input area (e.g., a multi-line textbox).
2.  **2:** A "Generate Podcast" button is present and clearly labeled in the user interface.
3.  **3:** Clicking the "Generate Podcast" button sends the text content from the input area to a dedicated `podcastfy` backend API endpoint.
4.  **4:** The `podcastfy` backend API endpoint successfully receives the text submission and returns an acknowledgment or basic status response.

### Story 1.3: Integrate Ollama for LLM and Kyutai TTS with Podcastfy Backend

As a **System Integrator**,
I want `podcastfy` to **seamlessly utilize locally deployed LLM and TTS services**,
so that **text content can be processed and converted into human-like speech securely on-premises.**

#### Acceptance Criteria

1.  **1:** `Ollama` is successfully containerized via Docker and runs as a service accessible by the `podcastfy` backend.
2.  **2:** `Kyutai TTS` (from `delayed-streams-modeling`) is successfully containerized via Docker and runs as a service accessible by the `podcastfy` backend.
3.  **3:** The `podcastfy` backend is configured and capable of sending text input to `Ollama` for processing (e.g., summarization or script generation).
4.  **4:** `podcastfy` successfully integrates with `Kyutai TTS` to convert `Ollama`-generated text into an audio file.
5.  **5:** The generated audio file, along with any relevant metadata, is temporarily stored or made accessible for subsequent processing or retrieval.

### Story 1.4: Enable Podcast Generation Flow and Audio Output/Download

As an **Internal Content Creator**,
I want to **obtain and listen to the generated podcast audio**,
so that **I can immediately access and utilize the final output.**

#### Acceptance Criteria

1.  **1:** The `podcastfy` backend successfully combines the LLM-processed text and TTS-generated audio into a complete, listenable podcast audio file (e.g., an MP3).
2.  **2:** Upon successful podcast generation, the web interface dynamically updates to indicate completion, and provides a clear mechanism to access the audio.
3.  **3:** Users can play the generated podcast audio directly within the web browser via an embedded audio player.
4.  **4:** Users can download the generated podcast audio file from the web interface.
5.  **5:** Basic error handling and user feedback mechanisms are implemented in the UI for failed generation processes (e.g., displaying error messages).

## Epic 2: Audio-to-Podcast Generation & Enhanced UI Feedback

**Expanded Goal:** This epic aims to significantly enhance the capabilities of the AI Podcast Generator by integrating speech-to-text functionality, allowing users to transform audio inputs (like meeting recordings) into podcasts. Concurrently, it focuses on improving the user experience by implementing a detailed, real-time progress bar across all generation processes, providing transparent feedback to the internal content creator on the status of their podcast generation.

### Story 2.1: Implement Audio Submission UI and Backend Endpoint

As an **Internal Content Creator**,
I want to **submit audio content through a web interface**,
so that **I can initiate podcast generation from spoken-word sources.**

#### Acceptance Criteria

1.  **1:** The web application UI includes a clearly visible audio file upload mechanism (e.g., a file input field with drag-and-drop support).
2.  **2:** A "Generate Podcast from Audio" button is present and clearly labeled in the user interface.
3.  **3:** Clicking the "Generate Podcast from Audio" button sends the uploaded audio file to a dedicated `podcastfy` backend API endpoint.
4.  **4:** The `podcastfy` backend API endpoint successfully receives the audio file submission and returns an acknowledgment or basic status response.

### Story 2.2: Integrate Whisper ASR Webservice for Audio Transcription

As a **System Integrator**,
I want `podcastfy` to **utilize a local speech-to-text service for audio files**,
so that **spoken content can be accurately transcribed and prepared for LLM processing.**

#### Acceptance Criteria

1.  **1:** `whisper-asr-webservice` is successfully containerized via Docker and runs as a service accessible by the `podcastfy` backend.
2.  **2:** The `podcastfy` backend is configured to send received audio files to `whisper-asr-webservice` for transcription.
3.  **3:** `whisper-asr-webservice` accurately transcribes the audio content into text.
4.  **4:** The `podcastfy` backend successfully receives the transcribed text from `whisper-asr-webservice`.
5.  **5:** The transcribed text is then passed to `Ollama` for further processing for podcast generation.

### Story 2.3: Implement Detailed Processing Progress Bar in UI

As an **Internal Content Creator**,
I want to **see the real-time progress of my podcast generation**,
so that **I understand where my request is in the pipeline and when it will be complete.**

#### Acceptance Criteria

1.  **1:** The web UI displays a detailed processing progress bar that visually indicates the current stage of podcast generation (e.g., "Uploading," "Transcribing," "Processing with LLM," "Generating Audio").
2.  **2:** The progress bar updates in real-time to reflect changes in the backend processing status.
3.  **3:** Real-time textual logs from the backend processes are streamed and displayed within the UI alongside the progress bar, providing granular detail.
4.  **4:** The UI clearly indicates completion or failure of the podcast generation process, along with relevant messages.

## Epic 3: Template Integration & Basic Content Management

**Expanded Goal:** This epic aims to empower internal content creators with pre-designed podcast templates, enabling them to quickly generate podcasts in various formats (e.g., interview, news update). Concurrently, it will lay the foundational groundwork for essential content management features, such as organizing and potentially categorizing generated audio outputs, enhancing usability and streamlining the overall content creation workflow.

### Story 3.1: Implement Podcast Template Selection in UI

As an **Internal Content Creator**,
I want to **choose from a library of pre-designed podcast templates**,
so that **I can quickly generate podcasts in various formats.**

#### Acceptance Criteria

1.  **1:** The web application UI includes a dedicated section or dropdown for selecting podcast templates.
2.  **2:** A clear list of available templates (e.g., "Interview," "News Update," "Storytelling") is displayed.
3.  **3:** Selecting a template updates the generation parameters (e.g., instructing the LLM to generate content in a specific format) for the subsequent podcast creation process.
4.  **4:** The UI provides a brief description or preview for each template to inform user selection.

### Story 3.2: Backend Integration for Template-Driven Content Generation

As a **System Integrator**,
I want the `podcastfy` backend to **apply selected templates during content generation**,
so that **the generated podcasts adhere to specific structural and stylistic formats.**

#### Acceptance Criteria

1.  **1:** The `podcastfy` backend receives the selected template identifier along with the user's input (text or transcribed audio).
2.  **2:** The backend logic incorporates the template's guidelines when instructing `Ollama` for content generation (e.g., structuring the narrative, assigning roles for multi-speaker formats).
3.  **3:** The generated text from `Ollama` reflects the chosen template's structure and style requirements.
4.  **4:** The subsequent TTS process (via `Kyutai TTS`) correctly synthesizes audio based on the template-driven content.

### Story 3.3: Implement Basic Generated Podcast Organization

As an **Internal Content Creator**,
I want to **easily find and manage my generated podcasts**,
so that **my content library is organized and accessible.**

#### Acceptance Criteria

1.  **1:** The web application UI includes a dedicated section for viewing previously generated podcasts.
2.  **2:** Podcasts are displayed in a list or grid format, showing key metadata (e.g., title, generation date, duration).
3.  **3:** Users can sort or filter the list of generated podcasts by basic criteria (e.g., date, name).
4.  **4:** Users can easily rename generated podcast files or titles within the UI.
5.  **5:** The underlying storage mechanism for generated podcasts supports the organization and retrieval based on UI interactions.