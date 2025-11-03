# Architecture

**Purpose:** Describe the structure of ProjectX systems so humans and AI tools can generate and reason about code accurately.  
**Scope:** Service layout, data flow, integrations, and trust boundaries.  
**Audience:** Dev | Ops | AI tools  
**See also:** [../02_Architecture/DEVOPS.md](../02_Architecture/DEVOPS.md), [../03_Development/DEVELOPER.md](../03_Development/DEVELOPER.md)

---

## System Overview

ProjectX operates as a modular web automation platform built for lead intake, processing, and follow-up.  
The flow begins at the **frontend landing page** (Astro + Tailwind), where form data is captured and sent via HTTPS POST to a **Cloudflare Worker** that acts as a secure proxy.  
The Worker validates input, attaches authentication headers, and forwards data to an **n8n webhook** endpoint hosted on a VPS.  
n8n workflows handle business logic—enrichment, notifications, CRM syncing—and persist data to **Supabase** (Postgres).  
From there, Supabase triggers or scheduled jobs can drive analytics, messaging, or additional automations.

---

## (Optional) Mermaid Diagram

```mermaid
flowchart LR
  A[User Form (Astro)] --> B[Cloudflare Worker]
  B --> C[n8n Webhook]
  C --> D[Supabase DB]
  C --> E[Postmark / SMS Provider]
  D --> F[Dashboard / Appsmith / API]
```

---

## Data Flow (E2E)

1. User submits form on landing page.  
2. Cloudflare Worker receives POST → validates → signs → forwards to n8n.  
3. n8n workflow executes:
   - Step 1: Parse input and sanitize.  
   - Step 2: Optional enrichment (e.g., lead scoring API).  
   - Step 3: Write to Supabase (table: `leads`).  
   - Step 4: Trigger notifications (email/SMS).  
4. Supabase stores data and enables queries via API.  
5. Optional: Appsmith dashboard displays metrics.  

---

## Integrations (Systems & Auth Modes)

| System      | Purpose            | Auth Mode          | Notes                               |
|--------------|-------------------|--------------------|--------------------------------------|
| **n8n**      | Workflow engine    | API key (env)      | Primary automation layer             |
| **Supabase** | Database + auth    | Service key (env)  | Postgres + optional pgvector         |
| **Cloudflare** | Proxy + hosting | Account token (env)| Worker secures inbound form traffic  |
| **Postmark** | Transactional email| Server token (env)| Sends confirmation + internal alerts |
| **Appsmith** | Internal dashboard | Auth via Supabase  | Optional; for internal visibility    |

---

## Scaling Notes

- Add caching layer (Redis) for large-scale traffic.  
- Introduce rate-limiting in Workers for public endpoints.  
- Use Supabase Row-Level Security (RLS) for user-specific dashboards.  
- Plan to deploy monitoring with Prometheus + Grafana.

---

## Future Additions

- `security-policy.md` — defines secret rotation and access control.  
- `infra-map.md` — DNS, subdomains, and route structure.  

---

## Summary for AI Tools

- Architecture = Astro form → Cloudflare Worker → n8n webhook → Supabase → notifications.  
- Data direction: inbound only through Worker; all DB writes go through n8n.  
- Auth is handled via environment variables (see DEVOPS.md).  
- Integrations and endpoints listed in the table above are canonical sources of truth.  

---

## Key Entities & Pointers

- Entities: ProjectX, n8n, Supabase (Postgres/pgvector), Cloudflare Workers, Postmark, Appsmith  
- Paths: ./workers/, ./n8n/, ./supabase/, ./02_Architecture/DEVOPS.md  
- Contracts: Worker receives `POST /lead`, forwards to n8n webhook; env vars declared in DEVOPS.md  
