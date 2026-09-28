# Bridge Inventory — Living Plan

> Living document for humans and AIs. Update checklists as work completes.
> Last updated: 2026-09-28 (Africa/Accra / UTC+0)

## Goal

Run **Bridge Inventory** (a branded/forked deployment of [Snipe-IT](https://github.com/grokability/snipe-it), AGPL-3.0) for Bridge BluePrint Solutions Ltd — locally first via Docker, then on DigitalOcean + Cloudflare.

## Decisions made

| Decision | Choice |
|----------|--------|
| Path on Mac | `/Users/padmore/Documents/Projects/Bridge-BluePrint/bridge-inventory` |
| Mode | **Mode B** — full git clone of upstream, push to Bridge GitHub, Docker Compose locally, modify code later |
| GitHub (origin) | `https://github.com/Bridge-BluePrint-Solutions-Ltd/bridge-inventory.git` |
| Upstream remote | `https://github.com/grokability/snipe-it.git` (keep for pulls) |
| App version pin | `APP_VERSION=v8.7.2` (Docker Hub + GitHub release 2026-08-19) |
| Local mail | SMTP → **Mailpit** (`mailpit:1025`, UI `:8025`) **and** document `MAIL_MAILER=log` alternate |
| Production mail (later) | Resend SMTP — document only for now |
| Timezone | `Africa/Accra` |
| Secrets | Local `.env` only (gitignored). Never commit real passwords |

## Architecture (local Docker)

```
browser → :8000 → app (snipe/snipe-it:v8.7.2)
                → db (mariadb:11.4.7)  volume: db_data
                → mailpit (axllent/mailpit)  UI :8025 / SMTP :1025
```

- Compose file: `docker-compose.yml`
- Env template: `.env.docker` → copy to `.env` (not committed)
- Mode B customize: `docker build -t bridge-inventory:local .` then point `app.image` at that tag (see commented lines in compose)

## Credentials locations

- **DB / root / APP_KEY**: see local `.env` only — do **not** paste secrets into markdown or chat
- GitHub: org `Bridge-BluePrint-Solutions-Ltd`, repo `bridge-inventory`
- Resend API / SMTP: not configured yet (future)

## Completed checklist

- [x] Preconditions: Docker Desktop running, git, `gh` auth as GeorgePadmore
- [x] Parent dir exists; created `bridge-inventory` via fresh clone (did not overwrite)
- [x] Clone upstream Snipe-IT → remotes: `origin` = Bridge, `upstream` = grokability/snipe-it
- [x] Pin `APP_VERSION=v8.7.2`
- [x] Add Mailpit service; wire `MAIL_HOST=mailpit`
- [x] Create `.env` from `.env.docker` with strong DB passwords, Africa/Accra, APP_URL
- [x] Document Mode B local image build path in compose + docs
- [x] `docker compose up -d` and verify HTTP on :8000
- [x] Living plan in repo + `/Users/padmore/Documents/Bridge-Inventory-Plan.md`
- [x] `docs/LOCAL-SETUP.md`, README Bridge note, commit (push if auth allows)

## Remaining checklist

- [ ] Complete web setup wizard (admin account) at http://localhost:8000 — **Padmore / human**
- [ ] Regenerate `APP_KEY` via `docker compose run --rm app php artisan key:generate --show` and paste into `.env` if still using sample key; restart app
- [ ] Push to GitHub origin if not already done (`git push -u origin master`)
- [ ] Test outbound mail via Mailpit UI (create user / password reset in app)
- [ ] Optional: switch `MAIL_MAILER=log` and confirm log driver
- [ ] Plan Resend SMTP for production (smtp.resend.com:587, API key in secrets)
- [ ] Cloudflare DNS for inventory hostname
- [ ] DigitalOcean droplet deploy (Docker or managed)
- [ ] Harden secrets (rotate DB passwords for prod; never reuse local `.env`)
- [ ] Backups (DB dump + `/var/lib/snipeit` volume)
- [ ] AGPL-3.0 compliance: if code is modified and distributed/SaaS-offered, publish source / offer
- [ ] SSO / SAML / LDAP if Bridge needs it later
- [ ] Branding (logo, APP name) when customizing

## Commands cheat sheet

```bash
cd /Users/padmore/Documents/Projects/Bridge-BluePrint/bridge-inventory

# Start / stop / logs
docker compose up -d
docker compose ps
docker compose logs -f app
docker compose stop
# Avoid: docker compose down -v  (destroys DB volume)

# Health
curl -sSI http://localhost:8000
open http://localhost:8000          # setup wizard / app
open http://localhost:8025          # Mailpit UI

# APP_KEY
docker compose run --rm app php artisan key:generate --show
# paste into .env as APP_KEY=base64:... then: docker compose up -d --force-recreate app

# Pull upstream updates (review before merge)
git fetch upstream
git log --oneline HEAD..upstream/master | head

# Mode B local build
docker build -t bridge-inventory:local .
# then edit docker-compose.yml app.image → bridge-inventory:local
docker compose up -d --force-recreate app

# Reset ONLY if setup failed and you accept data loss
# docker compose down -v   # DESTRUCTIVE
```

## How another AI should resume

1. Read this file and `docs/LOCAL-SETUP.md`.
2. Confirm machine: Padmore MacBook `a2ba6e84-6e49-4d59-8b17-214b412b52e7`; project path above.
3. Check `docker compose ps` and `git remote -v` / `git status`.
4. Do **not** commit `.env` or run `down -v` without explicit human approval.
5. Pick the next unchecked item under **Remaining**.
6. For production (DO/Cloudflare/Resend), ask Padmore for domain + secrets rather than inventing them.
7. Keep upstream attribution and AGPL notice when changing README/branding.

## URLs (local)

| Service | URL |
|---------|-----|
| App / setup wizard | http://localhost:8000 |
| Mailpit UI | http://localhost:8025 |
| Mailpit SMTP (from containers) | `mailpit:1025` |

## License note

Upstream Snipe-IT is **AGPL-3.0**. Bridge fork must retain license notices. If the running service is modified and offered to users over a network, AGPL requires corresponding source availability.

