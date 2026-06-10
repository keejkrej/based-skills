# Frontend

User-facing UI — web SPA and native mobile.

## Web defaults

- Use React for UI
- Use Effect Atom (`@effect-atom/atom-react`) for client and async state — UI state, derived state, and server data loaded via Effect programs
- Use `Atom.make` for atoms; read and write with `useAtomValue` and `useAtomSet`
- Wire services with `Atom.runtime` and shared Effect `Layer`s from domain packages
- Use `AtomHttpApi` (or `AtomRpc`) for typed query and mutation atoms against shared Effect API definitions
- Use `@effect/experimental` `Reactivity` with `Atom.runtime` when mutations should refresh related async atoms
- Use TanStack Router for routing
- Use Effect for domain logic, API clients, schemas, structured errors, retries, and concurrency
- Use `@effect/platform` HttpClient (or generated clients from shared API schemas) for HTTP calls
- Use coss-ui primitives for components
- Use Tailwind CSS v4 for styling
- Use Vite for bundling
- Use Bun for package management and runtime
- SSR or colocated UI+API → [fullstack.md](fullstack.md) (TanStack Start)
- Share `Schema` and domain packages with the backend in monorepos → [contracts.md](contracts.md)
- Generate TypeScript from OpenAPI when the backend is Python or Rust → [contracts.md](contracts.md)
- Relative imports → [dev.md](dev.md) (always extensionless)
- Tooling → [dev.md](dev.md)
- Effect patterns → `effect-ts` skill

## Monorepo layout

- Use Bun workspaces via root `package.json` `"workspaces"`
- Split shared Effect code into packages such as `domain` (schemas, services, errors) and `api` (HttpApi definitions, handlers)
- Keep React apps thin: state in Effect Atom atoms, domain logic in shared Effect packages
- Turborepo task wiring → [dev.md](dev.md)
- Scaffold → [../scaffolds/ts-monorepo.md](../scaffolds/ts-monorepo.md)

## Mobile

- Use React Native with Expo for native iOS/Android apps
- Use the current Expo SDK
- Use Expo Router for greenfield file-based routing when it fits
- Use EAS Build and EAS Submit for cloud build and store pipelines
- Use `expo-dev-client` when Expo Go is not enough
- Reuse Effect Atom where useful
- Share Effect schemas, clients, and domain packages with web and API where possible (TS↔TS)
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
