# Data Models

**Data Model: Podcast**

**Purpose:** Represents a generated audio podcast, containing the audio file itself and associated metadata.

**Key Attributes:**
*   `id`: `string` - Unique identifier for the podcast.
*   `title`: `string` - User-provided or AI-generated title of the podcast.
*   `audioUrl`: `string` - URL or path to the stored audio file.
*   `duration`: `number` - Duration of the podcast in seconds.
*   `generatedDate`: `string` (ISO 8601) - Timestamp of when the podcast was generated.
*   `sourceText`?: `string` - Original text used to generate the podcast (optional).
*   `sourceAudioUrl`?: `string` - URL or path to the original audio input (optional, if generated from audio).
*   `templateUsed`?: `string` - Identifier of the template used for generation (optional).

**TypeScript Interface:**
```typescript
interface Podcast {
  id: string;
  title: string;
  audioUrl: string;
  duration: number;
  generatedDate: string; // ISO 8601 format
  sourceText?: string;
  sourceAudioUrl?: string;
  templateUsed?: string;
}
```

**Relationships:**
*   A Podcast is generated from `Text Input` OR `Audio Input`.
*   A Podcast may be associated with a `Podcast Template`.
*   A Podcast is owned by a `User`.

**Data Model: User**

**Purpose:** Represents a user of the AI Podcast Generator, primarily for basic authentication (per NFR12).

**Key Attributes:**
*   `id`: `string` - Unique identifier for the user.
*   `username`: `string` - User's unique username for login.
*   `passwordHash`: `string` - Hashed password for security.
*   `email`?: `string` - Optional user email.

**TypeScript Interface:**
```typescript
interface User {
  id: string;
  username: string;
  passwordHash: string;
  email?: string;
}
```

**Relationships:**
*   A User can generate multiple `Podcasts`.

**Data Model: PodcastTemplate**

**Purpose:** Represents a pre-designed template that guides the generation of podcasts (per FR10, Story 3.1, Story 3.2).

**Key Attributes:**
*   `id`: `string` - Unique identifier for the template.
*   `name`: `string` - Display name of the template (e.g., "Interview", "News Update").
*   `description`: `string` - Brief explanation of what the template does.
*   `llmPromptInstructions`: `string` - Specific instructions for the LLM based on the template.
*   `ttsConfig`?: `object` - Optional configuration for TTS (e.g., specific voice, style).

**TypeScript Interface:**
```typescript
interface PodcastTemplate {
  id: string;
  name: string;
  description: string;
  llmPromptInstructions: string;
  ttsConfig?: {
    // Define specific TTS config properties here, e.g.,
    voiceId?: string;
    style?: string;
  };
}
```

**Relationships:**
*   A `Podcast` can be generated using a `PodcastTemplate`.
