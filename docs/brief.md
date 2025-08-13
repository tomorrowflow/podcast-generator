# Project Brief: AI Podcast Generator

### Executive Summary

The AI Podcast Generator is an innovative internal solution designed to rapidly create personalized podcasts from diverse content sources. Leveraging open-source components such as `podcastfy`, `Kyutai TTS` (from `delayed-streams-modeling`), and `Whisper ASR Webservice`, integrated with `Ollama` for advanced language generation, this system addresses the need for efficient internal content production. By deploying within our datacenter on a gaming PC for the MVP, we ensure data security and maintain full control over sensitive information. The web application, enhanced with `21st-dev/magic-mcp` components, offers a streamlined user experience, fostering rapid content creation, personalized learning, and standardized podcast formats, ultimately boosting internal team productivity and intellectual property protection.

### Problem Statement

Internal content creation, particularly for audio formats like podcasts, currently suffers from inefficiencies due to manual processes and reliance on external, potentially insecure, third-party services. This leads to slow turnaround times and a lack of scalable solutions for generating personalized, high-quality audio content. A significant pain point is the exposure of potentially sensitive internal data when utilizing cloud-based AI services, raising concerns about data privacy, intellectual property, and compliance. Crucially, dedicated internal services for generating such advanced podcast content are not currently available. Therefore, there is a critical need for an on-premises, secure, and automated solution that can rapidly transform internal information into high-quality audio podcasts without compromising sensitive data or intellectual property.

### Proposed Solution

Our proposed solution is an on-premises AI Podcast Generator, a web-based application designed to securely and efficiently transform internal data into high-quality audio podcasts. This solution will leverage a combination of established open-source projects: `podcastfy` as the core content generation framework, `whisper-asr-webservice` for robust Speech-to-Text capabilities, and `Kyutai TTS` (from `delayed-streams-modeling`) for advanced, natural-sounding Text-to-Speech synthesis. The generative AI capabilities will be powered by `Ollama`, serving large language models via an OpenAI-compatible API, ensuring all sensitive data processing remains within our secure datacenter environment. The user interface will be developed using `21st-dev/magic-mcp` to facilitate rapid development of intuitive and feature-rich components, including a detailed progress bar for transparent processing and pre-designed templates for streamlined content creation. This tightly integrated, open-source, and on-premises approach provides unparalleled control over data security and intellectual property, while offering a flexible and scalable platform for future content generation needs.

### Target Users

#### Primary User Segment: Internal Content Creators & Communicators

This segment includes any individuals or teams within the organization responsible for generating and disseminating information, presentations, training materials, or internal communications. They are currently challenged by the time-consuming and often manual processes of converting text-based content into engaging audio formats. Their primary needs include efficient and secure tools for transforming internal documents, meetings, or reports into audio podcasts without relying on external cloud services. They aim to enhance the accessibility and engagement of internal communications, streamline knowledge sharing, and protect sensitive organizational data. Furthermore, creating such podcast content is currently impossible due to the lack of trustworthy internal tools for handling sensitive company data.

### Goals & Success Metrics

#### Business Objectives

*   **Enhance Internal Content Production Efficiency:** Achieve a 50% reduction in time taken to produce audio content from text sources within 6 months of MVP deployment.
*   **Strengthen Data Security & IP Protection:** Maintain 0 instances of sensitive internal data exposure to external services related to podcast generation.
*   **Increase Internal Knowledge Dissemination:** Boost engagement with internal communications and training materials by creating an additional 2 hours of audio content per week, leading to a 20% increase in content consumption by relevant internal teams within 9 months.

#### User Success Metrics

*   **User Adoption Rate:** 75% of target internal content creators utilize the AI Podcast Generator at least once per month.
*   **Content Generation Satisfaction:** Achieve an average user satisfaction score of 4 out of 5 for generated podcast quality and ease of use.
*   **Time Savings per Podcast:** Average 30% reduction in time spent by users on podcast creation compared to manual methods.

#### Key Performance Indicators (KPIs)

*   **Podcast Generation Volume:** Total number of podcasts generated per month.
*   **Processing Speed:** Average time taken from input submission to audio output.
*   **Data Exposure Incidents:** Number of reported or detected instances of sensitive data leakage.
*   **User Feedback Score:** Aggregated score from in-app surveys or feedback channels.

### MVP Scope

#### Core Features (Must Have)

*   **Text-to-Podcast Generation:** Users can submit text content (e.g., meeting notes, reports) via a web interface, which is then processed by `podcastfy` using Ollama (LLM) and Kyutai TTS to generate an audio podcast.
*   **Audio-to-Podcast Generation:** Users can upload meeting audio (or similar spoken-word audio) via a web interface. The audio will be transcribed using `whisper-asr-webservice`, and the resulting text will be processed to generate a podcast using Ollama (LLM) and Kyutai TTS.
*   **On-Premises Deployment:** All core components (`podcastfy` backend, `whisper-asr-webservice`, Ollama, Kyutai TTS) will be containerized and deployed locally within our datacenter on a gaming PC via Docker Compose, leveraging its GPU for accelerated processing.
*   **Basic Web User Interface:** A functional web interface for inputting text/audio, initiating podcast generation, and downloading/playing the generated audio, built with `21st-dev/magic-mcp` basic components.
*   **Detailed Processing Progress Bar:** A visual indication of the podcast generation pipeline's progress within the web UI, displaying real-time logs.
*   **Pre-designed Podcast Templates:** Users can choose from a library of templates (e.g., interview, news update, storytelling) through the UI.

