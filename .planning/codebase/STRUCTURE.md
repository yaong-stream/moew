# Codebase Structure

**Analysis Date:** 2026-03-24

## Directory Layout

```
moew/                          # Monorepo root
├── apps/
│   ├── api/                   # NestJS REST API (@moew/api)
│   │   ├── src/               # Application source
│   │   │   ├── main.ts        # Bootstrap entry point
│   │   │   ├── app.module.ts  # Root NestJS module
│   │   │   ├── app.controller.ts
│   │   │   ├── app.controller.spec.ts  # Unit test
│   │   │   └── app.service.ts
│   │   ├── test/              # E2E tests
│   │   │   └── app.e2e.spec.ts
│   │   ├── dist/              # Compiled output (gitignored)
│   │   ├── nest-cli.json      # NestJS CLI config
│   │   ├── tsconfig.json      # API TypeScript config
│   │   └── package.json
│   └── web/                   # Next.js frontend (@moew/web)
│       ├── src/
│       │   └── app/           # Next.js App Router root
│       │       ├── layout.tsx  # Root layout
│       │       ├── page.tsx    # Home route (/)
│       │       ├── globals.css # Global styles
│       │       └── favicon.ico
│       ├── public/            # Static assets (SVGs, images)
│       ├── next.config.ts     # Next.js config
│       ├── tsconfig.json      # Web TypeScript config
│       └── package.json
├── packages/                  # Shared packages (empty — reserved for future use)
├── node_modules/              # Hoisted monorepo dependencies
├── biome.json                 # Monorepo-wide linter/formatter config
├── tsconfig.json              # Root TypeScript project references
├── package.json               # Workspace definition + root scripts
├── bun.lock                   # Bun lockfile (gitignored)
└── .gitignore
```

## Directory Purposes

**`apps/api/src/`:**
- Purpose: All NestJS application source code
- Contains: Module, controller, and service files; co-located unit tests (`*.spec.ts`)
- Key files: `main.ts` (entry), `app.module.ts` (DI root)

**`apps/api/test/`:**
- Purpose: E2E tests that boot the full NestJS application
- Contains: `*.e2e.spec.ts` files using `supertest`
- Key files: `app.e2e.spec.ts`

**`apps/api/dist/`:**
- Purpose: TypeScript compilation output for production
- Contains: Compiled JS files
- Generated: Yes — by `nest build`
- Committed: No (gitignored)

**`apps/web/src/app/`:**
- Purpose: Next.js App Router file tree — every subdirectory maps to a URL segment
- Contains: `layout.tsx`, `page.tsx`, `loading.tsx`, `error.tsx` (Next.js conventions)
- Key files: `layout.tsx` (root shell), `page.tsx` (home route)

**`apps/web/public/`:**
- Purpose: Static files served directly at the root URL
- Contains: SVG icons, images
- Generated: No — manually placed assets

**`packages/`:**
- Purpose: Shared code libraries consumed by multiple apps
- Contains: Nothing yet — directory reserved for future shared packages
- When to use: Extract shared types, utilities, or UI components here when they are needed by both `apps/api` and `apps/web`

## Key File Locations

**Entry Points:**
- `apps/api/src/main.ts`: NestJS application bootstrap
- `apps/web/src/app/layout.tsx`: Next.js App Router root layout

**Configuration:**
- `biome.json`: Monorepo-wide linting and formatting rules
- `tsconfig.json` (root): TypeScript project references (`apps/web`, `apps/api`)
- `apps/api/tsconfig.json`: API-specific TS config (decorator metadata, nodenext module)
- `apps/web/tsconfig.json`: Web-specific TS config (dom lib, jsx, bundler resolution)
- `apps/api/nest-cli.json`: NestJS CLI build settings
- `apps/web/next.config.ts`: Next.js server configuration
- `package.json` (root): Workspace definition, root-level scripts

**Core Logic:**
- `apps/api/src/app.module.ts`: Root NestJS DI module
- `apps/api/src/app.controller.ts`: HTTP route handlers
- `apps/api/src/app.service.ts`: Business logic layer

**Testing:**
- `apps/api/src/app.controller.spec.ts`: Unit tests (co-located with source)
- `apps/api/test/app.e2e.spec.ts`: E2E integration tests

## Naming Conventions

**Files:**
- NestJS: `<name>.<type>.ts` — e.g., `app.controller.ts`, `app.service.ts`, `app.module.ts`
- NestJS unit tests: `<name>.<type>.spec.ts` — e.g., `app.controller.spec.ts`
- NestJS E2E tests: `<name>.e2e.spec.ts` — e.g., `app.e2e.spec.ts`
- Next.js routes: reserved filenames (`page.tsx`, `layout.tsx`, `loading.tsx`, `error.tsx`)
- Next.js components: `PascalCase.tsx` for custom components

**Directories:**
- App Router route segments: lowercase with hyphens (`user-profile/`, `api/`)
- Feature modules in API: lowercase with hyphens (`user-auth/`, `billing/`)

## Where to Add New Code

**New API Feature Module:**
- Create directory: `apps/api/src/<feature>/`
- Module: `apps/api/src/<feature>/<feature>.module.ts`
- Controller: `apps/api/src/<feature>/<feature>.controller.ts`
- Service: `apps/api/src/<feature>/<feature>.service.ts`
- Unit tests: `apps/api/src/<feature>/<feature>.controller.spec.ts` (co-located)
- Register the new module in: `apps/api/src/app.module.ts` → `imports: []`

**New Web Route:**
- Create directory: `apps/web/src/app/<route-segment>/`
- Add `page.tsx` inside the directory for the route component
- Add `layout.tsx` if the route needs its own layout shell

**New Web Component:**
- Create `apps/web/src/components/` if it doesn't exist
- Place reusable UI components there as `PascalCase.tsx` files

**Shared Code (used by both apps):**
- Create `packages/<package-name>/` with its own `package.json` and `src/index.ts`
- Add the workspace package as a dependency in the consuming app's `package.json`

**Utilities:**
- API utilities: `apps/api/src/common/` (create if needed)
- Web utilities: `apps/web/src/lib/` or `apps/web/src/utils/` (create if needed)

## Special Directories

**`.planning/`:**
- Purpose: GSD planning documents and codebase analysis outputs
- Generated: No — manually maintained
- Committed: Yes

**`.claude/`:**
- Purpose: Claude Code agent configuration, commands, and skill references
- Generated: Partially — managed by the GSD framework
- Committed: Yes

**`.agents/`:**
- Purpose: Agent skill definitions
- Generated: No
- Committed: Yes

**`node_modules/` (root):**
- Purpose: Hoisted shared dependencies managed by Bun workspaces
- Generated: Yes — by `bun install`
- Committed: No (gitignored)

---

*Structure analysis: 2026-03-24*
