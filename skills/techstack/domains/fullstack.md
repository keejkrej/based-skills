# Fullstack

TypeScript apps with SSR, server functions, or colocated UI+API in one deploy.

## When to use

- Use this domain when the app needs SSR, server functions, or UI+API in one TypeScript app
- SPA without SSR → [frontend.md](frontend.md) (Vite + TanStack Router)
- Standalone API service → [backend.md](backend.md)

## Defaults

- Use TanStack Start for the app shell — built on TanStack Router
- Use React + Bun + Tailwind v4 + coss-ui (same UI stack as [frontend.md](frontend.md))
- Use TypeScript for the backend — Effect Platform `HttpApi` on Bun; not Python or Rust in this domain
- Use Effect Platform `HttpApi` for API contracts and handlers — not a separate router framework
- Use Effect Atom + `AtomHttpApi` for server-state in components — not TanStack Query by default
- Use route loaders to call Effect programs or a typed isomorphic HttpApi client — not raw `fetch`
- Keep domain logic in shared Effect packages; keep route files thin
- Contracts → [contracts.md](contracts.md)
- Tooling → [dev.md](dev.md)
- Handler patterns → [backend.md](backend.md)
- Relative imports → [dev.md](dev.md) (always extensionless)
- Effect patterns → `effect-ts` skill

## Architecture

Pick one TypeScript shape per app:

- **Colocated** — mount `HttpApi` handlers on TanStack Start server routes; one deploy, one monorepo
- **Split** — TanStack Start UI + separate Bun Effect API service; still TypeScript on both sides

Both use the same shared `domain` / `api` packages and `AtomHttpApi` client.

Python and Rust backends are for clean frontend/backend separation — use [frontend.md](frontend.md) + [backend.md](backend.md) + [contracts.md](contracts.md), not this domain.

## Loaders and SSR

- Run data loading in route `loader`s as Effect programs
- On the server, prefer direct handler invocation or an isomorphic client that skips HTTP at SSR time
- In the browser, use the same typed client over HTTP to the API route or split service
- Seed or hydrate Effect Atom state from loader results — use `HydrationBoundary`, `Atom.serializable`, or `withServerValue` when SSR must match client state
- Do not add TanStack Query unless a feature explicitly needs its cache semantics — then run Effect inside `queryFn`, never raw `fetch`

## Server-only code

- Put handlers, server layers, and privileged IO in `.server.ts` files — TanStack Start import protection excludes them from the client bundle
- Keep shared `Schema`, `HttpApi` definitions, and domain services in packages importable on both sides

## Rules

- Effect is the TS domain/IO backbone — same as SPA apps
- Backend language is TypeScript only — reserve Python and Rust for split front/back stacks
- One primary server stack per service
- Do not mix Atom `reactivityKeys` invalidation and TanStack Query cache invalidation in the same feature

## Docs

- TanStack Start: https://tanstack.com/start/latest/docs
- TanStack Router: https://tanstack.com/router/latest/docs
- Effect Platform: https://effect.website/docs/platform/introduction
- Effect Atom: https://github.com/tim-smart/effect-atom
