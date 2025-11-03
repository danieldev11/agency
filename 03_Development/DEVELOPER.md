# Developer Guide

**Purpose:** Provide clear setup, workflow, and coding standards for developers and AI tools to work within ProjectX consistently.  
**Scope:** Environment setup, repo layout, commit conventions, and testing practices.  
**Audience:** Dev | AI tools  
**See also:** [../02_Architecture/DEVOPS.md](../02_Architecture/DEVOPS.md), [../02_Architecture/ARCHITECTURE.md](../02_Architecture/ARCHITECTURE.md)

---

## Setup

### Prerequisites

- **OS:** macOS, Linux (Ubuntu preferred), Windows (WSL2).  
- **Requirements:**
  - Docker + Docker Compose
  - Node.js ≥ 18 (for Astro)
  - Git + GitHub CLI
  - Optional: VS Code + Codex/Claude extensions

### Installation

1. Clone the repo:

   ```bash
   git clone https://github.com/<org>/projectx.git
   cd projectx
   ```

2. Install frontend dependencies:

   ```bash
   cd frontend
   npm install
   ```

3. Copy and configure env variables:

   ```bash

   cp .env.example .env
   ```

4. Start Docker services:

   ```bash
   docker compose up -d
   ```

---

## Repository Layout

| Folder / File | Purpose |
|----------------|----------|
| `01_Product/` | Vision, docs, and roadmap context |
| `02_Architecture/` | System structure and DevOps config |
| `03_Development/` | Developer workflow and conventions |
| `04_Operations/` | Deployment and maintenance docs |
| `workers/` | Cloudflare Worker scripts |
| `n8n/` | Workflow JSON or nodes (sanitized) |
| `supabase/` | SQL schema, migrations, or seed files |
| `frontend/` | Astro/Tailwind frontend source |
| `.github/workflows/` | CI/CD automation pipelines |

---

## Coding Conventions

- **Language:** JavaScript/TypeScript (frontend), YAML (CI/CD), JSON (n8n).  
- **Naming:** lowercase for folders, camelCase for JS variables, snake_case for env vars.  
- **Commits:** Follow [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/):
  - `feat:` → new feature
  - `fix:` → bug fix
  - `docs:` → documentation change
  - `refactor:` → structural change without feature impact  
- **Branching:**
  - `main` → stable
  - `dev` → active development
  - feature branches → `feature/<name>`

---

## Local Testing

### Frontend

- Run local Astro dev server:

  ```bash
  npm run dev
  ```

- Test Worker proxy by submitting form data to local endpoint.

### n8n Workflows

- Access dashboard at `http://localhost:5678`.  
- Create new workflows for testing; never overwrite production ones.

### Logs & Debugging

- View container logs:

  ```bash
  docker compose logs -f
  ```

- For Cloudflare Worker debugging:

  ```bash
  npx wrangler dev
  ```

---

## Contribution Workflow

1. **Create a branch:**  

   ```bash
   git checkout -b feature/<short-description>
   ```

2. **Commit frequently** with clear messages.  
3. **Open PR** to `dev` branch.  
4. Once approved, merge to `main` via CI/CD.

---

## Future Additions

- `testing.md` — detailed test coverage plan.  
- `api-spec.md` — endpoint schemas for Worker → n8n → Supabase.

---

## Summary for AI Tools

- Development stack = Node (Astro) + Docker (n8n, Supabase).  
- Always run services via Compose before local testing.  
- Canonical folders: `/frontend`, `/workers`, `/n8n`, `/supabase`.  
- Refer to DEVOPS.md for env vars and pipeline info.  
- Follow Conventional Commits for consistent versioning.

---

## Key Entities & Pointers

- Entities: ProjectX, Docker Compose, n8n, Cloudflare Workers, Supabase, Astro  
- Paths: ./frontend/, ./workers/, ./n8n/, ./supabase/, ./02_Architecture/DEVOPS.md  
- Contracts: Environment = Docker containers + local Node runtime; CI/CD mirrors local setup.
