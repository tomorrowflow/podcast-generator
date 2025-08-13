# Database Schema

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
