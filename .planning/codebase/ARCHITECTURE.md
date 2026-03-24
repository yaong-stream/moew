# Architecture

**Analysis Date:** 2026-03-24

## Pattern Overview

**Overall:** Monorepo with two independent applications — a NestJS REST API and a Next.js frontend.

**Key Characteristics:**
- Bun workspaces monorepo (`apps/*`, `packages/*`)
- API follows NestJS Module/Controller/Service layered architecture
- Web follows Next.js App Router file-system routing
- No shared packages exist yet (`packages/` directory is empty)
- Applications are currently decoupled — no direct code sharing

## Layers

**API — Module Layer:**
- Purpose: Declares dependency injection graph, groups controllers and providers
- Location: `apps/api/src/app.module.ts`
- Contains: `@Module` decorated class that wires controllers and services
- Depends on: NestJS core DI framework
- Used by: `apps/api/src/main.ts` bootstrap function

**API — Controller Layer:**
- Purpose: Handles incoming HTTP requests, maps routes to service calls
- Location: `apps/api/src/app.controller.ts`
- Contains: `@Controller` decorated classes with `@Get`/`@Post`/etc. route handlers
- Depends on: Service layer (injected via constructor)
- Used by: NestJS router (registered in Module)

**API — Service Layer:**
- Purpose: Contains business logic, separated from transport concerns
- Location: `apps/api/src/app.service.ts`
- Contains: `@Injectable` decorated classes with pure business methods
- Depends on: Nothing yet (future: repositories, external clients)
- Used by: Controllers

**Web — Layout Layer:**
- Purpose: Defines shared UI shell applied to all routes
- Location: `apps/web/src/app/layout.tsx`
- Contains: Root HTML shell, global font loading (`Geist`, `Geist Mono`), global CSS import
- Depends on: `next/font/google`, `./globals.css`
- Used by: All pages via Next.js App Router nesting

**Web — Page Layer:**
- Purpose: Route-level React Server Components rendered per URL segment
- Location: `apps/web/src/app/page.tsx`
- Contains: Default export React components, server-side data fetching (future)
- Depends on: `next/image`, layout context
- Used by: Next.js App Router

## Data Flow

**API Request Handling:**

1. HTTP request arrives at the NestJS server (default port `3000`)
2. NestJS router matches request to a controller route handler
3. Controller calls the relevant service method, passing route params/body
4. Service executes business logic and returns a value
5. Controller returns the value; NestJS serializes to HTTP response

**Web Page Render:**

1. Browser requests a URL
2. Next.js App Router resolves the segment to `apps/web/src/app/page.tsx`
3. `layout.tsx` wraps page output with root HTML shell and fonts
4. React Server Component renders to HTML/RSC payload
5. Response delivered to browser; hydration occurs for interactive islands

**State Management:**
- Not established. No state management library is configured; the web app is a default scaffold.

## Key Abstractions

**NestJS Module (`@Module`):**
- Purpose: Unit of encapsulation — groups related controllers, services, and imports
- Examples: `apps/api/src/app.module.ts`
- Pattern: Decorator-based dependency declaration; all providers registered here are available for injection

**NestJS Injectable Service (`@Injectable`):**
- Purpose: Holds business logic, injectable into any controller or other service
- Examples: `apps/api/src/app.service.ts`
- Pattern: Class with `@Injectable()` decorator, constructor-injected dependencies

**Next.js App Router Layout:**
- Purpose: Persistent UI shell that survives route transitions
- Examples: `apps/web/src/app/layout.tsx`
- Pattern: Default export React component accepting `{ children: React.ReactNode }`

**Next.js Page:**
- Purpose: Route endpoint; one `page.tsx` per URL segment
- Examples: `apps/web/src/app/page.tsx`
- Pattern: Default export React Server Component (async-capable)

## Entry Points

**API Bootstrap:**
- Location: `apps/api/src/main.ts`
- Triggers: `bun run --filter @moew/api start:dev` or `node dist/main`
- Responsibilities: Creates NestFactory app from `AppModule`, binds to `PORT` env var (default `3000`)

**Web Bootstrap:**
- Location: `apps/web/src/app/layout.tsx` (App Router root) and `.next/` build output
- Triggers: `bun run --filter @moew/web dev` or `next start`
- Responsibilities: Defines root HTML document, loads fonts, wraps all pages

## Error Handling

**Strategy:** Not yet established — default NestJS exception filter behavior only.

**Patterns:**
- NestJS built-in exception filter converts unhandled errors to HTTP 500 responses
- Custom `HttpException` subclasses can be thrown from services/controllers (not yet used)
- No global exception filter or interceptor registered in `AppModule`

## Cross-Cutting Concerns

**Logging:** NestJS built-in logger (no configuration). Web: none configured.
**Validation:** Not configured — no `ValidationPipe` registered globally.
**Authentication:** Not implemented — no auth guards or middleware present.

---

*Architecture analysis: 2026-03-24*
