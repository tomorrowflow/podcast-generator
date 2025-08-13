# API Specification

**REST API Specification**

```yaml
openapi: 3.0.0
info:
  title: AI Podcast Generator API
  version: 1.0.0
  description: API for managing and generating podcasts
servers:
  - url: http://localhost:8000/api/v1
    description: Development server (local Docker Compose)
paths:
  /podcasts/generate/text:
    post:
      summary: Generate a podcast from text
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                text:
                  type: string
                  description: The text content to convert to a podcast.
                templateId:
                  type: string
                  nullable: true
                  description: Optional ID of a podcast template to use.
      responses:
        '200':
          description: Podcast generation initiated successfully.
          content:
            application/json:
              schema:
                type: object
                properties:
                  podcastId:
                    type: string
                    description: The ID of the newly initiated podcast generation.
                  status:
                    type: string
                    description: Current status of the generation process.
        '400':
          description: Invalid input
  /podcasts/generate/audio:
    post:
      summary: Generate a podcast from an audio file
      requestBody:
        required: true
        content:
          multipart/form-data:
            schema:
              type: object
              properties:
                audioFile:
                  type: string
                  format: binary
                  description: The audio file to transcribe and convert to a podcast.
                templateId:
                  type: string
                  nullable: true
                  description: Optional ID of a podcast template to use.
      responses:
        '200':
          description: Podcast generation initiated successfully.
          content:
            application/json:
              schema:
                type: object
                properties:
                  podcastId:
                    type: string
                    description: The ID of the newly initiated podcast generation.
                  status:
                    type: string
                    description: Current status of the generation process.
        '400':
          description: Invalid input or unsupported audio format
  /podcasts/{podcastId}:
    get:
      summary: Get podcast status and details
      parameters:
        - in: path
          name: podcastId
          schema:
            type: string
          required: true
          description: ID of the podcast to retrieve.
      responses:
        '200':
          description: Podcast details retrieved.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Podcast'
        '404':
          description: Podcast not found
  /podcasts/{podcastId}/download:
    get:
      summary: Download generated podcast audio
      parameters:
        - in: path
          name: podcastId
          schema:
            type: string
          required: true
          description: ID of the podcast to download.
      responses:
        '200':
          description: Audio file downloaded.
          content:
            audio/mpeg:
              schema:
                type: string
                format: binary
        '404':
          description: Podcast not found or audio not yet generated
components:
  schemas:
    Podcast:
      type: object
      properties:
        id:
          type: string
        title:
          type: string
        audioUrl:
          type: string
        duration:
          type: number
        generatedDate:
          type: string
          format: date-time
        sourceText:
          type: string
          nullable: true
        sourceAudioUrl:
          type: string
          nullable: true
        templateUsed:
          type: string
          nullable: true
      required:
        - id
        - title
        - audioUrl
        - duration
        - generatedDate
```
