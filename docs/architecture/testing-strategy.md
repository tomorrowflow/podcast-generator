# Testing Strategy

**1. Testing Pyramid**

```text
          E2E Tests
         /        \
    Integration Tests
       /            \
  Frontend Unit  Backend Unit
```

---

**2. Test Organization**

*   **Frontend Tests:**
    ```text
    web-app/
    ├── src/
    │   ├── components/
    │   │   ├── MyComponent.test.tsx   # Unit tests for individual components
    │   │   └── MyComponent.stories.tsx # Storybook for component isolation/testing (also visual regression baseline)
    │   ├── features/
    │   │   ├── MyFeature/
    │   │   │   ├── components/
    │   │   │   │   └── SubComponent.test.tsx
    │   │   │   └── MyFeature.test.tsx # Integration tests for feature logic/components
    │   ├── services/
    │   │   └── podcastService.test.ts # Unit tests for API service layer
    │   ├── hooks/
    │   │   └── useMyHook.test.ts      # Unit tests for custom hooks
    │   ├── __mocks__/                 # Centralized API mocks for isolated frontend testing (e.g., Mock Service Worker)
    │   │   ├── handlers.ts
    │   │   └── server.ts
    │   └── test-data/                 # Reusable test data generators/factories (e.g., Faker.js)
    │       ├── podcast-factory.ts
    │       └── user-factory.ts
    ├── tests/ # End-to-End and Visual Regression tests
    │   ├── e2e/
    │   │   ├── auth.spec.ts
    │   │   └── podcastGeneration.spec.ts
    │   └── visual-regression/         # Dedicated folder for visual regression snapshots/configurations
    │       ├── homepage.spec.ts
    │       └── component.spec.ts
    ├── jest.config.ts # Jest configuration for unit/integration tests
    └── playwright.config.ts # Playwright configuration for E2E and Visual Regression
    ```
    *   **Visual Regression Testing:** Integrated with Playwright for full-page snapshot comparisons or Storybook addons for component-level visual tests. This ensures UI consistency across changes and device sizes.
    *   **Accessibility Testing:** Automated checks using tools like `axe-core` integrated into Jest (e.g., `jest-axe`) or Playwright tests, focusing on WCAG guidelines and semantic HTML during component development.
    *   **Frontend Test Data Management:** Utilize Mock Service Worker (MSW) or similar for network level mocking of API responses. Employ reusable data factories (e.g., `faker.js` based) in `test-data/` to generate consistent, realistic, and varied test data for components and integration tests.

*   **Backend Tests:**
    ```text
    api/
    ├── src/
    │   ├── api/
    │   │   └── routes/
    │   │       └── podcast_routes.test.py # Integration tests for API endpoints
    │   ├── core/
    │   │   ├── generation_service.test.py # Unit/Integration tests for core logic
    │   │   └── podcast_management.test.py
    │   ├── tts/
    │   │   └── providers/
    │   │       └── kyutai_tts_provider.test.py # Unit tests for provider interfaces
    │   └── llm/
    │       └── ollama_client.test.py
    ├── tests/ # Root for backend tests if not co-located
    │   └── conftest.py # Pytest fixtures
    └── pytest.ini # Pytest configuration
    ```

*   **E2E Tests:**
    ```text
    # Located in web-app/tests/e2e/ (as shown above)
    web-app/
    ├── tests/
    │   └── e2e/
    │       ├── fixtures/ # Test data, helper functions
    │       ├── reporters/ # Custom reporters
    │       ├── screenshots/ # Output for screenshot comparisons
    │       ├── videos/ # Output for video recordings of tests
    │       ├── auth.spec.ts # User authentication flow
    │       ├── podcastGeneration.spec.ts # Text-to-podcast and Audio-to-podcast flows
    │       └── management.spec.ts # Podcast listing and management
    └── playwright.config.ts # Playwright configuration
    ```

---
**3. Test Examples**

*   **Frontend Component Test (React Testing Library/Jest):**
    ```typescript
    // web-app/src/components/PodcastCard/PodcastCard.test.tsx
    import React from 'react';
    import { render, screen } from '@testing-library/react';
    import PodcastCard from './PodcastCard';
    import { Podcast } from '../../../packages/shared-types/types'; // Using shared type

    const mockPodcast: Podcast = {
      id: '123',
      title: 'Test Podcast',
      audioUrl: 'http://example.com/audio.mp3',
      duration: 120,
      generatedDate: '2025-01-01T10:00:00Z',
    };

    describe('PodcastCard', () => {
      it('renders podcast title and duration', () => {
        render(<PodcastCard podcast={mockPodcast} />);
        expect(screen.getByText('Test Podcast')).toBeInTheDocument();
        expect(screen.getByText('2 min')).toBeInTheDocument(); // Assuming formatting util
      });

      it('displays play and download buttons', () => {
        render(<PodcastCard podcast={mockPodcast} />);
        expect(screen.getByRole('button', { name: /play/i })).toBeInTheDocument();
        expect(screen.getByRole('link', { name: /download/i })).toHaveAttribute('href', mockPodcast.audioUrl);
      });

      // Add more tests for interactivity, missing data, etc.
    });
    ```

