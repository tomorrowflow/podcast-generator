# Security and Performance

**1. Security Requirements**

*   **Frontend Security:**
    *   **CSP Headers:** Will be configured minimally for the MVP, primarily allowing scripts and styles from the application's own domain. This will be integrated into the web server serving the frontend (e.g., Nginx in a Docker container).
        *   _Recommendation:_ `Content-Security-Policy: default-src 'self'; script-src 'self' 'unsafe-inline'; style-src 'self' 'unsafe-inline';` (Fine-tune `unsafe-inline` if necessary).
    *   **XSS Prevention:** Frontend framework (React) inherently provides protection against XSS by escaping rendered content. Additional measures will involve strict input sanitization on both frontend and backend for any user-generated content.
    *   **Secure Storage:** Authentication tokens (JWTs) will be stored in `HttpOnly` cookies to prevent JavaScript access and XSS attacks. Sensitive user data will not be stored client-side.

*   **Backend Security:**
    *   **Input Validation:** Strict server-side input validation will be enforced for all API endpoints using FastAPI's Pydantic models. This is critical for preventing injection attacks and ensuring data integrity.
    *   **Rate Limiting:** IP-based rate limiting will be implemented on key API endpoints (e.g., `/podcasts/generate/text`, `/auth/login`) to prevent abuse and denial-of-service attacks.
        *   _Implementation:_ FastAPI middleware or integrated with a reverse proxy (e.g., Nginx).
    *   **CORS Policy:** A strict CORS policy will be configured to allow requests only from the frontend application's origin(s) (`http://localhost:3000` for development, `http://<gaming-pc-ip>:3000` for "production").

*   **Authentication Security:**
    *   **Token Storage:** Authentication tokens will be stored in `HttpOnly` and `Secure` cookies. This ensures tokens are not accessible via client-side JavaScript, mitigating XSS risks, and are only sent over HTTPS.
    *   **Session Management:** Token-based session management (likely JWTs as discussed) will be stateless on the backend, relying on token validation for each request. Token expiration will be enforced.
    *   **Password Policy:** For MVP, a basic password policy will involve hashing and salting passwords (e.g., using `bcrypt`). Future iterations can enforce complexity requirements (min length, special chars, etc.).

*   **Infrastructure Security Considerations:**
    *   **Network Segmentation:**
        *   **External Access:** The `web-app` container will expose necessary HTTP/HTTPS ports (e.g., 80/443) only. Access should be restricted to internal network ranges or specific IPs.
        *   **Internal Communication:** `podcastfy` backend and AI services (Ollama, Kyutai TTS, Whisper ASR) will communicate over Docker's internal bridge network, which is isolated from the host and external networks by default. No ports for these internal services should be exposed directly to the host or external networks.
    *   **Host-Level Firewall:** Implement a host-level firewall (e.g., `ufw` on Linux, macOS built-in firewall) to only allow inbound connections on ports required by the `web-app` (e.g., 80/443, 22 for SSH management) and explicitly block all other incoming traffic.
    *   **Service Isolation and Least Privilege:**
        *   **Container Users:** Containers will run with non-root users where possible. Dockerfiles will specify `USER` instruction when appropriate base images support it.
        *   **Volume Permissions:** Mount volumes with the principle of least privilege, ensuring `podcastfy` backend container only has read/write access to its designated `/data/podcasts` volume.
        *   **Secrets Management:** Environment variables containing sensitive information (e.g., `APP_SECRET_KEY`) will be passed to containers securely via Docker Compose environment variables, `.env` files (for local dev), and never hardcoded.
        *   **Resource Limits:** Docker Compose configurations will define appropriate CPU and memory limits for containers to prevent resource exhaustion attacks.

---

**2. Performance Optimization**

*   **Frontend Performance:**
    *   **Bundle Size Target:** Aim for smaller JavaScript bundles (e.g., < 500KB gzipped) to ensure fast initial page loads. This will be managed through code splitting, lazy loading, and efficient bundling (Vite helps here).
    *   **Loading Strategy:** Implement lazy loading for routes and components that are not critical for initial render. Display loading indicators for asynchronous operations (e.g., podcast generation progress bar).
    *   **Caching Strategy:** Utilize browser caching for static assets (JavaScript, CSS, images) with appropriate cache-control headers. No aggressive caching of dynamic data for MVP.

