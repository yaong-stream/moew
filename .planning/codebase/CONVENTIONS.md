# Coding Conventions

**Analysis Date:** 2026-03-24

## Naming Patterns

**Files:**
- Next.js pages and layouts use lowercase with no separators: `page.tsx`, `layout.tsx`, `globals.css`
- NestJS files use dot-separated kebab-case with role suffix: `app.controller.ts`, `app.service.ts`, `app.module.ts`
- Test files co-located with source use `.spec.ts` suffix: `app.controller.spec.ts`
- E2E test files use `.e2e.spec.ts` suffix: `app.e2e.spec.ts`
- Config files use kebab-case: `next.config.ts`, `nest-cli.json`, `postcss.config.mjs`

**Functions:**
- camelCase for all function names: `getHello()`, `bootstrap()`
- Default exports for Next.js page components: `export default function Home()`
- Default exports for Next.js layout components: `export default function RootLayout()`
- Named exports for NestJS classes and constants: `export class AppController`, `export const metadata`

**Variables:**
- camelCase for all variables: `appController`, `moduleFixture`, `geistSans`, `geistMono`
- `const` preferred for module-level declarations

**Types/Classes/Interfaces:**
- PascalCase for all class names: `AppController`, `AppService`, `AppModule`
- PascalCase for TypeScript interfaces and types: `Metadata`, `NextConfig`, `TestingModule`
- `type` keyword for type-only imports: `import type { Metadata } from "next"` (enforced in `apps/web`)
- In `apps/api`, `useImportType` is turned off (see `biome.json` overrides)

## Code Style

**Formatter:**
- Tool: Biome 2.2.0 (`biome.json`)
- Indent style: spaces
- Indent width: 2
- Applies to all files (excluding `node_modules`, `.next`, `dist`, `build`, `.claude`, `.agents`)

**Linting:**
- Tool: Biome 2.2.0
- Ruleset: `recommended` plus `next: recommended` and `react: recommended` domains
- `suspicious.noUnknownAtRules` is disabled (allows Tailwind CSS `@import`/`@theme` directives)
- `style.useImportType` is disabled only for `apps/api/**` (NestJS decorator metadata requires non-type imports)

**Import organization:**
- Biome `assist.actions.source.organizeImports` is set to `"on"` — imports are auto-sorted on save

## Import Organization

**Order (managed by Biome):**
1. External packages (e.g., `"@nestjs/common"`, `"next"`)
2. Internal/relative imports (e.g., `"./app.service"`, `"./globals.css"`)

**Path Aliases:**
- Both apps define `@/*` → `./src/*` in their `tsconfig.json`
  - `apps/web/tsconfig.json`: `"@/*": ["./src/*"]`
  - `apps/api/tsconfig.json`: `"@/*": ["./src/*"]`

**Type-only imports:**
- Use `import type { ... }` for type-only imports in `apps/web`:
  ```typescript
  import type { Metadata } from "next";
  import type { NextConfig } from "next";
  ```
- In `apps/api`, regular imports are used (NestJS decorator metadata requirement):
  ```typescript
  import { Controller, Get } from "@nestjs/common";
  ```

## NestJS Patterns

**Decorators:**
- `@Module()` on module classes with `imports`, `controllers`, `providers` arrays
- `@Controller()` with optional route prefix
- `@Injectable()` on all service classes
- `@Get()`, `@Post()`, etc. on controller methods

**Dependency Injection:**
- Constructor injection with `private readonly` for services:
  ```typescript
  constructor(private readonly appService: AppService) {}
  ```

**Module structure:**
- One module per feature domain
- Module imports its own controller and service providers
- Entry point `apps/api/src/main.ts` bootstraps via `NestFactory.create(AppModule)`

## Next.js Patterns

**App Router:**
- All pages use the App Router (`apps/web/src/app/`)
- `layout.tsx` defines the root HTML shell, fonts, and metadata
- `page.tsx` is the default export for each route segment

**Server Components:**
- Components are Server Components by default (no `"use client"` directive observed)
- Client components must be marked explicitly with `"use client"` at the top of the file

**Metadata:**
- Defined as a named `export const metadata: Metadata` in layout/page files:
  ```typescript
  export const metadata: Metadata = {
    title: "...",
    description: "...",
  };
  ```

**Styling:**
- Tailwind CSS v4 via `@import "tailwindcss"` in `apps/web/src/app/globals.css`
- Utility classes applied directly as JSX `className` strings
- CSS custom properties defined in `:root` for theme tokens (`--background`, `--foreground`)
- Dark mode via `@media (prefers-color-scheme: dark)`
- `@theme inline` block maps CSS vars to Tailwind theme tokens

**Fonts:**
- Google Fonts loaded via `next/font/google` with CSS variable injection:
  ```typescript
  const geistSans = Geist({ variable: "--font-geist-sans", subsets: ["latin"] });
  ```

**Images:**
- Use `next/image` `Image` component (not bare `<img>` tags)

## Error Handling

**Patterns:**
- API server listens on `process.env.PORT ?? 3000` — nullish coalescing for env var fallback
- No custom error handling infrastructure detected at this stage (scaffold-level codebase)
- NestJS default exception filters are implicitly active via `@nestjs/platform-express`

## Logging

**Framework:** None explicitly configured (NestJS built-in logger is available but not yet used)

**Patterns:**
- No `console.log` or logger calls observed in current source files

## Comments

**When to Comment:**
- No inline comments in source files; code is self-documenting at scaffold level

**TSDoc/JSDoc:**
- Not used in current source files

## TypeScript Strictness

**Both apps enable `"strict": true`**, which enforces:
- `noImplicitAny`
- `strictNullChecks`
- `strictFunctionTypes`
- `strictPropertyInitialization`

**Additional strict flags in `apps/api`:**
- `"noFallthroughCasesInSwitch": true`
- `"emitDecoratorMetadata": true` (required for NestJS DI)
- `"experimentalDecorators": true` (required for NestJS decorators)

## Module Design

**Exports:**
- Next.js components: default export only
- NestJS classes: named exports
- Config objects (Next, Nest): default export

**Barrel Files:**
- Not used at current scaffold stage

---

*Convention analysis: 2026-03-24*
