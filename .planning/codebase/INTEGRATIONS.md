# External Integrations

**Analysis Date:** 2026-03-24

## APIs & External Services

**Font CDN:**
- Google Fonts (via Next.js font system) - Loads Geist and Geist Mono typefaces
  - SDK/Client: `next/font/google` (bundled with Next.js)
  - Auth: None required
  - Usage: `apps/web/src/app/layout.tsx`

No other external API integrations are present. The codebase is a scaffold with no third-party service SDKs wired in.

## Data Storage

**Databases:**
- None configured. No ORM, database driver, or migration tooling is installed.

**File Storage:**
- Local filesystem only (static assets in `apps/web/public/`)

**Caching:**
- None

## Authentication & Identity

**Auth Provider:**
- None. No authentication library or provider is configured.

## Monitoring & Observability

**Error Tracking:**
- None

**Logs:**
- NestJS default console logger (built-in, no external log service)
- Next.js default console output

## CI/CD & Deployment

**Hosting:**
- Web: Vercel strongly implied — Vercel branding assets present in `apps/web/public/vercel.svg` and deploy links in `apps/web/src/app/page.tsx`
- API: No deployment platform configured

**CI Pipeline:**
- None detected (no GitHub Actions, CircleCI, or similar config files present)

## Environment Configuration

**Required env vars:**
- `PORT` — Optional. API listen port, defaults to `3000` (`apps/api/src/main.ts`)

**No `.env` files present.** `.env` and `.env*.local` are gitignored. No secrets are committed.

**Secrets location:**
- Not configured. To add: create `.env` at root or per-app level (already gitignored).

## Webhooks & Callbacks

**Incoming:**
- None

**Outgoing:**
- None

---

*Integration audit: 2026-03-24*
