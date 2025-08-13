# Frontend Architecture

**1. Component Architecture**

**Component Organization:**
```text
src/
├── app/                     # Main application structure
├── components/              # Reusable UI components (e.g., Button, Modal)
├── features/                # Feature-specific components and logic (e.g., PodcastGenerator, PodcastList)
│   ├── PodcastGenerator/
│   │   ├── components/
│   │   ├── hooks/
│   │   ├── slices/          # State management slices (if using Redux Toolkit/Zustand)
│   │   └── index.ts
│   └── PodcastList/
│       ├── components/
│       └── index.ts
├── hooks/                   # General custom React hooks (e.g., useAuth, useApi)
├── pages/                   # Route-level components (e.g., HomePage, DashboardPage)
├── services/                # API client and logic for communicating with backend
├── stores/                  # Global state management (e.g., context providers, Zustand store)
├── styles/                  # Global styles, Tailwind CSS configuration
├── utils/                   # General utility functions
├── assets/                  # Static assets (images, icons)
├── types/                   # Shared TypeScript types for frontend (could be from packages/shared)
└── main.tsx                 # Entry point
```

**Component Template:**
```typescript
// src/components/ExampleComponent/ExampleComponent.tsx
import React from 'react';

interface ExampleComponentProps {
  // Define component props here
  title: string;
  description?: string;
}

const ExampleComponent: React.FC<ExampleComponentProps> = ({ title, description }) => {
  return (
    <div className="p-4 bg-white shadow rounded-lg">
      <h2 className="text-xl font-semibold mb-2">{title}</h2>
      {description && <p className="text-gray-600">{description}</p>}
      {/* Additional UI elements */}
    </div>
  );
};

export default ExampleComponent;

// src/components/ExampleComponent/index.ts (barrel file for easy import)
export { default } from './ExampleComponent';
export type { ExampleComponentProps } from './ExampleComponent';
```

---

**2. State Management Architecture**

**State Structure:**
```typescript
// src/stores/podcastStore.ts (Example using a Zustand-like pattern)
import create from 'zustand';
import { Podcast } from '../types'; // Assuming shared types are in `src/types` or `packages/shared/types`

interface PodcastState {
  podcasts: Podcast[];
  currentGeneratingPodcastId: string | null;
  generationProgress: {
    podcastId: string;
    stage: string;
    percentage: number;
    logs: string[];
  } | null;
  addPodcast: (podcast: Podcast) => void;
  updateGenerationProgress: (podcastId: string, stage: string, percentage: number, newLog?: string) => void;
  resetGenerationProgress: () => void;
  // ... other actions like fetchPodcasts, deletePodcast
}

export const usePodcastStore = create<PodcastState>((set) => ({
  podcasts: [],
  currentGeneratingPodcastId: null,
  generationProgress: null,
  addPodcast: (podcast) => set((state) => ({ podcasts: [...state.podcasts, podcast] })),
  updateGenerationProgress: (podcastId, stage, percentage, newLog) => set((state) => ({
    currentGeneratingPodcastId: podcastId,
    generationProgress: {
      podcastId,
      stage,
      percentage,
      logs: newLog ? [...(state.generationProgress?.logs || []), newLog] : (state.generationProgress?.logs || []),
    },
  })),
  resetGenerationProgress: () => set({ generationProgress: null, currentGeneratingPodcastId: null }),
}));

// src/stores/authStore.ts (for basic user state)
interface AuthState {
  user: { id: string; username: string } | null;
  isLoggedIn: boolean;
  login: (user: { id: string; username: string }) => void;
  logout: () => void;
}

export const useAuthStore = create<AuthState>((set) => ({
  user: null,
  isLoggedIn: false,
  login: (user) => set({ user, isLoggedIn: true }),
  logout: () => set({ user: null, isLoggedIn: false }),
}));
```

**State Management Patterns:**
- **Centralized Store:** A single source of truth for application state (e.g., `usePodcastStore`, `useAuthStore`).
- **Updater Functions:** State updates are done via explicit updater functions (e.g., `addPodcast`, `updateGenerationProgress`) rather than direct state mutation, ensuring immutability.
- **Derived State:** Compute derived state (e.g., `isLoggedIn`) from raw state for efficiency and consistency.
- **Asynchronous Actions:** Handle API calls and other asynchronous operations within the store actions or separate service layers that then update the store.

---

**3. Routing Architecture**

