# Building an Agency

```text
localhost
astro site: http://localhost:4321/
n8n: http://localhost:5678


## Frontend

Tech Stack:
HTML, CSS, JS, Astro

## Creating a new project with Astro

npm create astro@latest [project-name]

## Astro quickstart cheat sheet

- Initialize and run

```text
  cd [project-name]
  npm install
  npm run dev
  npm run build
  npm run preview
  ```

- Common structure
  - src/pages: file-based routes
  - src/components: reusable UI
  - src/layouts: page wrappers
  - src/content: content collections (Markdown/MDX)
  - public: static assets (served at /)
  - astro.config.mjs: project config

- Routing basics
  - src/pages/index.astro → /
  - src/pages/about.astro → /about
  - src/pages/blog/[slug].astro → /blog/:slug
  - API routes: src/pages/api/hello.ts

- To start building a site, edit the following files:

```text
  Page content: index.astro
  Site wrapper (head, title, global styles): Layout.astro
  Optional components: Welcome.astro
  Assets: Images
  ```

## Testing webhook with curl

prod:
curl -i -X POST \
  -H 'Content-Type: application/json' \
  -d '{"name":"Prod","email":"prod@example.com","message":"live"}' \
  http://localhost:5678/webhook/test-node

test:
curl -i -X POST \
  -H 'Content-Type: application/json' \
  -d '{"name":"Test","email":"test@example.com","message":"hi"}' \
  http://localhost:5678/webhook-test/testing

## Log 10/28

ASTRO
Created a new Astro project (npm create astro@latest).
Set up the repo and project directory.
Built a simple static page (pages/index.astro) with a “Name / Email / Message” form.

BACKEND
The site itself is static (no backend).
Form submissions use fetch() to send data to a webhook endpoint.
The webhook triggers a workflow in n8n, which handles automation (database insert, email, etc.).

```N8N
Installed n8n in Docker using a docker-compose.yml file.
Verified it runs at http://localhost:5678.
Created a new workflow in n8n with:
Webhook node (Method: POST, Path: testing)
Respond to Webhook node connected after it.

WEBHOOKS
Webhook node
Responds “Using Respond to Webhook”.
Path = testing
No authentication.
Respond to Webhook node
Respond With: JSON
Response Body: { "ok": true }
Response Code: 200
(No empty headers)

TESTING
curl -i -X POST \
  -H 'Content-Type: application/json' \
  -d '{"name":"Test","email":"test@example.com","message":"hi"}' \
  http://localhost:5678/webhook/testing

Next steps:
Add a Supabase Insert Node in n8n to store leads.
Add Email/SMS notification nodes for client alerts.
Deploy the Astro site to Cloudflare Pages (or Vercel).
Host n8n on a VPS (when ready for production).
Add a small API relay for production security (Cloudflare Function → n8n).