#### Out of Scope for MVP

*   **Real-time Visual Voice Feedback:** Advanced UI for real-time visualization of Kyutai TTS voice characteristics.
*   **"Podcast-on-Demand" Service:** Dynamic generation of podcasts based on niche topic requests from knowledge bases.
*   **Advanced Content Curation/Editing Tools:** In-app tools for fine-tuning podcast content (e.g., cutting, splicing, extensive script editing).
*   **Multi-User Authentication/Authorization:** While security is paramount for data, initial MVP user management will be simple (e.g., local access or basic internal authentication).
*   **Integration with External Platforms:** No direct integrations with public podcast hosting platforms or distribution channels.

#### MVP Success Criteria

The MVP will be considered successful if:
*   It successfully generates high-quality podcasts from both text and audio inputs in a secure, on-premises environment.
*   Internal content creators can effectively use the web application to produce audio content, leading to early adoption.
*   The system demonstrates stable operation and acceptable performance metrics on the gaming PC deployment for typical usage volumes.
*   No instances of sensitive internal data exposure to external services are recorded.

### Post-MVP Vision

#### Phase 2 Features

*   **Real-time Visual Voice Feedback:** Implementing the advanced UI for real-time visualization of Kyutai TTS voice characteristics to enhance voice selection and customization.
*   **Expanded Content Sources:** Beyond text and audio, explore integration with other internal data bases (e.g., internal documents, reports, knowledge bases) for podcast generation.
*   **Basic Content Curation/Editing Tools:** Introduce in-app functionalities for basic podcast post-production, such as trimming or simple audio adjustments.

#### Long-term Vision

Our long-term vision is to establish the AI Podcast Generator as the primary internal platform for on-demand audio content creation, transforming how internal communications, training, and knowledge sharing are conducted. This platform will become a central hub where any internal information can be dynamically converted into engaging, personalized audio experiences, fostering a rich, accessible, and secure knowledge ecosystem within the organization.

#### Expansion Opportunities

*   **Advanced Customization:** Enable granular control over voice styles, emotional tones, and narrative pacing.
*   **Integration with Internal Communication Platforms:** Seamlessly integrate with existing internal communication tools (e.g., Slack, Teams) for broader dissemination of generated podcasts.
*   **Multi-Role/Multi-Speaker Support:** Develop features for more complex podcast formats involving multiple AI-generated voices interacting.
*   **Automated Content Discovery:** Implement AI-driven suggestions for content topics based on internal data trends or user roles.

### Technical Considerations

#### Platform Requirements

*   **Target Platforms:** On-premises datacenter environment, initially a single gaming PC with a GPU.
*   **Performance Requirements:** The MVP aims for acceptable performance for typical internal usage volumes, acknowledging potential limitations of an 8GB VRAM GPU for large models/high throughput.
*   **Deployment:** Docker and Docker Compose for containerization and orchestration. NVIDIA Container Toolkit must be installed for GPU utilization within Docker.

#### Technology Preferences

*   **Core Backend:** Python (`podcastfy`), which will be extended to integrate Ollama (LLM) and Kyutai TTS (via Delayed Streams Modeling) as local providers. Leveraging LangChain (or similar orchestration frameworks specifically for these integrations) is also preferred.
*   **Speech-to-Text (STT):** `whisper-asr-webservice` (Dockerized).
*   **Text-to-Speech (TTS):** `Kyutai TTS` (from `delayed-streams-modeling`), Dockerized.
*   **Large Language Model (LLM):** `Ollama` (Dockerized, providing OpenAI-compatible API).
*   **User Interface (UI):** Web-based application, utilizing `21st-dev/magic-mcp` for UI component generation. Framework choice (e.g., React, Vue, Angular) to be determined but compatible with `21st-dev/magic-mcp`.

#### Architecture Considerations

*   **Repository Structure:** Monorepo or separate repositories for `podcastfy` modifications, web application, and Dockerfiles to be decided based on maintainability and collaboration.
*   **Service Architecture:** Microservices-oriented, with clearly defined APIs between the web application, `podcastfy` backend, and the STT/TTS/LLM services.
*   **Integration Requirements:** Seamless API integration between `podcastfy` and the Dockerized `whisper-asr-webservice`, Ollama, and `Kyutai TTS`.
*   **Security/Compliance:** Focus on local data processing to prevent exposure to external services, adhering to internal data security policies. Initial MVP authentication will be basic, with plans for robust authentication in future phases.

### Constraints & Assumptions

#### Constraints