**Route Organization:**
```text
src/
├── pages/
│   ├── index.tsx          # Homepage/Dashboard (e.g., displays recent podcasts)
│   ├── generate/
│   │   ├── index.tsx      # Text/Audio submission form
│   │   └── [id].tsx       # Dynamic route for podcast generation progress/details
│   ├── podcasts/
│   │   ├── index.tsx      # List of generated podcasts (e.g., for management)
│   │   └── [id].tsx       # Individual podcast playback/download page
│   ├── auth/
│   │   ├── login.tsx
│   │   └── register.tsx
│   └── _app.tsx           # Global App component/layout
└── AppRouter.tsx          # Centralized routing configuration
```

**Protected Route Pattern:**
```typescript
// src/components/ProtectedRoute.tsx
import React from 'react';
import { useAuthStore } from '../stores/authStore';
import { Navigate, Outlet } from 'react-router-dom'; // Assuming React Router Dom

interface ProtectedRouteProps {
  allowedRoles?: string[]; // Optional role-based access
}

const ProtectedRoute: React.FC<ProtectedRouteProps> = ({ allowedRoles }) => {
  const isLoggedIn = useAuthStore((state) => state.isLoggedIn);
  const user = useAuthStore((state) => state.user); // Get user to check roles

  if (!isLoggedIn) {
    // Redirect unauthenticated users to login page
    return <Navigate to="/auth/login" replace />;
  }

  // Optional: Check for roles if allowedRoles is provided
  // if (allowedRoles && user && !allowedRoles.includes(user.role)) {
  //   return <Navigate to="/unauthorized" replace />;
  // }

  return <Outlet />; // Render child routes if authenticated
};

export default ProtectedRoute;

// Usage in AppRouter.tsx
// <Route element={<ProtectedRoute />}>
//   <Route path="/generate" element={<GeneratePodcastPage />} />
//   <Route path="/podcasts" element={<PodcastListPage />} />
// </Route>
```

---

**4. Frontend Services Layer**

**API Client Setup:**
```typescript
// src/services/api.ts
import axios from 'axios';

const API_BASE_URL = import.meta.env.VITE_API_BASE_URL || 'http://localhost:8000/api/v1';

const apiClient = axios.create({
  baseURL: API_BASE_URL,
  headers: {
    'Content-Type': 'application/json',
    // 'Authorization': `Bearer ${token}` // Add token if authentication implemented
  },
});

// Interceptor for handling global errors (e.g., network issues)
apiClient.interceptors.response.use(
  response => response,
  error => {
    console.error('API call error:', error);
    // You could show a global error notification here
    return Promise.reject(error);
  }
);

export default apiClient;
```

**Service Example:**
```typescript
// src/services/podcastService.ts
import apiClient from './api';
import { Podcast } from '../types'; // Shared type

interface GenerateTextRequest {
  text: string;
  templateId?: string;
}

interface GenerateAudioFormData extends FormData {
  audioFile: File;
  templateId?: string;
}

interface PodcastGenerationResponse {
  podcastId: string;
  status: string;
}

const podcastService = {
  generatePodcastFromText: async (data: GenerateTextRequest): Promise<PodcastGenerationResponse> => {
    const response = await apiClient.post<PodcastGenerationResponse>('/podcasts/generate/text', data);
    return response.data;
  },

  generatePodcastFromAudio: async (data: GenerateAudioFormData): Promise<PodcastGenerationResponse> => {
    const response = await apiClient.post<PodcastGenerationResponse>('/podcasts/generate/audio', data, {
      headers: {
        'Content-Type': 'multipart/form-data', // Important for file uploads
      },
    });
    return response.data;
  },

  getPodcastDetails: async (podcastId: string): Promise<Podcast> => {
    const response = await apiClient.get<Podcast>(`/podcasts/${podcastId}`);
    return response.data;
  },

  downloadPodcast: async (podcastId: string): Promise<Blob> => {
    const response = await apiClient.get(`/podcasts/${podcastId}/download`, {
      responseType: 'blob', // Important for binary data like audio
    });
    return response.data;
  },

  // Example for streaming logs/progress (conceptually, actual implementation might use WebSockets/SSE)
  // watchPodcastGeneration: (podcastId: string, onUpdate: (progress: any) => void) => {
  //   // This would typically involve WebSockets or Server-Sent Events (SSE)
  //   // For MVP polling on /podcasts/{podcastId} is assumed sufficient (FR9)
  // }
};

export default podcastService;
```
