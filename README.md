# Server Infra

Shared VPS infrastructure for public ingress, analytics, and lightweight monitoring.

## Owns

- Caddy reverse proxy
- Umami analytics
- Umami Postgres
- Beszel monitoring

## Does Not Own

- Application databases
- Backups
- App-specific services

## One-Time Setup

Create the shared Docker network if it does not already exist:

```bash
docker network create caddy-shared
```

Copy the environment template and fill secrets:

```bash
cp .env.example .env
```

## Deploy

Start app repositories first so Caddy can resolve their containers on `caddy-shared`.

Then start infra:

```bash
docker compose up -d
```

Pushes to `master` deploy through GitHub Actions over SSH. Configure these
repository secrets:

- `DEPLOY_HOST`
- `DEPLOY_USER`
- `DEPLOY_KEY`

The deploy job expects this repo to exist on the server at
`~/apps/server-infra`.

## Beszel First Run

1. Open `https://monitoring.$DOMAIN`.
2. Create the first admin user.
3. Add the local system in the Beszel UI.
4. Copy the generated token and public key into `.env` as `BESZEL_AGENT_TOKEN` and `BESZEL_AGENT_KEY`.
5. Restart the agent:

```bash
docker compose up -d beszel-agent
```

For the local system host, use the Unix socket path:

```text
/beszel_socket/beszel.sock
```

## Routes

- `https://$DOMAIN` -> IHYS frontend/backend
- `https://analytics.$DOMAIN` -> Umami
- `https://mcp.$DOMAIN` -> Dixa MCP
- `https://monitoring.$DOMAIN` -> Beszel
