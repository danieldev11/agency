# Short-Term Workflow — Integrity-First Architecture (v0.1)

**Status:** Draft for internal review
**Pillars:** Infrastructure + Consulting/Marketing
**Target:** Home Remodelers & General Contractors
**Objective:** Ship a coherent, revenue-ready proof of concept that mirrors long‑term architecture, using open-source tools and zero SaaS lock‑in.

---

## 1) Executive Summary

We are delivering a **Website + Workflow + Dashboard** package that turns inbound interest into tracked leads and faster responses.

* **Infrastructure layer:** Astro (frontend) → n8n (orchestration) → Supabase (DB/Auth).
* **Client visibility:** Appsmith dashboard (read-only) backed by Supabase.
* **ROI focus:** Shorter response times, higher conversion at intake, better retention through consistent follow‑up.

> KPI targets for pilot accounts (directional, not guaranteed):
> • **+50%** increase in captured leads vs current site/form baseline
> • **−40%** lead response time (first contact)
> • **+15–25%** increase in booked estimates (pipeline stage conversion)

This short-term system **shares the same topology** as the long-term plan, enabling a clean migration to **one‑client‑per‑stack (containerized)** without re‑architecture.

---

## 2) Architecture Overview

```mermaid
flowchart LR
  A[Astro Landing Page & Forms] -- POST /webhook --> B[n8n Webhook]
  B --> C{Validation & Enrichment}
  C -->|valid| D[Supabase (Postgres)]
  C -->|invalid| E[Error Log + Alert]
  D --> F[Email/SMS Notify (Postmark/Gmail API)]
  D --> G[Appsmith Dashboard]
  D --> H[Analytics/Queries]
  F --> I[Client Inbox/Slack]
```

**Why this mirrors long‑term:** Astro ↔ n8n ↔ Supabase are the same anchors we’ll keep when we containerize per client. Appsmith is a temporary UI that we can replace with a custom dashboard later.

---

## 3) Tech Stack (Short-Term)

| Layer                   | Tooling                             | Notes                                                            |
| ----------------------- | ----------------------------------- | ---------------------------------------------------------------- |
| Frontend                | **Astro** (open-source template)    | Zero SaaS cost; actual code; simple form → webhook.              |
| Orchestration           | **n8n** (Docker Compose)            | Webhook trigger; validation; enrichment; notifications; retries. |
| Database/Auth           | **Supabase** (Postgres + Auth)      | Leads table; events table; row‑level security (RLS) for safety.  |
| Client Dashboard        | **Appsmith** (cloud or Docker)      | Read-only views; filters; CSV export; minimal training required. |
| Email/SMS               | **Postmark** or Gmail API (via n8n) | Transactional notifications; later: sequences.                   |
| Analytics (lightweight) | Supabase SQL views                  | Build KPI views now; swap to Grafana later if needed.            |
| Hosting                 | Local/VPS via **Docker Compose**    | Same dockerized pattern we’ll promote to per-client stacks.      |

---

## 4) Data Model (Initial)

**Table: `leads`**

* `id` (uuid, pk)
* `source` (text: website, ad, referral, etc.)
* `campaign` (text, nullable)
* `full_name` (text)
* `email` (text)
* `phone` (text)
* `service_type` (text: kitchen, bath, flooring, etc.)
* `message` (text)
* `utm_medium`, `utm_source`, `utm_campaign` (text, nullable)
* `created_at` (timestamptz, default now())
* `status` (enum: new, contacted, qualified, scheduled, closed_won, closed_lost)

**Table: `lead_events`**

* `id` (uuid, pk)
* `lead_id` (fk → leads.id)
* `event_type` (enum: created, email_sent, sms_sent, reply_received, status_changed)
* `notes` (text)
* `created_at` (timestamptz)

  **Security**

* Enable **RLS**; service role for n8n writes; dashboard role for read-only.

---

## 5) Core n8n Workflows (v0.1)

1. **Intake**

* Trigger: Webhook `/lead-intake`
* Steps: validate payload → dedupe by email/phone → enrich (UTM/IP) → insert into `leads` → insert `lead_events(created)` → notify via email/Slack → return 200.

