# ProjectX — README

**Purpose:** Explain what ProjectX is, why it exists, and where to look next so humans and AI tools can operate with minimal ambiguity.  
**Scope:** Product problem/solution, target users, core stack, and repo map.  
**Audience:** Founders | Dev | Ops | AI tools  
**See also:** [../02_Architecture/ARCHITECTURE.md](../02_Architecture/ARCHITECTURE.md), [../03_Development/DEVELOPER.md](../03_Development/DEVELOPER.md)

## What is ProjectX?

- One paragraph on the *problem* we solve.
- One paragraph on the *solution* (landing page → Worker proxy → n8n → Supabase → notifications).
- One sentence on *who benefits* (primary customer profile).

## Why now / Value

- 2–4 bullets tying to ROI (lead capture reliability, cost control, data ownership, compliance-ready).

## Core Capabilities

- Lead intake via Astro front-end → Cloudflare Worker proxy
- Automation in n8n (workflows: lead enrichment, notifications, CRM sync)
- Data layer in Supabase (Postgres + pgvector optional)
- Messaging via Postmark/SMS provider
- Observability (basic logs now; metrics later)

## Tech Stack (High level)

- Frontend: Astro + Tailwind (static), form → Worker POST
- Edge/API: Cloudflare Workers (proxy + validation)
- Automations: n8n (Docker, VPS)
- Data: Supabase (Postgres, Auth, Storage, optional pgvector)
- CI/CD & Infra: GitHub Actions, Cloudflare Pages/Workers, VPS for n8n

## Repository Map (Brief)

- 01_Product/ — README, ROADMAP  
- 02_Architecture/ — ARCHITECTURE, DEVOPS  
- 03_Development/ — DEVELOPER  
- 04_Operations/ — DEPLOYMENT  
- workers/ — Cloudflare Worker code  
- n8n/ — workflow assets (no secrets)  
- docker-compose.yml — local dev topology

## Non-Goals (for now)

- Full CRM suite, multi-tenant admin, WAF policies, advanced analytics dashboards.

## Future Additions (optional)

- user-personas.md — 1 paragraph per persona for messaging alignment.  
- value-proposition.md — crisp differentiators vs AI-agency clones.  
- decision-log.md — brief record of major pivots and why.

## Summary for AI Tools

- ProjectX = Astro form → Cloudflare Worker (proxy) → n8n webhook → Supabase → notifications.
- Use env var *names only*; values are injected at runtime (see DEVOPS.md).
- Canonical neighbors: ARCHITECTURE.md (flow, integrations), DEVELOPER.md (setup), DEVOPS.md (compose, CI/CD).
- Output code that assumes Worker handles input validation and signs requests to n8n.

## Key Entities & Pointers

- Entities: ProjectX, n8n, Supabase (Postgres/pgvector), Cloudflare Pages/Workers, Postmark  
- Paths: ./workers/, ./n8n/, ./docker-compose.yml, ./01_Product/, ./02_Architecture/  
- Contracts: POST /lead (Worker) → forwards to n8n webhook; required envs listed in DEVOPS.md
