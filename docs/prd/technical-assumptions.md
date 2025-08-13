# Technical Assumptions

## Repository Structure: Monorepo

The project will adopt a Monorepo structure, incorporating the `podcastfy` core modifications and the new web application within a single repository. This approach is chosen given the user's intent to integrate the forked `podcastfy` into the existing repository, simplifying collaboration, dependency management, and atomic commits across tightly coupled components for this internal tool.

## Service Architecture

The system will operate with a microservices-oriented architecture. This involves defining clear APIs between the web application, the `podcastfy` backend, and the STT/TTS/LLM services. This decision is based on the Project Brief's "Architecture Considerations" which states a preference for "Microservices-oriented, with clearly defined APIs."

## Testing Requirements

The Project Brief does not explicitly detail testing requirements beyond a general assumption of compatibility and performance. Given the project's nature and the need for a robust system, the testing strategy should encompass a Full Testing Pyramid, including unit, integration, and end-to-end tests to ensure comprehensive coverage and stability. (Assumption Made)

## Additional Technical Assumptions and Requests

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
