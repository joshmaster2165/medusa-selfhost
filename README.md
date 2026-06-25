# Medusa — Self-Hosting (Enterprise)

Run [Medusa](https://medusasec.com) — endpoint DLP for AI tools — entirely on
**your own infrastructure** (on-prem, your VPC, or air-gapped). Your endpoints'
telemetry, policies, findings, and approvals stay inside your network. Medusa only
issues an offline-verified license key.

> **Self-host is an Enterprise capability.** The dashboard is closed-source and
> ships as a **private, license-gated** container image; on purchase we grant your
> team image pull access. Want Medusa for free? The **open-source browser
> extension** and a **free hosted dashboard tier** are free — see
> [medusasec.com](https://medusasec.com).

## What you run

- **The dashboard** — `ghcr.io/joshmaster2165/medusa-dashboard` (private image;
  Enterprise pull access).
- **Your own Supabase** — Postgres + Auth + Edge Functions, self-hosted or a
  Supabase Cloud project in your own account.
- **The browser extension** — open source
  ([medusa-extension](https://github.com/joshmaster2165/medusa-extension)).

All DLP detection runs **in the browser** via an on-device model; the control
plane only ever receives metadata, never your text.

## Quick start (Enterprise)

**1. Onboarding.** Contact `sales@medusasec.com` for an Enterprise license + image
pull access. You receive: your `MEDUSA_LICENSE_KEY`, a registry token, the backend
kit (`medusa-selfhost-kit.tar.gz`), and your per-org extension build.

**2. Provision your Supabase** (from the kit):

```bash
tar -xzf medusa-selfhost-kit.tar.gz && cd medusa-selfhost-kit
SUPABASE_PROJECT_REF=<your-ref> DASHBOARD_URL=https://dlp.your-company.com \
  node scripts/selfhost-setup.mjs
```

**3. Run the dashboard** (this repo):

```bash
echo $GHCR_TOKEN | docker login ghcr.io -u <your-github-user> --password-stdin
cp .env.example .env       # Supabase + license + POLICY_SIGNING_SECRET
docker compose pull && docker compose up -d
# open http://localhost:3000  (put behind TLS at your NEXT_PUBLIC_APP_URL)
```

**4. Roll out the extension** via MDM (Google Admin / Intune / Jamf / Edge).

📘 **Full guide, incl. GCP + on-prem runbooks:** https://medusasec.com/docs/deployment

## Pricing

The browser extension is free + open source, and there's a free **hosted** tier.
**Self-host is part of the Enterprise tier** — unlimited endpoints, SAML SSO,
multi-tenant (MSSP), air-gap, and SLA support. Contact `sales@medusasec.com`.
