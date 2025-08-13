# Deployment Architecture

**1. Deployment Strategy**

Deployment will leverage Docker Compose for orchestrating the multi-container application on the local gaming PC.

*   **Frontend Deployment (`web-app`):**
    *   **Platform:** Docker Container (within local Docker Compose)
    *   **Build Command:** `npm run build` (executed within the Dockerfile or build step, producing static assets)
    *   **Output Directory:** `dist/` (standard for Vite builds)
    *   **Serving Mechanism:** A lightweight web server (e.g., Nginx or directly served by the frontend framework's build output) within its Docker container.
    *   **CDN/Edge:** N/A (local deployment, no CDN involved).

*   **Backend Deployment (`api` - FastAPI/podcastfy wrapper):**
    *   **Platform:** Docker Container (within local Docker Compose)
    *   **Build Command:** `poetry install --no-root --no-dev && poetry build` (or `pip install -r requirements.txt`) followed by copying application code into the Docker image.
    *   **Deployment Method:** Run as a long-running Uvicorn server process inside its Docker container, managed by Docker Compose.

---

**2. CI/CD Pipeline**

For the MVP, CI/CD will be manual, primarily managed via direct Docker Compose commands. This approach is practical for a single-machine, internal-facing application. Future iterations could integrate more sophisticated CI/CD tooling like GitHub Actions or Jenkins.

*   **CI/CD Pipeline Configuration (Conceptual - Manual / Scripted):**

    ```yaml
    # Example of a conceptual build and deploy script (NOT a full YAML pipeline)
    # This would reside in scripts/deploy.sh or similar

    # Manual Steps for MVP CI/CD:
    # 1. Developer pushes code to Git repository (e.g., main branch).
    # 2. Developer pulls latest changes on the Gaming PC: `git pull origin main`
    # 3. Developer navigates to project root: `cd AI-Podcast-Generator`
    # 4. Build and deploy all services: `docker compose up --build -d --force-recreate`
    #    - `--build`: ensures Docker images are rebuilt from latest code.
    #    - `-d`: runs containers in detached mode.
    #    - `--force-recreate`: forces recreation of containers to pick up new images if old ones are found

    # Or for a specific service update:
    # `docker compose up --build -d <service_name>` (e.g., `api` or `web-app`)
    ```

---

**3. Environments**

For the MVP, there are two primary environments: a local development environment (local machine where developers code) and the single "production" like environment on the gaming PC. While not strictly separate "environments" in the cloud sense, it's important to define their access points.

*   **Environments Table:**

| Environment | Frontend URL        | Backend URL           | Purpose                                  |
| :---------- | :------------------ | :-------------------- | :--------------------------------------- |
| Development | `http://localhost:3000` (or Vite's default) | `http://localhost:8000/api/v1` | Local coding, testing, and debugging. |
| Production  | `http://<gaming-pc-ip>:3000` | `http://<gaming-pc-ip>:8000/api/v1` | Stable, internal deployment for actual use. |
