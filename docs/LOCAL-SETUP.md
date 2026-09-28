# Bridge Inventory — Local Setup

## Prerequisites

- Docker Desktop running
- Git
- Optional: `gh` authenticated with write access to `Bridge-BluePrint-Solutions-Ltd/bridge-inventory`

## First-time setup

```bash
cd /Users/padmore/Documents/Projects/Bridge-BluePrint/bridge-inventory

# If .env missing:
cp .env.docker .env
# Edit .env: set strong DB_PASSWORD + MYSQL_ROOT_PASSWORD; APP_VERSION=v8.7.2;
# APP_URL=http://localhost:8000; APP_TIMEZONE='Africa/Accra';
# MAIL_MAILER=smtp; MAIL_HOST=mailpit; MAIL_PORT=1025

docker compose up -d
docker compose ps
curl -sSI http://localhost:8000
```

Open http://localhost:8000 and complete the **setup wizard** (create admin).  
Mailpit UI: http://localhost:8025

### APP_KEY

```bash
docker compose run --rm app php artisan key:generate --show
# Put value in .env as APP_KEY=...
docker compose up -d --force-recreate app
```

## Daily commands

| Action | Command |
|--------|---------|
| Start | `docker compose up -d` |
| Status | `docker compose ps` |
| App logs | `docker compose logs -f app` |
| Mailpit logs | `docker compose logs -f mailpit` |
| Stop | `docker compose stop` |
| Recreate app | `docker compose up -d --force-recreate app` |

**Do not** run `docker compose down -v` unless you intend to wipe the database volume.

## Mail

- **Default (local):** `MAIL_MAILER=smtp`, `MAIL_HOST=mailpit`, `MAIL_PORT=1025`
- **Alternate:** `MAIL_MAILER=log` (messages go to Laravel/app logs; no SMTP)
- **Later (production):** Resend SMTP — `smtp.resend.com`, port `587`, username `resend`, password = API key. Keep secrets out of git.

## Mode B — customize code

```bash
# Edit PHP/views in this repo, then:
docker build -t bridge-inventory:local .
# In docker-compose.yml set app.image to bridge-inventory:local
# (or uncomment the build: / image: lines)
docker compose up -d --force-recreate app
```

## Remotes

```text
origin    https://github.com/Bridge-BluePrint-Solutions-Ltd/bridge-inventory.git
upstream  https://github.com/grokability/snipe-it.git
```

## Related docs

- Living plan: [BRIDGE-INVENTORY-PLAN.md](./BRIDGE-INVENTORY-PLAN.md) (also `/Users/padmore/Documents/Bridge-Inventory-Plan.md`)
- Repo root shorthand: `PLAN.md`, `DEPLOYMENT.md`