*   **Backend Performance:**
    *   **Response Time Target:** Aim for API response times (excluding long-running AI generation) under 200-300ms for routine operations (e.g., fetching podcast list, user authentication).
    *   **Identified Bottlenecks:**
        *   **AI Model Inference:** The primary bottleneck is expected to be the time taken by Ollama, Kyutai TTS, and Whisper ASR for processing input and generating output, heavily reliant on GPU performance.
        *   **Audio File I/O:** Reading/writing large audio files to local storage can be a bottleneck.
    *   **Database Optimization:** For local file storage, optimize read/write operations by reading/writing only necessary data and performing any filtering/sorting in-memory for smaller datasets. For scaling beyond MVP, consider indexing and database-specific optimizations if a formal database is introduced.
    *   **Caching Strategy:** For MVP, no explicit backend caching (beyond OS-level file caching). If performance bottlenecks arise, consider in-memory caches (e.g., Redis) or database query caching.
    *   **Load Balancing (Future/Scaling):** For a single-machine MVP, load balancing is N/A. In a scaled deployment, internal load balancing (e.g., Nginx, Envoy proxy) would distribute API requests across multiple `podcastfy` backend instances.
    *   **Horizontal and Vertical Scaling Strategies (Future):**
        *   **Vertical Scaling:** Upgrade the underlying gaming PC's CPU, RAM, and especially GPU (VRAM) as initial scaling.
        *   **Horizontal Scaling:**
            *   **Backend API:** Deploy multiple `podcastfy` backend API instances behind a load balancer. FastAPI is designed for concurrency.
            *   **AI Services:** Deploy multiple instances of Ollama, Kyutai TTS, and Whisper ASR containers, each capable of utilizing GPU resources. This would require intelligent routing/load management at the `podcastfy` backend level.
            *   **Queuing:** Introduce a message queue (e.g., RabbitMQ, Kafka) for asynchronous processing of podcast generation requests, decoupling the request from the long-running AI tasks. This allows the backend API to respond quickly and process requests in the background.
    *   **Resource Sizing Recommendations (MVP):**
        *   **Gaming PC:** Minimum 8 CPU cores, 16 GB RAM, 8 GB VRAM (for GPU-accelerated AI models).
        *   **Disk Space:** Initial 100GB for audio file storage, scalable as needed.

---

**3. Data Security Details**

To enhance data security and address critical gaps, the following approaches will be adopted:

*   **Data Encryption:**
    *   **At Rest:** Local file storage (for podcast MP3s and JSON metadata) will be encrypted at the filesystem layer of the host gaming PC (e.g., using macOS FileVault, Linux dm-crypt, or Windows BitLocker). Application-level encryption will be considered for highly sensitive metadata, if any is identified beyond current scope.
    *   **In Transit:** All internal API communication within the Docker network will rely on Docker's default secure networking. External access to the web application will be secured via HTTPS (e.g., through an Nginx proxy with SSL termination). Data transmitted to AI services (Ollama, KyutaiTTS, Whisper ASR) will occur over secure internal Docker networks.

*   **Sensitive Data Handling Procedures:**
    *   **Identification:** Regularly review data inputs (source text, transcribed audio) for sensitive internal information (PII, confidential company data).
    *   **Minimization:** Only process and store the minimum necessary sensitive data.
    *   **Temporary Storage:** Transcribed audio and intermediate LLM outputs containing sensitive data will be purged immediately after successful podcast generation, not retained.
    *   **Access Control:** Access to raw audio inputs, transcribed texts, and generated podcasts on the local file system will be restricted to authorized `podcastfy` backend processes only, enforced at the operating system level.
    *   **Sanitization/Redaction:** Explore capabilities within the LLM (Ollama) to redact or anonymize sensitive information from generated podcast scripts as a proactive measure, if identiied by PRD or future requirements.

*   **Data Retention and Purging Policies:**
    *   **Generated Podcasts:** MP3 audio files and their metadata will be retained for a configurable period (e.g., 30 days default for MVP) to allow for user retrieval. Beyond this, an automated purging process will securely delete older files.
    *   **Source Data:** Original text inputs or uploaded audio files will be deleted immediately after successful processing, aligning with data minimization and security goals. Transcribed texts will be purged immediately after LLM processing.
    *   **User Data:** User credentials (passwords) will be hashed and salted indefinitely. Other user metadata (e.g., email) will adhere to a 90-day retention policy after user account deletion.

*   **Backup Encryption:**
    *   **Strategy:** Implement regular backups of generated podcasts and critical configuration files (users.json, templates.json/yaml) to an encrypted storage location on the local network or a physically secured external drive.
    *   **Encryption at Rest:** Backups will be encrypted using strong, industry-standard encryption methods (e.g., AES-256) on the backup target.
    *   **Frequency:** Daily backups of configuration, weekly backups of generated podcasts (or as needed, based on volume).
    *   **Recovery:** Document a detailed procedure for restoring the application and its data from backups in case of disaster, including decryption steps.
