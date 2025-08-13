# Epic 2: Audio-to-Podcast Generation & Enhanced UI Feedback

**Expanded Goal:** This epic aims to significantly enhance the capabilities of the AI Podcast Generator by integrating speech-to-text functionality, allowing users to transform audio inputs (like meeting recordings) into podcasts. Concurrently, it focuses on improving the user experience by implementing a detailed, real-time progress bar across all generation processes, providing transparent feedback to the internal content creator on the status of their podcast generation.

## Story 2.1: Implement Audio Submission UI and Backend Endpoint

As an **Internal Content Creator**,
I want to **submit audio content through a web interface**,
so that **I can initiate podcast generation from spoken-word sources.**

### Acceptance Criteria

1.  **1:** The web application UI includes a clearly visible audio file upload mechanism (e.g., a file input field with drag-and-drop support).
2.  **2:** A "Generate Podcast from Audio" button is present and clearly labeled in the user interface.
3.  **3:** Clicking the "Generate Podcast from Audio" button sends the uploaded audio file to a dedicated `podcastfy` backend API endpoint.
4.  **4:** The `podcastfy` backend API endpoint successfully receives the audio file submission and returns an acknowledgment or basic status response.

## Story 2.2: Integrate Whisper ASR Webservice for Audio Transcription

As a **System Integrator**,
I want `podcastfy` to **utilize a local speech-to-text service for audio files**,
so that **spoken content can be accurately transcribed and prepared for LLM processing.**

### Acceptance Criteria

1.  **1:** `whisper-asr-webservice` is successfully containerized via Docker and runs as a service accessible by the `podcastfy` backend.
2.  **2:** The `podcastfy` backend is configured to send received audio files to `whisper-asr-webservice` for transcription.
3.  **3:** `whisper-asr-webservice` accurately transcribes the audio content into text.
4.  **4:** The `podcastfy` backend successfully receives the transcribed text from `whisper-asr-webservice`.
5.  **5:** The transcribed text is then passed to `Ollama` for further processing for podcast generation.

## Story 2.3: Implement Detailed Processing Progress Bar in UI

As an **Internal Content Creator**,
I want to **see the real-time progress of my podcast generation**,
so that **I understand where my request is in the pipeline and when it will be complete.**

### Acceptance Criteria

1.  **1:** The web UI displays a detailed processing progress bar that visually indicates the current stage of podcast generation (e.g., "Uploading," "Transcribing," "Processing with LLM," "Generating Audio").
2.  **2:** The progress bar updates in real-time to reflect changes in the backend processing status.
3.  **3:** Real-time textual logs from the backend processes are streamed and displayed within the UI alongside the progress bar, providing granular detail.
4.  **4:** The UI clearly indicates completion or failure of the podcast generation process, along with relevant messages.
