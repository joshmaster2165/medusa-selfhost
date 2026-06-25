# Medusa — Self-Hosting

Run [Medusa](https://medusasec.com) — endpoint DLP for AI tools — entirely on
**your own infrastructure**. Your endpoints' telemetry, policies, findings, and
approvals stay inside your network (on-prem, your VPC, or air-gapped). Nothing
talks to Medusa's cloud or to Supabase-the-company; Medusa only issues an
offline-verified license key.

> Medusa's dashboard is **closed-source** and ships as a prebuilt,
> license-gated container image — you never need our source to run it. The image
> is private; Medusa grants your team pull access as part of onboarding.

## What you run

- **The dashboard** — `ghcr.io/joshmaster2165/medusa-dashboard` (private image;
  we grant your team pull access).
- **Your own Supabase** — Postgres + Auth + Edge Functions, self-hosted (Docker)
  or a Supabase Cloud project in your own account.
- **A per-org browser extension** — Medusa builds it for your origins.

All DLP detection runs **in the browser** via a bundled on-device model; the
control plane only ever receives metadata, never your text.

## Quick start

**1. Onboard with Medusa.** Email `support@medusasec.com` with your intended
dashboard + Supabase URLs and your team's GitHub account(s). You receive:
- **pull access** to the dashboard image (we invite your GitHub user, or issue a
  read-only token),
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
# authenticate to the private image (one-time), then pull + run
echo $GHCR_TOKEN | docker login ghcr.io -u <your-github-user> --password-stdin

cp .env.example .env       # fill in Supabase + license + POLICY_SIGNING_SECRET
docker compose pull
docker compose up -d
# open http://localhost:3000  (put behind TLS at your NEXT_PUBLIC_APP_URL)
```

**4. Roll out the extension** via MDM (Google Admin / Intune / Jamf / Edge) and
enroll with an org key generated in the dashboard.

📘 **Full step-by-step guide:** https://medusasec.com/docs/deployment

## How distribution works (closed-source)

A container image is the *compiled* app — not our git repo. Three facts:

1. **You pull an image, not source.** It contains the built app, never the
   source. The image is **private**; Medusa grants your team read access.
2. **One image, any Supabase.** It's built once with placeholder config; the
   entrypoint injects **your** Supabase values (from `.env`) into the bundle at
   container start, so the same image works for any deployment — no rebuild.
3. **The license sets your tier.** `MEDUSA_LICENSE_KEY` is verified **offline**
   (Ed25519), so it works fully air-gapped and never phones home.

Air-gapped? After pulling, `docker save` the image on a connected host and
`docker load` it inside your network.

## Licensing

| Tier | Endpoints | Retention | Notable |
|---|---|---|---|
| Free | 3 | 30 days | single user |
| Starter | 5 | 90 days | OAuth SSO, alerting |
| Pro | 25 | 1 year | RBAC, audit export, SIEM |
| Enterprise | unlimited | unlimited | SAML SSO, multi-tenant (MSSP) |

To request or renew a key, contact `sales@medusasec.com`.

## Support

Self-hosted deployments on a paid tier include email support at
`support@medusasec.com`. Include your license id (Settings → License & Tier).
