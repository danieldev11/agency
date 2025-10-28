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

- .env → controls Compose / host-level variables (e.g. port mapping, volume paths).
- n8n.env → controls n8n app-level settings inside the container.

| Category                | Variables                    | Who uses them                |
| ----------------------- | ---------------------------- | ---------------------------- |
| **Internal networking** | HOST, PORT, PROTOCOL         | n8n itself                   |
| **Security**            | ENCRYPTION_KEY, BASIC_AUTH_* | n8n                          |
| **External access**     | WEBHOOK_URL, EDITOR_BASE_URL | n8n (for clients & webhooks) |
| **Deployment-level**    | (none of these)              | Docker Compose / host (.env) |
