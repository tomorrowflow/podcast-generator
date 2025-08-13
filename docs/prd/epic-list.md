# Epic List

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
