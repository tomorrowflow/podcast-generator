# External APIs

No external APIs are needed for this project. All core AI services (`Ollama`, `Kyutai TTS`, `Whisper ASR Webservice`) are designed to be deployed locally via Docker Compose and integrated directly with the `podcastfy` backend within the secure, on-premises environment. This design choice explicitly fulfills the non-functional requirement (NFR3) of maintaining zero instances of sensitive internal data exposure to external services. Therefore, there are no third-party APIs that require separate documentation, authentication, or rate limit considerations in this architecture.
