# Building an Agency

## Frontend

Tech Stack:
HTML, CSS, JS, Astro

## Creating a new project with Astro

npm create astro@latest [project-name]

### Astro quickstart cheat sheet

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