*   **Backend API Test (Pytest/FastAPI TestClient):**
    ```typescript
    # api/src/api/routes/podcast_routes.test.py
    from fastapi.testclient import TestClient
    from main import app # Assuming main.py is the FastAPI app entry
    import pytest

    client = TestClient(app)

    # For simplicity, mocking external services like Ollama, KyutaiTTS, WhisperASR
    # In a real scenario, use monkeypatching or dedicated test containers.
    # For MVP, direct unit tests of generate functions is probably sufficient.

    def test_generate_podcast_from_text_success():
        response = client.post(
            "/podcasts/generate/text",
            json={"text": "This is a test script.", "templateId": "default"},
        )
        assert response.status_code == 200
        assert "podcastId" in response.json()
        assert response.json()["status"] == "processing"

    def test_generate_podcast_from_text_invalid_input():
        response = client.post(
            "/podcasts/generate/text",
            json={"invalid_field": "oops"},
        )
        assert response.status_code == 422 # FastAPI validation error
        assert "detail" in response.json()

    # Add more tests for audio generation, retrieval, download, error cases etc.
    ```

*   **E2E Test (Playwright):**
    ```typescript
    // web-app/tests/e2e/podcastGeneration.spec.ts
    import { test, expect } from '@playwright/test';

    test.describe('Podcast Generation E2E Flow', () => {
      test('should generate a podcast from text and allow playback', async ({ page }) => {
        await page.goto('/'); // Assuming base URL is configured in playwright.config.ts

        // 1. Login (if authentication is enabled)
        // await page.fill('input[name="username"]', 'testuser');
        // await page.fill('input[name="password"]', 'testpassword');
        // await page.click('button:has-text("Login")');
        // await expect(page).toHaveURL('/dashboard');

        // 2. Navigate to Text-to-Podcast generation page
        await page.click('button:has-text("Generate Podcast")');
        await expect(page).toHaveURL('/generate');

        // 3. Input text and select template
        await page.fill('textarea[placeholder="Enter your script here..."]', 'This is an automated test to generate a short podcast.');
        await page.selectOption('select[name="template"]', { label: 'News Update' }); // Assuming a template selector

        // 4. Submit for generation
        await page.click('button:has-text("Generate Podcast")');

        // 5. Verify progress bar appears (FR9)
        await expect(page.locator('div.progress-bar')).toBeVisible();
        await expect(page.locator('div.progress-logs')).toBeVisible();

        // 6. Wait for success message (or for a specific element to appear, like playback button)
        await expect(page.locator('text=Podcast generated successfully!')).toBeVisible({ timeout: 60000 }); // Wait up to 60 seconds

        // 7. Verify playback and download options (FR7)
        await expect(page.locator('audio')).toBeVisible();
        await expect(
---

**5. Advanced Resilience Patterns**

For increased operational readiness beyond the MVP, the following resilience patterns should be considered and integrated, especially for interactions with the AI core services:

*   **Retry Policies:**
    *   **Mechanism:** Implement retry logic for transient failures (e.g., network glitches, temporary service unavailability) when `podcastfy` backend communicates with Ollama, Kyutai TTS, and Whisper ASR.
    *   **Approach:** Use exponential backoff with a maximum number of retries (`e.g., 3-5 attempts`). Differentiate between retriable (e.g., 500, 503, connection errors) and non-retriable (e.g., 400, 403, 404) errors.
    *   **Implementation:** Libraries like `tenacity` (Python) can be used to easily apply retry decorators to service calls.

*   **Circuit Breakers:**
    *   **Mechanism:** Apply circuit breaker patterns to calls to external or internal critical services (Ollama, Kyutai TTS, Whisper ASR) to prevent cascading failures.
    *   **Approach:** If a service consistently fails or becomes unresponsive, the circuit opens, preventing further calls for a defined period. After a timeout, it can transition to a half-open state to test if the service has recovered.
    *   **Implementation:** Consider libraries such as `pybreaker` (Python) or built-in frameworks features if available.

*   **Graceful Degradation:**
    *   **Mechanism:** Define how the application behaves if certain non-critical features or services become unavailable to maintain core functionality.
    *   **Approach:** For example, if a specific podcast template lookup fails, the system could fall back to a "default" podcast generation style instead of failing the entire request. If the transcription service is slow, provide clearer feedback to the user on the delay.
    *   **Considerations:** Prioritize core "text-to-audio" and "audio-to-text" flows. Non-essential features should fail gracefully (e.g., disable template selection if template service is down, or use a cached version if up-to-date templates cannot be fetched).
    *   **User Feedback:** Enhance the progress bar (FR9) to clearly indicate when a fallback mechanism is active or if a specific sub-process is experiencing delays, managing user expectations.

*   **Automated Recovery:**
    *   **Mechanism:** For long-running processes like podcast generation, implement mechanisms to checkpoint progress or store intermediate results, allowing for resuming failed operations.
    *   **Approach:** If a generation process fails at the TTS stage, store the LLM-generated text and allow the system (or user) to re-initiate from that point rather than restarting from raw input.
    *   **Implementation:** This might involve defining a state machine for podcast generation requests that tracks stages and allows for retries at specific points.