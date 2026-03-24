# Technology Stack

**Analysis Date:** 2026-03-24

## Languages

**Primary:**
- TypeScript 5.x - Used across both `apps/api` and `apps/web`

**Secondary:**
- CSS - Styling in `apps/web/src/app/globals.css` via Tailwind v4 directives

## Runtime

**Environment:**
- Node.js v24.13.0 (detected on host)
- Bun 1.3.11 - Primary package manager and test runner

**Package Manager:**
- Bun 1.3.11
- Lockfile: `bun.lock` present (committed but listed in `.gitignore` — discrepancy noted)

## Frameworks

**Frontend (`apps/web`):**
- Next.js 16.2.1 - App Router, React Server Components, SSR/SSG
- React 19.2.4 - UI rendering
- Tailwind CSS v4 - Utility-first CSS via PostCSS plugin

**Backend (`apps/api`):**
- NestJS 11.x (`@nestjs/core`, `@nestjs/common`, `@nestjs/platform-express`) - HTTP API framework
- Express (via `@nestjs/platform-express`) - HTTP server adapter
- RxJS 7.8.1 - Reactive streams (NestJS internal dependency)

**Testing:**
- Bun test (built-in) - Unit and integration test runner for `apps/api`
- `@nestjs/testing` 11.x - NestJS testing utilities
- supertest 7.x - HTTP integration test client

**Build/Dev:**
- NestJS CLI 11.x (`nest build`, `nest start --watch`) - API build and dev server
- Next.js CLI (`next dev`, `next build`) - Web build and dev server
- Biome 2.2.0 - Linting and formatting for entire monorepo

## Key Dependencies

**Critical:**
- `@nestjs/common` ^11.0.1 - Core NestJS decorators and utilities (`apps/api`)
- `@nestjs/core` ^11.0.1 - NestJS application bootstrap (`apps/api`)
- `next` 16.2.1 - Next.js framework with App Router (`apps/web`)
- `react` 19.2.4 - React UI library (`apps/web`)
- `reflect-metadata` ^0.2.2 - Required for NestJS decorator metadata

**Infrastructure:**
- `rxjs` ^7.8.1 - Observable streams, NestJS internals
- `tailwindcss` ^4 - CSS utility framework (dev dependency, `apps/web`)
- `@tailwindcss/postcss` ^4 - PostCSS integration for Tailwind v4

## Configuration

**Environment:**
- No `.env` files present (`.env` and `.env*.local` are gitignored)
- API listens on `process.env.PORT` with fallback to `3000` (`apps/api/src/main.ts`)
- No environment variables currently required for the baseline scaffold

**Build:**
- Root `tsconfig.json` — monorepo base config, references `apps/web` and `apps/api`
- `apps/web/tsconfig.json` — Next.js target ES2017, bundler module resolution, path alias `@/*` → `./src/*`
- `apps/api/tsconfig.json` — NestJS target ES2023, nodenext module resolution, path alias `@/*` → `./src/*`, decorator metadata enabled
- `apps/api/tsconfig.build.json` — Extends API tsconfig, excludes test/spec files from build output
- `apps/api/nest-cli.json` — NestJS CLI config, sourceRoot `src`, deleteOutDir on build
- `apps/web/next.config.ts` — Minimal Next.js config (no custom options set)
- `apps/web/postcss.config.mjs` — PostCSS with `@tailwindcss/postcss` plugin
- `biome.json` — Monorepo-wide linting/formatting: 2-space indents, recommended rules, Next.js and React domains enabled

## Platform Requirements

**Development:**
- Bun 1.3.11+
- Node.js 24.x (NestJS API compiled and served via Node)
- Run web: `bun run dev:web` (starts Next.js on default port 3000)
- Run API: `bun run dev:api` (starts NestJS with watch mode)

**Production:**
- Web: Static/SSR output via `next build` → `next start`, suitable for Vercel (Vercel branding present in default page)
- API: `nest build` compiles to `apps/api/dist/`, served via `node dist/main`

---

*Stack analysis: 2026-03-24*
