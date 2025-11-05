# n8n Documentation

## Interesting Reddit Post:

```text
https://www.reddit.com/r/n8n/comments/1mvb78b/how_to_install_and_run_n8n_locally_in_2025/
```

## Running n8n locally in a docker container with docker compose

DOCKER Conifg
n8n
image: n8nio/n8n
n8n ports
5678:5678

.ENV
Basics:
N8N_HOST=localhost
N8N_PORT=5678
N8N_PROTOCOL=http

Security
N8N_ENCRYPTION_KEY=change_me_to_a_random_32_char_string
N8N_BASIC_AUTH_ACTIVE=true
N8N_BASIC_AUTH_USER=admin
N8N_BASIC_AUTH_PASSWORD=your_secure_password

VOLUMES
n8n_data:/home/node/.n8n
volumes
n8n_data

## Volumes in docker

Containers are temporary. Restarting or deleting containers deletes all data.
Volumes store data on host

## INSIDE THE CONTAINER

n8n stores all configuration and workflow data in
/home/node/.n8n inside the container.

## USING VARIABLES in DOCKER

env_file: (e.g., ./n8n/n8n.env) sets environment variables inside the container.

Compose variable interpolation (the ${VAR} you use in ports:) only reads from a file named .env next to your compose file or from your shell env.

## Environment Files in Docker

- .env -> controls Compose / host-level variables (e.g. port mapping, volume paths).
- n8n.env -> controls n8n app-level settings inside the container.

| Category                | Variables                    | Who uses them                |
| ----------------------- | ---------------------------- | ---------------------------- |
| **Internal networking** | HOST, PORT, PROTOCOL         | n8n itself                   |
| **Security**            | ENCRYPTION_KEY, BASIC_AUTH_* | n8n                          |
| **External access**     | WEBHOOK_URL, EDITOR_BASE_URL | n8n (for clients & webhooks) |
| **Deployment-level**    | (none of these)              | Docker Compose / host (.env) |

---

## Lead Intake Workflow (Blueprint)

When the Worker forwards payloads to `/webhook/lead`, n8n should run the following nodes:

1. **Webhook (POST /webhook/lead)** - parses JSON body. Expects `{ name, email, service_type?, budget?, message?, submittedAt? }`.
2. **Function: Normalize Payload** - map fields to Supabase columns (`full_name`, `email`, `service_type`, `budget`, `message`, `source = 'web'`) and attach metadata (request ID, submittedAt, user agent).
3. **Supabase (Insert Row)** - table `leads`. Use Service Role key, enable upsert on `email` + `service_type` to prevent duplicates.
4. **Notification Branch** - Postmark or SMS node to alert the operator; Slack/Teams optional.
5. **Automation Branching** - IF node for lead score threshold, CRM sync, review trigger, etc.
6. **Respond to Webhook** - return `{ ok: true, leadId }` with 200 status so the Worker can surface success to the browser.

Enable **Error Workflow** so failures emit an alert (email/Slack) and do not silently drop leads.

---

## Required Credentials in n8n

- `SUPABASE_URL` + `SUPABASE_SERVICE_ROLE_KEY`
- `POSTMARK_SERVER_TOKEN` (or Twilio/Telnyx credentials for SMS)
- Optional: Google Places API, Clearbit, or other enrichment providers

Store them as n8n credentials and reference via the node credential selector-avoid hardcoding secrets in workflows.

---

## Summary for AI Tools

- Flow: Astro form -> Cloudflare Worker (`POST /lead`) -> n8n webhook -> Supabase -> notifications.
- Use environment variable names only; values live in `.env`, `n8n.env`, or secrets manager.
- Worker adds `X-Request-Id` and `X-Project-Name` headers-capture them in n8n for traceability.