*   **Deployment Environment:** Initial MVP deployment target is a single gaming PC with a GPU (8GB VRAM) within the internal datacenter. Scalability beyond this will be addressed in future phases.
*   **Open-Source Mandate:** All major components (STT, TTS, LLM, core generation framework) must be open-source to ensure data security, intellectual property control, and avoid reliance on external cloud services.
*   **Time & Resources:** (To be defined with the user) Specific budget, timeline, and available team resources will constrain the MVP scope and features.

#### Key Assumptions

*   **GPU Compatibility:** The chosen open-source AI models (Whisper, Kyutai, Ollama) and their Dockerized implementations are fully compatible with the gaming PC's GPU and associated drivers (e.g., NVIDIA CUDA).
*   **Performance on Gaming PC:** The gaming PC (with 8GB VRAM) will provide sufficient performance for MVP workloads (internal usage volumes) despite potential limitations for very large models or extremely high throughput.
*   **Ollama Stability:** Ollama, when running locally and providing an OpenAI-compatible API, will be stable and reliable enough for core LLM functionalities within the MVP.
*   **Open-Source Maturity & Support:** While acknowledged as a risk, sufficient community support and documentation exist for `podcastfy`, `delayed-streams-modeling` (Kyutai TTS), and `whisper-asr-webservice` to enable successful integration and ongoing maintenance for the MVP.
*   **`21st-dev/magic-mcp` Utility:** The `21st-dev/magic-mcp` tool effectively accelerates UI development and provides suitable components for the web application's requirements.
*   **Internal Network Access:** The gaming PC, once deployed in the datacenter, will have appropriate network connectivity to internal knowledge bases (if relevant for future "Podcast-on-Demand" features) and to the local Dockerized services.

### Risks & Open Questions

#### Key Risks

*   **GPU Memory Constraints:** The 8GB VRAM on the gaming PC might limit the size and complexity of LLM and ASR models that can be run, potentially impacting performance and quality for larger audio files or higher quality podcast generation.
*   **Integration Complexities & Dependency Management:** Integrating multiple disparate open-source projects (`podcastfy`, `delayed-streams-modeling`, `whisper-asr-webservice`, Ollama) could lead to versioning conflicts, complex dependency resolution, and increased development overhead.
*   **Maturity of Research-Oriented TTS:** `delayed-streams-modeling` (Kyutai TTS) is a research-oriented project, which might imply less stable APIs, fewer community resources, and potential maintenance challenges in a production environment compared to more mature solutions.
*   **Performance Bottlenecks with Ollama:** While Ollama offers an OpenAI-compatible API, its local performance and stability under sustained load on the gaming PC need to be validated, especially given its role as the core LLM.
*   **`21st-dev/magic-mcp` Maturity:** The `21st-dev/magic-mcp` is described as "v0," which indicates it might not be fully stable or feature-complete for enterprise-grade UI development, potentially introducing development delays or requiring workarounds.

#### Open Questions

*   What are the specific network configurations and security protocols required for internal datacenter deployment on the gaming PC?
*   What are the exact legal and compliance requirements regarding internal data processing and content generation within our organization?
*   What is the expected average workload (e.g., number and length of podcasts per day/week) for the MVP to accurately assess performance requirements?

#### Areas Needing Further Research

*   **Optimal Model Selection:** Research best-performing Whisper ASR and Ollama LLM models that can operate efficiently within the 8GB VRAM constraint while delivering acceptable quality for different content types.
*   **Licensing Compliance:** A thorough review of the licenses for all integrated open-source components (`podcastfy`, `delayed-streams-modeling`, `whisper-asr-webservice`, Ollama, `21st-dev/magic-mcp`, Coqui XTTS-v2) to ensure full compliance for internal use.
*   **Scalability Beyond MVP:** Explore future options for horizontal scaling (e.g., cluster deployment beyond a single gaming PC) as demand for the service grows.
*   **Alternative TTS Research:** Continue investigating **Coqui XTTS-v2** as a stable and robust alternative or fallback to `delayed-streams-modeling` if integration or stability issues arise with the latter.

### Next Steps

#### Immediate Actions

1.  **Finalize Project Brief:** Obtain final approval for this Project Brief, incorporating all discussions and revisions.
2.  **Resource Allocation:** Define and allocate specific budget, timeline, and human resources for the MVP development.
3.  **Technical Deep Dive:** Conduct detailed technical planning sessions for each component's integration (Kyutai TTS, Ollama, Whisper ASR, `podcastfy` extensions, web app).
4.  **Hardware Procurement/Setup:** Ensure the gaming PC and necessary GPU are ready and configured in the datacenter with Docker and NVIDIA Container Toolkit.
5.  **Licensing Review:** Prioritize the comprehensive review of all open-source component licenses (including Coqui XTTS-v2) to ensure full compliance for internal use.

#### PM Handoff

This Project Brief provides the full context for the AI Podcast Generator project. Once approved, the project can transition to the Product Manager for detailed planning, roadmap development, and execution oversight. The Product Manager will review the brief thoroughly to work with the development team to create the PRD section by section as the template indicates, asking for any necessary clarification or suggesting improvements.
