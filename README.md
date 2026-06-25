# Medusa — Self-Hosting

Run [Medusa](https://medusasec.com) — endpoint DLP for AI tools — entirely on
**your own infrastructure**. Your endpoints' telemetry, policies, findings, and
approvals stay inside your network (on-prem, your VPC, or air-gapped). Nothing
talks to Medusa's cloud or to Supabase-the-company; Medusa only issues an
offline-verified license key.

> Medusa's dashboard is **closed-source** and ships as a prebuilt, public,
> license-gated container image — you never need our source to run it.

## What you run

- **The dashboard** — `ghcr.io/joshmaster2165/medusa-dashboard` (public image).
- **Your own Supabase** — Postgres + Auth + Edge Functions, self-hosted (Docker)
  or a Supabase Cloud project in your own account.
- **A per-org browser extension** — Medusa builds it for your origins.

All DLP detection runs **in the browser** via a bundled on-device model; the
control plane only ever receives metadata, never your text.

## Quick start

**1. Get your kit from Medusa.** Email `support@medusasec.com` with your intended
dashboard + Supabase URLs to receive:
- your signed `MEDUSA_LICENSE_KEY`,
- the backend kit (`medusa-selfhost-kit.tar.gz` — DB migrations + edge functions
  + setup scripts),
- your per-org extension build (`medusa-extension-selfhost.zip`).

**2. Provision your Supabase** (from the kit):

```bash
tar -xzf medusa-selfhost-kit.tar.gz && cd medusa-selfhost-kit
SUPABASE_PROJECT_REF=<your-ref> \
DASHBOARD_URL=https://dlp.your-company.com \
node scripts/selfhost-setup.mjs
```

**3. Run the dashboard** (this repo):

```bash
cp .env.example .env       # fill in Supabase + license + POLICY_SIGNING_SECRET
docker compose pull
docker compose up -d
# open http://localhost:3000  (put behind TLS at your NEXT_PUBLIC_APP_URL)
```

**4. Roll out the extension** via MDM (Google Admin / Intune / Jamf / Edge) and
enroll with an org key generated in the dashboard.

📘 **Full step-by-step guide:** https://medusasec.com/docs/deployment

## How a public image stays "closed-source"

A container image is the *compiled* app — not our git repo. The image is built
once with placeholder config; its entrypoint injects **your** Supabase values
(from `.env`) into the bundle at container start, so one public image works for
any deployment. The image is free to pull — your **license key** is what unlocks
a paid tier (verified offline with Ed25519, so it works fully air-gapped).

Air-gapped? `docker save` the image on a connected host and `docker load` it
inside your network.

## Licensing

| Tier | Endpoints | Retention | Notable |
|---|---|---|---|
| Free | 3 | 30 days | single user |
| Starter | 5 | 90 days | OAuth SSO, alerting |
| Pro | 25 | 1 year | RBAC, audit export, SIEM |
| Enterprise | unlimited | unlimited | SAML SSO, multi-tenant (MSSP) |

Without a key the dashboard runs at the free tier. To request or renew a key,
contact `sales@medusasec.com`.

## Support

Self-hosted deployments on a paid tier include email support at
`support@medusasec.com`. Include your license id (Settings → License & Tier).
