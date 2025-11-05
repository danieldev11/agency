# ProjectX - Developer Guide

**Purpose:**
Provide a consistent setup, workflow, and coding standard so developers and AI tools can build, test, and extend ProjectX without ambiguity.

**Scope:**
Environment setup, repo layout, commit conventions, and testing practices.

**Audience:**
Dev | AI tools

**See also:**
[../02_Architecture/DEVOPS.md](../02_Architecture/DEVOPS.md)
[../02_Architecture/ARCHITECTURE.md](../02_Architecture/ARCHITECTURE.md)

---

## Setup

### Prerequisites

* **OS:** macOS or Linux (Ubuntu recommended). Windows users -> WSL2.
* **Required Tools:**

  * Docker + Docker Compose
  * Node.js >= 18 (for Astro frontend)
  * Git + GitHub CLI
  * Optional: VS Code + Codex or Claude for assisted development

### Installation

1. Clone the repository:

   ```bash
   git clone https://github.com/<org>/projectx.git
   cd projectx
   ```

2. Install frontend dependencies and copy the sample environment:

   ```bash
   cd frontend
   npm install
   cp .env.example .env
   cd ..
   ```

   Update `frontend/.env` so `PUBLIC_LEAD_ENDPOINT` points at your Worker (`http://127.0.0.1:8787/lead` in local dev).

3. Install Worker dependencies:

   ```bash
   cd workers/lead-proxy
   npm install
   cd ../..
   ```

4. Start Docker services:

   ```bash
   docker compose up -d
   ```

---

## Repository Layout

| Folder / File        | Purpose                                       |
| -------------------- | --------------------------------------------- |
| `01_Product/`        | Vision, roadmap, and business context         |
| `02_Architecture/`   | Architecture, DevOps, and system diagrams     |
| `03_Development/`    | Developer workflow, conventions, and tests    |
| `04_Operations/`     | Deployment, monitoring, and maintenance       |
| `frontend/`          | Astro + Tailwind landing page and assets      |
| `workers/`           | Cloudflare Worker scripts (proxy, validation) |
| `n8n/`               | Workflow exports (JSON)                       |
| `supabase/`          | Database schema, triggers, and migrations     |
| `.github/workflows/` | CI/CD pipeline configs                        |

---

## Coding Conventions

* **Languages:**

  * Frontend -> JavaScript/TypeScript
  * CI/CD -> YAML
  * Automations -> JSON (n8n)
  * Docs -> Markdown

* **Naming:**

  * Folders -> lowercase
  * Variables -> camelCase
  * Env vars -> UPPER_SNAKE_CASE

* **Commit Style (Conventional Commits):**

  * `feat:` -> new feature
  * `fix:` -> bug fix
  * `docs:` -> documentation change
  * `refactor:` -> non-breaking restructuring

* **Branching Model:**

  * `main` -> production
  * `dev` -> active development
  * `feature/<name>` -> new features
  * `hotfix/<name>` -> urgent production fixes

---

## Local Testing

### Frontend

Run Astro's development server:

```bash
npm run dev
```

Submit test data through the local form and confirm Worker -> n8n -> Supabase flow. Start the Worker in a second terminal:

```bash
cd workers/lead-proxy
npm run dev
```

Wrangler defaults to `http://127.0.0.1:8787/lead`. Update `PUBLIC_LEAD_ENDPOINT` in `frontend/.env` if you are tunneling or using a staged Worker.

---

### n8n Workflows

* Access local dashboard at `http://localhost:5678`.
* Build or clone workflows under test prefixes (e.g., `lead_test_1`).
* Avoid editing production workflows directly.

To export for Git tracking:

```bash
n8n export:workflow --id <workflow_id> --output ./n8n/<name>.json
```

---

### Logs & Debugging

* View service logs:

  ```bash
  docker compose logs -f
  ```

* Debug Cloudflare Workers:

  ```bash
  npx wrangler dev
  ```
  
* Restart containers cleanly:

  ```bash
  docker compose down && docker compose up -d
  ```

---

## Contribution Workflow

1. **Create a feature branch:**

   ```bash
   git checkout -b feature/<short-description>
   ```

2. Commit frequently with clear messages.
3. Push branch and open a PR to `dev`.
4. Wait for review and automated tests.
5. CI/CD merges into `main` on approval.

---

## Developer Productivity Notes

* Sync `.env` across local, staging, and prod using `.env.template`.
* Use `n8n_data` Docker volume to persist workflows between rebuilds.
* Enable Prettier and ESLint for consistent formatting.
* Test Worker endpoints before merging changes to avoid proxy failure loops.
* Use [Appsmith](https://www.appsmith.com/) locally to test Supabase queries and UI dashboards.

---

## Future Additions

* `testing.md` - detailed test matrix (unit + integration).
* `api-spec.md` - schema for Worker -> n8n -> Supabase communication.
* `developer-onboarding.md` - checklist for new team members.

---

## Summary for AI Tools

* Core stack: **Node (Astro)** + **Docker (n8n, Supabase)**.
* Canonical paths: `/frontend`, `/workers`, `/n8n`, `/supabase`.
* Run Docker Compose before any local testing.
* Use Conventional Commits and branch discipline.
* See `DEVOPS.md` for pipelines and env setup.

---

## Key Entities & Pointers

* **Entities:** ProjectX, Docker Compose, n8n, Cloudflare Workers, Supabase, Astro
* **Paths:** `./frontend/`, `./workers/`, `./n8n/`, `./supabase/`, `./02_Architecture/DEVOPS.md`
* **Contracts:**

  * Environment = Docker containers + Node runtime
  * CI/CD mirrors local Compose setup
  * Worker proxies handle validation and n8n signing
