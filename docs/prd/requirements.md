# Requirements

## Functional

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

## Non Functional

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
