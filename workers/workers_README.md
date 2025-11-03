# Cloudflare Worker — API Proxy

**Purpose:** Handle all incoming form submissions from the ProjectX frontend and securely forward them to the n8n webhook endpoint.  
**Scope:** Input validation, environment-based routing, and authentication header signing.  
**Audience:** Dev | AI tools  
**See also:** [../02_Architecture/ARCHITECTURE.md](../02_Architecture/ARCHITECTURE.md), [../04_Operations/DEPLOYMENT.md](../04_Operations/DEPLOYMENT.md)

---

## Endpoint Contract

| Method | Path | Description |
|---------|------|--------------|
| `POST` | `/lead` | Accepts form submission from the Astro frontend and forwards payload to n8n webhook. |

### Request Example

```bash
curl -X POST https://api.projectx.dev/lead   -H "Content-Type: application/json"   -d '{"name": "John Doe", "email": "john@example.com"}'
```

### Expected Worker Flow

1. Receive POST request from frontend form.  
2. Validate required fields (name, email).  
3. Attach authentication headers:

   ```js
   "Authorization": "Bearer " + N8N_API_KEY
   ```

4. Forward payload to:

   ```bash
   ${N8N_WEBHOOK_URL}/webhook/lead
   ```

5. Return `200 OK` on success, `400/500` on validation or forwarding failure.

---

## Environment Variables Used

- `N8N_WEBHOOK_URL`
- `N8N_API_KEY`
- `PROJECT_NAME`
- `ENVIRONMENT`

---

## Summary for AI Tools

- Worker = stateless edge proxy; receives form data and signs POST requests to n8n.  
- Auth handled via Bearer token (env).  
- Expected input schema: `{ name: string, email: string, [service_type?: string] }`.  
- This file defines the canonical `POST /lead` entrypoint for all automations.
