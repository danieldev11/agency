# DevOps

**Purpose:** Document local development, containerization, CI/CD, and deployment practices so humans and AI tools can operate consistently.  
**Scope:** Docker, Compose, environment variables, CI/CD pipeline, and deployment flow.  
**Audience:** Dev | Ops | AI tools  
**See also:** [ARCHITECTURE.md](./ARCHITECTURE.md), [../03_Development/DEVELOPER.md](../03_Development/DEVELOPER.md)

---

## Local Development Quickstart

1. Clone the repo:  

   ```bash
   git clone https://github.com/<org>/projectx.git
   cd projectx
   ```

2. Copy the environment file and update required variables:  

   ```bash
   cp .env.example .env
   ```

3. Start services with Docker Compose:

   ```bash
   docker compose up -d
   ```

4. Access the local n8n instance at `http://localhost:5678`.  
5. To stop all containers:  

   ```bash
   docker compose down
   ```

---

## Docker Compose Topology

The `docker-compose.yml` file orchestrates multiple services:

| Service | Image | Ports | Purpose |
|----------|--------|--------|----------|
| **n8n** | `n8nio/n8n:latest` | 5678 | Automation workflows |
| **postgres** | `postgres:15` | 5432 | Database for Supabase/n8n |
| **appsmith** | `appsmith/appsmith-ce` | 8080 | Internal dashboard |
| **mailhog** | `mailhog/mailhog` | 8025 | Email testing (local only) |

**Volumes:**  

- `n8n_data` → `/home/node/.n8n`  
- `postgres_data` → `/var/lib/postgresql/data`  

**Networks:**  

- Default Docker bridge for internal communication.

---

## CI/CD Overview

CI/CD runs via **GitHub Actions**.  
Pipeline stages:

1. **Lint/Test:** Validate syntax, run unit tests (future).  
2. **Build:** Build Docker images and push to registry (Docker Hub or GHCR).  
3. **Deploy:** Trigger Cloudflare Pages build and VPS redeploy for n8n.  

**Sample Trigger:**  

- On merge to `main`, the pipeline builds and redeploys automatically.  
- Preview environments created on pull requests (optional).

---

## Deployment Flow

| Stage | Platform | Description |
|--------|-----------|-------------|
| **Frontend** | Cloudflare Pages | Static build of Astro site |
| **Edge Proxy** | Cloudflare Workers | API routing and validation |
| **Automation** | VPS (Docker) | n8n instance running workflows |
| **Database** | Supabase | Managed Postgres with optional vector support |

Rollback: re-deploy previous Docker image tag or restore from snapshot.

---

## Secrets & Envs (Policy + Locations)

- **Locations:**  
  - Local: `.env` (untracked)  
  - Production: Cloudflare/Server secrets manager  
- **Rules:**  
  - Never commit real values.  
  - All env vars are named in UPPER_SNAKE_CASE.  
  - Rotate on role or infrastructure changes.  
- **Examples (names only):**

  ```bash
  N8N_API_KEY=<key>
  SUPABASE_SERVICE_ROLE_KEY=<key>
  CLOUDFLARE_ACCOUNT_TOKEN=<token>
  POSTMARK_SERVER_TOKEN=<token>
  ```

- **Injection:**  
  - Local via `.env`  
  - CI/CD via encrypted secrets in GitHub Actions  

---

## Future Additions

- Add `pipeline.md` for deeper GitHub Actions documentation.  
- Add `infra-map.md` for environment-specific deployments (staging/prod).  

---

## Summary for AI Tools

- DevOps defines containerized setup, CI/CD automation, and deployment flow.  
- Docker Compose spins up local n8n, Postgres, Mailhog, and Appsmith.  
- CI/CD builds Docker images and deploys to Cloudflare + VPS.  
- Secrets handled via env vars; names listed above.  
- Reference ARCHITECTURE.md for system relationships.  

---

## Key Entities & Pointers

- Entities: ProjectX, n8n, Supabase, Cloudflare Pages/Workers, Docker, GitHub Actions  
- Paths: ./docker-compose.yml, ./.env.example, .github/workflows/, ./02_Architecture/ARCHITECTURE.md  
- Contracts: Compose defines local services; Actions handles build/deploy automation.  
