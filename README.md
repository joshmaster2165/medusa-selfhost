# Medusa — Self-Hosting

Run [Medusa](https://medusasec.com) — endpoint DLP for AI tools — entirely on
**your own infrastructure**. Your endpoints' telemetry, policies, findings, and
approvals stay inside your network (on-prem, your VPC, or air-gapped). Nothing
talks to Medusa's cloud or to Supabase-the-company.

> **Self-hosting is free.** The dashboard is closed-source but ships as a
> **free, public** container image — you never need our source to run it. You pay
> only for the hosted SaaS (we run it for you). The free tier needs no license
> key; a key only unlocks higher tiers.

## What you run

- **The dashboard** — `ghcr.io/joshmaster2165/medusa-dashboard` (free, public image).
- **Your own Supabase** — Postgres + Auth + Edge Functions, self-hosted (Docker)
  or a Supabase Cloud project in your own account.
- **The browser extension** — open source
  ([medusa-extension](https://github.com/joshmaster2165/medusa-extension)); build
  your own per-org build, or install an official (model-included) build.

All DLP detection runs **in the browser** via an on-device model; the control
plane only ever receives metadata, never your text.

## Quick start

**1. Get the backend kit** (`medusa-selfhost-kit.tar.gz`) + (optional) a license
key from [medusasec.com](https://medusasec.com) — the free tier needs none.

**2. Provision your Supabase** (from the kit):

```bash
tar -xzf medusa-selfhost-kit.tar.gz && cd medusa-selfhost-kit
SUPABASE_PROJECT_REF=<your-ref> \
DASHBOARD_URL=https://dlp.your-company.com \
node scripts/selfhost-setup.mjs
```

**3. Run the dashboard** (this repo):

```bash
cp .env.example .env       # fill in Supabase + (optional) license + POLICY_SIGNING_SECRET
docker compose pull
docker compose up -d
# open http://localhost:3000  (put behind TLS at your NEXT_PUBLIC_APP_URL)
```

**4. Roll out the extension** — build a per-org build from the open-source repo
(`node scripts/build-selfhost.mjs` with your Supabase + dashboard URLs), or use an
official build. Distribute via MDM (Google Admin / Intune / Jamf / Edge) and
enroll with an org key from the dashboard.

📘 **Full step-by-step guide (incl. GCP + on-prem runbooks):**
https://medusasec.com/docs/deployment

## How distribution works

A container image is the *compiled* app — not our git repo. Three facts:

1. **You pull a free, public image** — anyone can `docker pull` it. It contains
   the built app, never our source.
2. **One image, any Supabase.** Built once with placeholder config; the entrypoint
   injects **your** Supabase values (from `.env`) at container start, so the same
   image works for any deployment — no rebuild.
3. **Free to self-host; a license sets your tier.** `MEDUSA_LICENSE_KEY` is
   verified **offline** (Ed25519), so paid tiers work fully air-gapped.

Air-gapped? After pulling, `docker save` the image on a connected host and
`docker load` it inside your network.

## Licensing

| Tier | Endpoints | Retention | Notable |
|---|---|---|---|
| Free | 3 | 30 days | single user |
| Starter | 5 | 90 days | OAuth SSO, alerting |
| Pro | 25 | 1 year | RBAC, audit export, SIEM |
| Enterprise | unlimited | unlimited | SAML SSO, multi-tenant (MSSP) |

The free tier needs no key. To request or renew one, contact `sales@medusasec.com`.

## Support

Self-hosted deployments on a paid tier include email support at
`support@medusasec.com`. Include your license id (Settings → License & Tier).
