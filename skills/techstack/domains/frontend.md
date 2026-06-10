# Frontend

User-facing UI — web SPA and native mobile.

## Web defaults

- Use React for UI
- Use Effect Atom (`@effect-atom/atom-react`) for client and async state — UI state, derived state, and server data loaded via Effect programs
- Use `Atom.make` for atoms; read and write with `useAtomValue` and `useAtomSet`
- Wire services with `Atom.runtime` and shared Effect `Layer`s from `packages/client`
- Prefer port-backed query/mutation atom factories in `packages/client` — `AtomHttpApi` is fine when it maps cleanly to shared `HttpApi` definitions
- Use `@effect/experimental` `Reactivity` with `Atom.runtime` when mutations should refresh related async atoms
- Use TanStack Router for routing
- Use Effect for domain logic, API clients, schemas, structured errors, retries, and concurrency
- Use `@effect/platform` HttpClient via `packages/client` ports — not raw `fetch` in components
- Use coss-ui primitives for components
- Use Tailwind CSS v4 for styling
- Use Vite for bundling
- Use Bun for package management and runtime
- SSR or colocated UI+API → [fullstack.md](fullstack.md) (TanStack Start)
- Share `Schema` and contracts with the backend in monorepos → [contracts.md](contracts.md)
- Generate TypeScript from OpenAPI when the backend is Python or Rust → [contracts.md](contracts.md)
- Relative imports → [dev.md](dev.md) (always extensionless)
- Tooling → [dev.md](dev.md)
- Effect patterns → `effect-ts` skill

## Monorepo layout

- Use Bun workspaces via root `package.json` `"workspaces"`; pin shared versions with a Bun **catalog** at scale
- Split shared code into the package map below — not ad hoc `domain` / `api` unless the repo already uses those names
- Keep `apps/web` thin: routes, app-local writable atoms, port wiring — not feature UI or domain logic
- Turborepo task wiring → [dev.md](dev.md)
- Scaffold → [../scaffolds/ts-monorepo.md](../scaffolds/ts-monorepo.md)
- Multi-product repos: nest apps as `apps/<product>/web` (and `apps/<product>/server`) — same packages, product-specific routes/atoms only
- Agent notes for durable repo facts → `docs/agent/` (memory skill)

### Package map

| Package | Role |
| ------- | ---- |
| `contracts` | Effect `Schema` wire types, `HttpApi` definition, decode helpers; optional subpaths for non-wire UI types |
| `client` | Effect runtime, `HttpApiClient`, ports, sessions, shared query/mutation atoms |
| `utils` | Pure helpers — no React, no storage, no IO |
| `storage` | Sync storage abstraction (`localStorage`, `sessionStorage`; AsyncStorage when mobile exists) |
| `ui-headless` | Shared presentation logic — React hooks and render-prop components; no DOM |
| `ui` | Web rendering — coss primitives, shell, feature components; thin wrappers over headless |
| `web-app` | Shared Vite config, app bootstrap, port factory, global CSS entry |
| `<feature>` | Optional feature packages (e.g. `analysis`) when a domain grows large — catalogs, parsers, feature atoms |

### UI layering

```
utils          → pure math/helpers
ui-headless    → hooks + render-prop state (imports utils + contracts)
ui             → DOM rendering (imports ui-headless + utils)
apps/web       → routes + app atoms + port wiring (imports ui + client)
```

- Vendor coss primitives under `packages/ui/src/components/ui/` — do not edit
- Group shared UI under `packages/ui/src/shell/` (layout, chrome, providers) and `packages/ui/src/features/` (domain folders)
- Apps import via package subpaths — `@repo/ui/shell`, `@repo/ui/features`, `@repo/ui/components`; never deep paths into `src/features/...`
- Re-export public headless types from `@repo/ui/features` — apps should not import `@repo/ui-headless` unless they already depend on it
- Feature dependency rules: shared infra domains (e.g. `canvas/`) may be imported by siblings; no cross-feature imports between leaf domains
- Writable session UI atoms stay in each app; shared async query atoms stay in `packages/client`

### Client atoms

- `createAppRuntime` merges `Reactivity.layer` with port `Layer`s
- Query atoms: `Atom.family` for parameterized data; `Atom.keepAlive` for session-stable reads; `Atom.withReactivity` + `invalidateAfter` on mutations
- Apps bootstrap a `RegistryProvider` and bind ports from env via `web-app` factory

## Mobile (optional)

- Use React Native with Expo for native iOS/Android apps
- Use the current Expo SDK
- Use Expo Router for greenfield file-based routing when it fits
- Use EAS Build and EAS Submit for cloud build and store pipelines
- Use `expo-dev-client` when Expo Go is not enough
- Add `ui-native` + `mobile-app` packages mirroring `ui` + `web-app` — same `features/` boundaries, native renderers only
- Reuse `client`, `contracts`, `utils`, and `ui-headless` on native
- Use OpenAPI- or AsyncAPI-generated clients when the API is Python or Rust → [contracts.md](contracts.md)
- Do not assume Tailwind v4 or coss-ui on native unless the repo has a compatible RN kit
- Pick one navigation approach per app

## Docs

- React: https://react.dev/learn
- React Compiler: https://react.dev/learn/react-compiler
- TanStack Router: https://tanstack.com/router/latest/docs
- Effect: https://effect.website/docs
- Effect Atom: https://github.com/tim-smart/effect-atom
- coss-ui: https://coss.com/ui/docs
- Tailwind CSS: https://tailwindcss.com/docs
- Vite: https://vite.dev/guide
- Bun: https://bun.sh/docs
- React Native: https://reactnative.dev/docs/getting-started
- Expo: https://docs.expo.dev
- Expo Router: https://docs.expo.dev/router/introduction
