# Tech Stack

| Category | Technology | Version | Purpose | Rationale |
| :--------- | :--------- | :------ | :-------- | :-------- |
| Frontend Language | TypeScript | Latest Stable | Type-safe development | Enhances code quality, maintainability, and scalability for the web application, especially with `magic-mcp` components. |
| Frontend Framework | React | Latest Stable | Build interactive UIs | A widely adopted, component-based library compatible with `21st-dev/magic-mcp` for efficient UI development. |
| UI Component Library | 21st-dev/magic-mcp compatible components | N/A | UI component generation | Directly supports FR8 and NFR10, simplifying UI development and maintaining consistency. |
| State Management | React Context API | N/A | Localized state management | Sufficient for MVP's simplified user management and avoids external dependencies for quick prototyping. |
| Backend Language | Python | 3.9+ | Main backend logic | Explicitly stated in NFR5 for `podcastfy` backend, supporting integration with LLM/TTS services. |
| Backend Framework | FastAPI | Latest Stable | Build REST APIs | Modern, fast, and asynchronous web framework for Python, suitable for API-driven microservices architecture (implied by `fast_app.py`), easy to integrate with Docker. |
| API Style | REST | N/A | Standardized communication | Common for microservices, provides clear, stateless communication between frontend, `podcastfy` backend, and other services. |
| Database | Local File System | N/A | Audio file storage | For MVP, generated audio files will be stored directly on the local filesystem, aligning with the on-premises deployment and diagram. |
| Cache | None (for MVP) | N/A | Minimal complexity for MVP | Caching is not a critical performance bottleneck for current MVP requirements and can be added later if needed. |
| File Storage | Local File System | N/A | Persistent storage for generated podcasts | Directly supports the storage and retrieval of generated audio files within the local environment. |
| Authentication | Basic Local Authentication | N/A | Simple user management | Aligns with NFR12 for initial MVP user management, providing simple access control without external dependencies. |
| Frontend Testing | Jest, React Testing Library | Latest Stable | Unit/Component testing | Standard testing tools for React applications, ensuring component functionality and UI integrity. |
| Backend Testing | Pytest | Latest Stable | Unit/Integration testing | Widely used Python testing framework, suitable for robust testing of the `podcastfy` backend logic and integrations. |
| E2E Testing | Playwright | Latest Stable | End-to-end testing | Supports cross-browser testing for the full user journey, ensuring the complete system works as expected from UI to backend. |
| Build Tool | Docker | Latest Stable | Containerization | Central to NFR1 and NFR2, ensuring all core components are isolated, portable, and runnable in the on-premises environment. |
| Bundler | Vite | Latest Stable | Frontend asset bundling | Modern and fast build tool for the frontend, optimized for development experience and production performance. |
| IaC Tool | Docker Compose | Latest Stable | Local orchestration | Facilitates the definition and running of multi-container Docker applications on the gaming PC, streamlining local development and deployment (per NFR2). |
| CI/CD | Manual for MVP / Git hooks | N/A | Initial deployment process | For MVP, CI/CD will be manual, potentially using simple Git hooks for basic automation, with future plans for full CI/CD systems like GitHub Actions. |
| Monitoring | Basic Docker Logs | N/A | Initial monitoring | For MVP, relying on standard Docker logging mechanisms for basic insight into container health and application output. |
| Logging | Basic Python Logging / Console Logs | N/A | Application logging | Standard logging practices within Python backend and frontend console for initial debugging and operational visibility. |
| CSS Framework | Tailwind CSS | Latest Stable | Utility-first styling | Provides a flexible and efficient way to style the frontend application, complementary to React development. |