2.**Instant Response**

* Trigger: `lead_events.created` (polling or Supabase hook via HTTP)
* Steps: send confirmation to lead (optional), send instant summary to client inbox; log `email_sent` event.

3.**Follow-up Reminder**

* Trigger: cron (e.g., every hour)
* Steps: query `leads` where `status = 'new'` and `created_at > 2h`; send reminder to client; log event.

> v0.2 roadmap: add drip sequence (day 1, 3, 7), missed‑call text back, and Google Ads lead form ingestion.

---

## 6) Client Dashboard (Appsmith)

**Pages:**

* **Overview:** Lead count, new today/7d/30d, avg first‑response time, pipeline bar.
* **Leads:** Table with filters (date, source, status), CSV export.
* **Lead Detail:** Timeline (events from `lead_events`).

**Access:**

* Appsmith auth; restrict to client emails.
* Data sources scoped via SQL with parameterized queries and RLS-compliant roles.

---

## 7) SLAs & Guardrails (Pilot)

* **Uptime (pilot):** Best effort; business hours support.
* **Response Latency:** Webhook ingest < 2s typical; notifications < 30s typical.
* **Data Ownership:** Client owns their data in Supabase; export available anytime.
* **Security:** Environment secrets via `.env`; least-privileged Supabase roles; HTTPS in production.

---

## 8) Deployment Topology (Pilot)

* Single Compose stack per environment (`dev`, `pilot-client-1`).
* Services: `n8n`, `postgres` (Supabase hosted), `appsmith` (optional container), `caddy`/`traefik` (if exposing).
* DNS: `clientname.hayai.cc` for webhook + dashboard during pilot (migrate to client domain when paid).

---

## 9) Migration Path → One‑Client‑Per‑Stack (Long‑Term)

* Template a **Compose** file with env‑subst for client name/hostnames.
* Clone stack per client: `n8n`, `client‑scoped Supabase project`, `dashboard`.
* CI: GitHub Actions to render `.env` and bring up stack on VPS.
* Optional: Central observability (Prometheus/Grafana) across tenants.

---

## 10) Implementation Plan (T‑14 days)

**Week 1**

* Pick Astro template; add `/contact` form → POST to n8n.
* Provision Supabase; create `leads` + `lead_events`; enable RLS.
* Build n8n **Intake** + **Instant Response** workflows.
* Seed Appsmith dashboard with Overview + Leads pages.

**Week 2**

* Add Follow‑up Reminder workflow.
* Wire Appsmith filters and CSV export.
* Dry-run with test traffic; fix schema/edge cases.
* Prep client‑ready demo with sample data + KPI views.

---

## 11) Risks & Mitigations

* **Frontend delays:** Use template; keep copy minimal; iterate later.
* **Notification deliverability:** Prefer Postmark over raw SMTP; monitor bounces.
* **Data leakage:** Enforce RLS; no Supabase console access for clients.
* **Vendor drift:** Keep core in open stack (Astro, n8n, Postgres).
* **Scope creep:** Lock Tier‑1 to 3 workflows; backlog everything else.

---

## 12) Tier‑1 Package (Client-Facing Definition)

**Deliverables:**

1. Conversion‑focused landing page with tracked form.
2. Orchestrated lead intake + instant notifications.
3. Read‑only lead dashboard with filters and exports.

**Outcomes (tracked):**

* More captured leads (form completion rate).
* Faster first response (timestamp delta).
* Higher pipeline advance rate (new → scheduled).

**What’s not included (pilot):**

* Ad management, SEO, complex CRMs, AI receptionist. (Can be scoped as add‑ons.)

---

## 13) Open Questions (for next iteration)

1. Do we standardize on **Appsmith Cloud** initially, or self‑host it in Compose?
2. Which Astro template do we adopt for quickest client theming?
3. Preferred notification mix for contractors: email only, or email + SMS? (Cost/benefit)
4. Do we capture call‑tracking now, or defer to v0.2?

---

**Owner:** <Your Name>
**Next Review:** Upon pilot client selection or in 7 days (whichever comes first).
