# Unified Project Structure

```plaintext
AI-Podcast-Generator/
├── .github/                    # CI/CD workflows (if implemented later)
│   └── workflows/
│       ├── ci.yaml
│       └── deploy.yaml
├── apps/                       # Application packages
│   ├── web-app/                # Frontend application (React with Vite/Tailwind)
│   │   ├── src/
│   │   │   ├── components/     # UI components (potentially enhanced by 21st-dev/magic-mcp)
│   │   │   ├── features/       # Feature-specific components and logic
│   │   │   ├── hooks/          # Custom React hooks
│   │   │   ├── pages/          # Route-level components
│   │   │   ├── services/       # API client services (e.g., podcastService.ts)
│   │   │   ├── stores/         # State management (e.g., Zustand stores)
│   │   │   ├── styles/         # Global styles/themes, Tailwind CSS config
│   │   │   ├── utils/          # Frontend utilities
│   │   │   └── main.tsx        # Entry point
│   │   ├── public/             # Static assets
│   │   ├── tests/              # Frontend tests (Jest, React Testing Library)
│   │   ├── package.json        # Frontend package manifest
│   │   └── vite.config.ts      # Vite configuration
│   └── api/                    # API gateway / orchestration layer for podcastfy backend (FastAPI)
│       ├── src/
│       │   ├── routes/         # API routes/controllers (FastAPI routers)
│       │   ├── core/           # Business logic, generation orchestration
│       │   ├── tts/            # TTS provider interfaces
│       │   ├── llm/            # LLM provider interfaces
│       │   ├── asr/            # ASR provider interfaces
│       │   ├── config/         # Backend configurations
│       │   ├── dependencies.py # FastAPI dependency injection
│       │   └── main.py         # FastAPI application entry point
│       ├── tests/              # Backend tests (Pytest)
│       ├── requirements.txt    # Python dependencies for API
│       └── Dockerfile          # Dockerfile for FastAPI service
├── packages/                   # Shared packages
│   ├── shared-types/           # Shared TypeScript interfaces and utility types
│   │   ├── src/
│   │   │   ├── index.ts
│   │   │   └── types.ts        # e.g., Podcast.ts, User.ts
│   │   └── package.json
│   ├── ui-components/          # Shared UI components (if needed, e.g., via Storybook)
│   │   ├── src/
│   │   └── package.json
│   └── config/                 # Shared configurations (e.g., ESLint, Prettier, TSConfig)
│       ├── eslint/
│       ├── typescript/
│       └── jest/
├── services/                   # Dockerized AI services and podcastfy core
│   ├── podcastfy/              # Original podcastfy source (potentially as a separate package/submodule or directly copied)
│   │   ├── podcastfy/
│   │   ├── Dockerfile          # Original podcastfy Dockerfile
│   │   └── ...                 # All other podcastfy source files
│   ├── ollama/                 # Ollama service definition (e.g., custom models, config)
│   │   └── Dockerfile          # Ollama Docker setup (if custom)
│   └── whisper-asr-webservice/ # Whisper ASR service (Dockerized)
│       └── Dockerfile          # Whisper ASR Docker setup
├── infrastructure/             # IaC definitions (e.g., for cloud deployment if expanded)
│   └── docker-compose.yml      # Main Docker Compose file at monorepo root
├── scripts/                    # Build, deploy, and utility scripts
├── tools/                      # Monorepo management tools (e.g., Nx, Turborepo configs)
├── .env.example                # Environment variables template
├── package.json                # Root package.json (for monorepo tool and shared scripts)
├── {{monorepo_config}}         # Monorepo configuration file (e.g., turborepo.json, nx.json)
└── README.md
```
