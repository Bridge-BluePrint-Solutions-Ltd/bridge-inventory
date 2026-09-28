# Bridge Inventory — Deployment Notes

## Local (now)

See [docs/LOCAL-SETUP.md](docs/LOCAL-SETUP.md).

- App: http://localhost:8000  
- Mailpit: http://localhost:8025  
- Stack: Docker Compose (`app`, `db`, `mailpit`)

## Production (planned — not done)

Target: DigitalOcean droplet + Cloudflare DNS/TLS.

Checklist:

1. Provision DO droplet (Docker) or equivalent
2. Clone `Bridge-BluePrint-Solutions-Ltd/bridge-inventory`
3. Create production `.env` (new secrets; `APP_URL=https://…`; `APP_ENV=production`)
4. Point Cloudflare DNS A/CNAME at droplet; enable proxy/TLS as desired
5. Configure Resend SMTP (or Graph) for real mail — replace Mailpit
6. Restrict ports (80/443 only publicly); DB not public
7. Enable backups (DB + storage volume)
8. AGPL: publish/offer source if modified and network-offered
9. Optional: SSO/SAML

Do not reuse local `.env` passwords in production.
