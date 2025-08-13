# Epic 3: Template Integration & Basic Content Management

**Expanded Goal:** This epic aims to empower internal content creators with pre-designed podcast templates, enabling them to quickly generate podcasts in various formats (e.g., interview, news update). Concurrently, it will lay the foundational groundwork for essential content management features, such as organizing and potentially categorizing generated audio outputs, enhancing usability and streamlining the overall content creation workflow.

## Story 3.1: Implement Podcast Template Selection in UI

As an **Internal Content Creator**,
I want to **choose from a library of pre-designed podcast templates**,
so that **I can quickly generate podcasts in various formats.**

### Acceptance Criteria

1.  **1:** The web application UI includes a dedicated section or dropdown for selecting podcast templates.
2.  **2:** A clear list of available templates (e.g., "Interview," "News Update," "Storytelling") is displayed.
3.  **3:** Selecting a template updates the generation parameters (e.g., instructing the LLM to generate content in a specific format) for the subsequent podcast creation process.
4.  **4:** The UI provides a brief description or preview for each template to inform user selection.

## Story 3.2: Backend Integration for Template-Driven Content Generation

As a **System Integrator**,
I want the `podcastfy` backend to **apply selected templates during content generation**,
so that **the generated podcasts adhere to specific structural and stylistic formats.**

### Acceptance Criteria

1.  **1:** The `podcastfy` backend receives the selected template identifier along with the user's input (text or transcribed audio).
2.  **2:** The backend logic incorporates the template's guidelines when instructing `Ollama` for content generation (e.g., structuring the narrative, assigning roles for multi-speaker formats).
3.  **3:** The generated text from `Ollama` reflects the chosen template's structure and style requirements.
4.  **4:** The subsequent TTS process (via `Kyutai TTS`) correctly synthesizes audio based on the template-driven content.

## Story 3.3: Implement Basic Generated Podcast Organization

As an **Internal Content Creator**,
I want to **easily find and manage my generated podcasts**,
so that **my content library is organized and accessible.**

### Acceptance Criteria

1.  **1:** The web application UI includes a dedicated section for viewing previously generated podcasts.
2.  **2:** Podcasts are displayed in a list or grid format, showing key metadata (e.g., title, generation date, duration).
3.  **3:** Users can sort or filter the list of generated podcasts by basic criteria (e.g., date, name).
4.  **4:** Users can easily rename generated podcast files or titles within the UI.
5.  **5:** The underlying storage mechanism for generated podcasts supports the organization and retrieval based on UI interactions.