# ProjectX — README

**Purpose:** ProjectX exists to help contractors and small service businesses run their operations more efficiently by unifying how they capture, track, and grow customer leads. It bridges the gap between scattered communication and organized follow-up by combining automation, data management, and marketing workflows into one private, cost-controlled system. This README defines the vision, scope, and architecture of ProjectX so that both humans and AI tools can understand the services provided, how they connect, and how to contribute or extend them without ambiguity.
**Scope:** Contractor-focused automation system that captures, tracks, and grows leads.
**Audience:** Founders | Dev | Ops | AI tools
**See also:** [../02_Architecture/ARCHITECTURE.md](../02_Architecture/ARCHITECTURE.md), [../03_Development/DEVELOPER.md](../03_Development/DEVELOPER.md)

---

## What is ProjectX?

**Problem:**
Contractors lose opportunities every week because their leads come from scattered calls, texts, and word-of-mouth with no central system to track or follow up. High-priced CRMs and marketing software often cost more than they deliver, leaving small teams managing business growth manually.

**Solution:**
ProjectX provides a modular automation system that turns your existing communication channels into a private, cost-controlled growth engine. Leads from your landing page, calls, and texts are captured, routed, and followed up automatically. Everything flows through one secure stack — **Cloudflare Worker → n8n → Supabase → Dashboard** — allowing contractors to see every lead, call, and review in one place.

**Who Benefits:**
Independent contractors, local home-service businesses, and small agencies who want a simple, own-your-data system to capture, track, and grow leads without paying for bloated CRMs.

---

## Why Now / Value

* **Reliability:** Every call, text, and form submission is automatically logged, routed, and followed up.
* **Cost Control:** Open-source and serverless technologies replace high recurring software costs.
* **Data Ownership:** All customer and lead data stays in your Supabase instance — never shared or sold.
* **Compliance Ready:** Transparent, consent-based data handling with scalable privacy controls.

---

## Core Services

### **1. Lead System**

A complete inbound pipeline that captures and organizes every lead across web, call, or SMS.

* Landing page and form capture via **Astro → Cloudflare Worker**
* Follow-up automations (email/SMS reminders)
* Automatic routing and CRM-lite dashboard via **Supabase + Appsmith**
* Designed to increase response rates and reduce lead loss

### **2. Call & SMS Tracking**

Track every call and message to understand what channels actually convert.

* Call/SMS logging via **Twilio or Telnyx APIs**
* Missed call detection + auto follow-up workflows
* Caller attribution, transcription, and response metrics
* Unified communication history view for contractors

### **3. Marketing & Reputation**

Turn satisfied clients into repeat business and organic growth.

* Automatic **review requests** via email/SMS after job completion
* Optional **Google Business integration** for review posting
* Referral and remarketing automation templates
* Optional campaign integrations with Google Ads or Meta

---

## Future Additions (Tier 2 Ideas)

* **Smart Reporting:** Weekly summary emails with key metrics (calls, leads, conversion %).
* **Quote Generator:** Auto-send templated quotes based on job type and location.
* **AI Call Summaries:** Transcribe calls and tag important details (budget, timeline).
* **Lead Scoring:** Rank incoming leads based on data enrichment.
* **AI Chat Assistant:** Web chatbot that qualifies leads before they reach you.

---

## Tech Stack Overview

| Layer             | Tools                                                 | Function                                 |
| ----------------- | ----------------------------------------------------- | ---------------------------------------- |
| **Frontend**      | Astro + Tailwind                                      | Landing page, forms                      |
| **Edge/API**      | Cloudflare Workers                                    | Proxy + validation + request signing     |
| **Automation**    | n8n (Docker on VPS)                                   | Workflows, lead routing, notifications   |
| **Data Layer**    | Supabase (Postgres, Auth, Storage, pgvector optional) | Central database + CRM-lite backend      |
| **Messaging**     | Postmark, Twilio/SMS Provider                         | Follow-ups and notifications             |
| **Infra / CI/CD** | GitHub Actions, Cloudflare Pages/Workers              | Deployment, build automation, monitoring |

---

## Repository Map

```
01_Product/        → README, ROADMAP  
02_Architecture/   → ARCHITECTURE.md, DEVOPS.md  
03_Development/    → DEVELOPER.md  
04_Operations/     → DEPLOYMENT.md  
workers/           → Cloudflare Worker code  
n8n/               → Workflow exports (no secrets)  
docker-compose.yml → Local dev topology
```

---

## Non-Goals (for now)

* Full CRM suite or multi-tenant admin panel
* AI receptionist or scheduling integrations (future tier)
* Advanced analytics dashboards beyond essential insights

---

## Summary for AI Tools

**System flow:** Astro → Cloudflare Worker (proxy) → n8n webhook → Supabase → notifications.
Use environment variable **names only**; values are injected at runtime (see `DEVOPS.md`).
Canonical neighbors:

* `ARCHITECTURE.md` → data flow and integrations
* `DEVELOPER.md` → setup and development
* `DEVOPS.md` → Docker, CI/CD, environment setup

**Rule:** Worker handles input validation, rate-limiting, and request signing to n8n.

---

## Key Entities & Pointers

* **Entities:** ProjectX, n8n, Supabase (Postgres/pgvector), Cloudflare Pages/Workers, Postmark, Twilio
* **Paths:** `./workers/`, `./n8n/`, `./docker-compose.yml`, `./01_Product/`, `./02_Architecture/`
* **Contract:**
  `POST /lead` (Worker) → validates and forwards payload to n8n webhook.
  Required environment variables listed in `DEVOPS.md`.

