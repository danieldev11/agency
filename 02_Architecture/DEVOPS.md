# ProjectX — DevOps

**Purpose:**
Document how ProjectX is developed, containerized, and deployed so humans and AI tools can build, test, and deliver consistently across environments.

**Scope:**
Local Docker development, Compose topology, CI/CD pipelines, environment variables, and deployment flow.

**Audience:**
Dev | Ops | AI tools

**See also:** [ARCHITECTURE.md](./ARCHITECTURE.md), [../03_Development/DEVELOPER.md](../03_Development/DEVELOPER.md)

---

## Local Development Quickstart

1. **Clone the repo**

   ```bash
   git clone https://github.com/<org>/projectx.git
   cd projectx
   ```

2. **Copy the environment template**

   ```bash
   cp .env.example .env
   ```

   Populate all required variables (see section below).

3. **Start containers**

   ```bash
   docker compose up -d
   ```

4. Access services locally:

   * n8n → `http://localhost:5678`
   * Appsmith → `http://localhost:8080`
   * Mailhog → `http://localhost:8025`

5. To stop containers:

   ```bash
   docker compose down
   ```

---

## Docker Compose Topology

The `docker-compose.yml` defines a minimal local mirror of the production environment.

| Service      | Image                  | Ports | Purpose                 |
| ------------ | ---------------------- | ----- | ----------------------- |
| **n8n**      | `n8nio/n8n:latest`     | 5678  | Core workflow engine    |
| **postgres** | `postgres:15`          | 5432  | Persistent data layer   |
| **appsmith** | `appsmith/appsmith-ce` | 8080  | Contractor dashboard    |
| **mailhog**  | `mailhog/mailhog`      | 8025  | Email testing & sandbox |

**Volumes:**

* `n8n_data` → `/home/node/.n8n`
* `postgres_data` → `/var/lib/postgresql/data`

**Networks:**

* Default Docker bridge (`projectx_default`) for internal comms.

---

## CI/CD Overview

CI/CD is powered by **GitHub Actions** and automates the following lifecycle:

| Stage           | Description                                        |
| --------------- | -------------------------------------------------- |
| **Lint & Test** | Validate syntax, run tests (to be added).          |
| **Build**       | Build and tag Docker images for `n8n` and Worker.  |
| **Push**        | Push built images to Docker Hub or GHCR.           |
| **Deploy**      | Trigger Cloudflare Pages (frontend) and VPS (n8n). |

**Triggers:**

* Push or merge to `main` → auto build and deploy.
* Pull requests → create preview builds (optional).

**Artifacts:**

* Docker images versioned by Git SHA.
* Cloudflare and VPS deployments tied to branch context.

---

## Deployment Flow

| Layer                | Platform             | Description                  |
| -------------------- | -------------------- | ---------------------------- |
| **Frontend**         | Cloudflare Pages     | Builds Astro landing pages   |
| **Edge Proxy**       | Cloudflare Workers   | Validates & routes requests  |
| **Automation Layer** | VPS (Dockerized n8n) | Core logic + workflows       |
| **Database Layer**   | Supabase (managed)   | Postgres + optional pgvector |
| **Messaging Layer**  | Postmark / Twilio    | Email + SMS notifications    |

**Rollback Strategy:**
Redeploy previous Docker image tag or restore Supabase snapshot (automated backups).

---

## Secrets & Environment Variables

**Locations**

* Local: `.env` (excluded from Git)
* Production: Stored in Cloudflare + VPS secrets manager
* CI/CD: Encrypted secrets in GitHub Actions

**Rules**

* Never commit real values.
* Use `UPPER_SNAKE_CASE`.
* Rotate credentials after team or infrastructure changes.

**Example Variables (names only):**

```bash
N8N_API_KEY=<key>
SUPABASE_SERVICE_ROLE_KEY=<key>
CLOUDFLARE_ACCOUNT_TOKEN=<token>
POSTMARK_SERVER_TOKEN=<token>
TWILIO_AUTH_TOKEN=<token>
```

**Injection**

* Local → `.env` file
* CI/CD → GitHub Actions secrets
* Worker → Cloudflare environment variables

---

## Monitoring & Scaling (Planned)

* Add Prometheus + Grafana for container-level metrics.
* Use Watchtower or GitHub Action triggers for auto image updates.
* Enable rate-limiting on Cloudflare Workers.
* Add Redis (optional) for caching and queueing.

---

## Future Additions

* `pipeline.md` — detailed GitHub Actions breakdown.
* `infra-map.md` — visual of staging vs production topology.
* `observability.md` — defines monitoring + alerting stack.

---

## Summary for AI Tools

* **Local:** Docker Compose spins up n8n, Postgres, Appsmith, Mailhog.
* **CI/CD:** GitHub Actions builds + deploys to Cloudflare Pages/Workers and VPS.
* **Secrets:** Stored via env vars (names listed above).
* **Reference:** See `ARCHITECTURE.md` for flow context and `DEVELOPER.md` for setup commands.

---

## Key Entities & Pointers

* **Entities:** ProjectX, n8n, Supabase, Cloudflare, Docker, GitHub Actions, Appsmith
* **Paths:** `./docker-compose.yml`, `.env.example`, `.github/workflows/`, `./02_Architecture/ARCHITECTURE.md`
* **Contracts:** Docker Compose defines service topology; Actions handle build/deploy pipelines.

