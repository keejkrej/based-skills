---
name: techstack
description: >-
  Default stack guidance: React with Effect Atom and TanStack Router, Effect,
  coss-ui, Tailwind v4, Vite, Bun, oxfmt, and oxlint; JS/TS monorepos use Bun
  workspaces and Turborepo; mobile defaults to React Native with Expo (Expo Router
  when file-based routing fits); TypeScript HTTP APIs use Effect Platform on Bun
  (@effect/platform-bun); FastAPI when Python-first, Axum for Rust services;
  UI-first desktop defaults to Electron. End-to-end type safety across TS↔TS,
  TS↔Python, and TS↔Rust via shared Effect Schema, OpenAPI, or AsyncAPI.
  Python greenfield uses uv, Ruff, and ty; C++/scientific imaging leans CMake,
  Qt 6, VTK, and ITK when that domain applies. Use when choosing libraries, scaffolding apps, comparing frameworks, refactoring
  stack, or starting greenfield web, mobile, desktop, or API work.
---

# Techstack Defaults

Assume these defaults only when the repo or user does not already choose a stack.

- On brownfield repos, follow the stack already in use. Do not migrate runtime, package managers, frameworks, or tooling unless the user explicitly asks.

## Effect

Effect is the backbone for TypeScript domain logic, IO, and services — not just a helper library.

- Model business logic as `Effect` programs with `Effect.gen`.
- Define services with `Context.Tag` and wire them with `Layer`.
- Use `Schema` for validation, decoding, and shared contracts between client and server.
- Model failures with tagged errors (`Data.TaggedError`) instead of thrown exceptions.
- Compose retries, timeouts, concurrency, and resource scopes in Effect — not ad hoc Promise chains.
- Keep side effects at the edges; keep handlers and domain functions pure over `Effect`.
- Run servers and CLIs with `BunRuntime.runMain`.
- Test services and handlers by providing test `Layer`s instead of mocking globals.

## Web

- Use React for UI.
- Use Effect Atom (`@effect-atom/atom-react`) for client and async state — UI state, derived state, and server data loaded via Effect programs.
- Use `Atom.make` for atoms; read and write with `useAtomValue` and `useAtomSet`.
- Wire services with `Atom.runtime` and shared Effect `Layer`s from domain packages.
- Use `AtomHttpApi` (or `AtomRpc`) for typed query and mutation atoms against shared Effect API definitions.
- Use `@effect/experimental` `Reactivity` with `Atom.runtime` when mutations should refresh related async atoms.
- Use TanStack Router for routing.
- Use Effect for domain logic, API clients, schemas, structured errors, retries, and concurrency.
- Use `@effect/platform` HttpClient (or generated clients from shared API schemas) for HTTP calls.
- Share `Schema` and domain packages with the backend in monorepos — see Type Safety.
- Generate TypeScript from OpenAPI when the backend is Python or Rust.
- Use coss-ui primitives for components.
- Use Tailwind CSS v4 for styling.
- Use Vite for bundling.
- Use Bun for package management.
- Use oxfmt and oxlint on greenfield JS/TS.

## JS/TS Monorepos

- Use Bun workspaces via root `package.json` `"workspaces"`.
- Use Turborepo for task graph, cache, and pipelines.
- Put root `package.json`, `bun.lock`, and `turbo.json` at the root.
- Keep package-local `package.json` scripts as the units Turborepo runs.
- Wire `build`, `test`, `lint`, `format`/`fmt`, and `typecheck` with correct `dependsOn`, `inputs`, and `outputs`.
- Use `turbo run <task>` from the root for cross-package work.
- Split shared Effect code into packages such as `domain` (schemas, services, errors) and `api` (HttpApi definitions, handlers).
- Keep React apps thin: state in Effect Atom atoms, domain logic in shared Effect packages.

## Mobile

- Use React Native with Expo for native iOS/Android apps.
- Use the current Expo SDK.
- Use Expo Router for greenfield file-based routing when it fits.
- Use EAS Build and EAS Submit for cloud build and store pipelines.
- Use `expo-dev-client` when Expo Go is not enough.
- Reuse Effect Atom where useful.
- Share Effect schemas, clients, and domain packages with web and API where possible (TS↔TS).
- Use OpenAPI- or AsyncAPI-generated clients when the API is Python or Rust (TS↔Python, TS↔Rust).
- Do not assume Tailwind v4 or coss-ui on native unless the repo has a compatible RN kit.
- Pick one navigation approach per app.

## APIs

- Use `@effect/platform` for TypeScript/JavaScript HTTP services — not a separate router framework.
- Prefer `HttpApi` + `HttpApiBuilder` for declarative route and schema definitions.
- Use `HttpRouter` when a lower-level router fits better than `HttpApi`.
- Serve on Bun with `@effect/platform-bun` (`BunHttpServer.layer(…)`).
- Implement handlers as `Effect` programs; provide dependencies through `Layer`.
- Reuse the same `Schema` types the web and mobile clients decode.
- Use FastAPI for Python-first APIs, data/ML adjacency, and headless Python tooling exposed over HTTP.
- Use Axum for Rust services when throughput, hardening, or binary deployment matters.
- Type Axum handlers with `serde` and export OpenAPI via `utoipa` when Rust serves TypeScript clients.
- When the backend is not TypeScript, keep contracts machine-readable — see Type Safety.
- Pick one primary server stack per service.
- Implement WebSockets with Effect Platform `Socket` when realtime is needed in Effect services.
- Use uv, Ruff, and ty for new Python services unless the repo dictates otherwise.

## Type Safety

Every TypeScript client (web, mobile, Electron) should have typed HTTP and WebSocket boundaries. Pick the contract strategy by backend language.

### Rules (all pairings)

- Treat compile-time types as necessary but not sufficient — decode or validate at runtime on untrusted input.
- Use one contract per transport: shared `Schema` or OpenAPI for HTTP; shared `Schema` or AsyncAPI for WebSockets.
- Regenerate cross-language client types in CI; fail the build when specs drift.
- Wrap HTTP and socket IO in Effect programs — keep React components free of raw `fetch` and manual JSON parsing.
- Prefer typed message envelopes (`{ "type": "...", "payload": ... }`) for WebSocket protocols.

### TS ↔ TS

Strongest guarantee — one monorepo, one schema graph, no codegen between app and API.

- Put `Schema`, tagged errors, and domain services in a shared `domain` package.
- Put `HttpApi` / handler wiring in a shared `api` package; import the same schemas on client and server.
- Encode requests and decode responses with the same `Schema` on both sides.
- WebSocket payloads: same `Schema` definitions in `domain`; no OpenAPI or AsyncAPI step needed internally.
- Export OpenAPI from Effect Platform only when external consumers need it.

### TS ↔ Python

Compile-time types from OpenAPI; runtime validation on both sides.

- Backend: Pydantic v2 models on every FastAPI route body, query model, and response; type-check Python with `ty` or Pyright.
- HTTP: FastAPI publishes `/openapi.json` from those models — treat it as the contract; generate TS with `openapi-typescript`.
- Client: wrap generated types in Effect programs; add `Schema` decoding on responses when runtime validation matters.
- WebSocket: maintain AsyncAPI for message shapes; implement payloads as Pydantic models that match the spec.
- CI: regenerate `openapi.json` and TS client output; optionally lint the spec with Spectral.

### TS ↔ Rust

Compile-time types from OpenAPI; serde-backed runtime checks on the server.

- Backend: `serde` types on Axum handlers; derive OpenAPI with `utoipa` — no `serde_json::Value` in domain code.
- HTTP: export OpenAPI from `utoipa` → `openapi-typescript` on the TS side.
- WebSocket: `asyncapi-rust` with Axum (or a checked-in AsyncAPI spec) aligned with `serde` message enums.
- CI: regenerate OpenAPI and TS client output from Rust builds or spec files.

### Non-HTTP boundaries

For Tauri commands, Electron IPC, or other non-REST surfaces without OpenAPI:

- Rust: Specta (`specta::Type`) → TypeScript export.
- Python: Pydantic models → TypeScript via `pydantic-to-typescript` when AsyncAPI does not apply.

## Desktop

- Use Electron for greenfield UI-first desktop apps.
- Bundle the same web SPA stack inside Electron unless the repo chooses another web stack.
- Use Electron preload/IPC only for narrow OS capabilities.
- Run companion backends as Effect Platform services on Bun over localhost when the UI needs a separate process.
- Keep primary app conversation over localhost WebSockets when the UI has a companion backend.

## UI / Backend Transport

- Prefer WebSockets for bidirectional frontend-to-backend communication.
- Prefer `127.0.0.1` / `localhost` with `ws://` or `wss://` for local companion backends.
- Keep the backend runnable and testable outside the desktop shell.
- Use Effect Platform `Socket` in Effect backends; use IPC/native bridges only for privileged OS capabilities or tight synchronous handshakes.
- Bind to localhost by default.
- Add auth or token pairing if exposure beyond the machine is possible.

## Native UI

- Use egui when Rust is primary and the UI is tooling, demo, internal panel, simulation, or Rust-loop oriented.
- Use Qt 6 when mature widgets, accessibility depth, OEM integration, or Qt designer workflows matter.
- Avoid Electron when native performance is the reason the product exists.

## C++ Scientific Imaging

- Use CMake for builds.
- Prefer Ninja for fast incremental builds.
- Use Visual Studio or Xcode generators when the team workflow requires them.
- Use vcpkg manifest mode on greenfield dependency management.
- Use Qt 6 for app UI.
- Use VTK for 3D visualization and data pipelines.
- Use ITK for n-dimensional image IO, filtering, registration, and algorithms.
- Follow Conan, Conda, FetchContent, vendor SDKs, or OS packages when the repo already uses them.

## Python

- Use Python for rapid scripts, CLIs, data jobs, scraping, notebooks, and orchestration prototypes unless the repo is Rust- or Bun-centric.
- Use Pydantic v2 models on all FastAPI boundaries; publish OpenAPI for TS clients (see Type Safety).
- Use uv for project management, dependency sync, lockfiles, and `uv run`.
- Use Ruff for linting and formatting.
- Use ty for type checking.
- Wire `ruff check`, `ruff format`, and `ty check` into CI.
- Follow Poetry, PDM, Hatch, pip-tools, conda, pip+venv, mypy, Pyright, or basedpyright when already standardized.
- Treat prototypes as transitional; keep core algorithms easy to migrate.

## Python + Qt

- Use PySide6 only to extend existing QtPy, PyQt, or PySide-style codebases.
- Reassess Qt bindings, Rust UI, or web UI for greenfield Python GUI work.

## Production

- Prefer Rust for maintained binaries, services, installers, crates, reproducible deployments, performance, and long-lived production code.
- Do not replace a working Rust production path with Python for convenience unless the user explicitly wants that tradeoff.

## Decision Flow

- Web product -> React + Vite + Bun + Tailwind v4 + coss-ui + Effect Atom + TanStack Router + Effect.
- JS/TS monorepo -> Bun workspaces + Turborepo + shared Effect domain/api packages.
- Native mobile -> React Native + Expo + shared Effect schemas/clients.
- TS ↔ TS -> shared Effect `Schema` + `HttpApi`; WebSocket via same schemas.
- TS ↔ Python -> FastAPI + Pydantic + OpenAPI + AsyncAPI -> openapi-typescript.
- TS ↔ Rust -> Axum + serde + utoipa + asyncapi-rust -> openapi-typescript.
- TypeScript API -> Effect Platform (`HttpApi` or `HttpRouter`) + `@effect/platform-bun`.
- UI-first desktop -> Electron + typed backend companion (Effect, FastAPI, or Axum per pairing above).
- UI plus separate backend -> REST over OpenAPI; WebSocket over shared Schema (TS) or AsyncAPI (Python/Rust).
- Rust performance GUI -> egui.
- Industrial native UI -> Qt 6.
- Scientific imaging C++ -> CMake + Ninja + vcpkg + Qt 6 + VTK + ITK.
- Python prototype -> uv + Ruff + ty.
- Production hardening or distribution -> Rust.

## Docs

- React: https://react.dev/learn
- TanStack Router: https://tanstack.com/router/latest/docs
- Effect: https://effect.website/docs
- Effect Atom: https://github.com/tim-smart/effect-atom
- Effect Platform: https://effect.website/docs/platform/introduction
- Effect Platform README (HttpApi, HttpServer): https://github.com/Effect-TS/effect/blob/main/packages/platform/README.md
- coss-ui: https://coss.com/ui/docs
- Tailwind CSS: https://tailwindcss.com/docs
- Vite: https://vite.dev/guide
- Bun: https://bun.sh/docs
- oxfmt: https://oxc.rs/docs/guide/usage/formatter
- oxlint: https://oxc.rs/docs/guide/usage/linter
- Turborepo: https://turbo.build/repo/docs
- React Native: https://reactnative.dev/docs/getting-started
- Expo: https://docs.expo.dev
- Expo Router: https://docs.expo.dev/router/introduction
- FastAPI: https://fastapi.tiangolo.com
- Pydantic: https://docs.pydantic.dev
- OpenAPI: https://swagger.io/specification/
- AsyncAPI: https://www.asyncapi.com/docs
- openapi-typescript: https://openapi-ts.dev
- openapi-fetch: https://openapi-ts.dev/openapi-fetch/
- utoipa: https://docs.rs/utoipa/latest/utoipa/
- asyncapi-rust: https://github.com/mlilback/asyncapi-rust
- Axum: https://docs.rs/axum/latest/axum
- serde: https://serde.rs
- Specta (IPC only): https://docs.rs/specta/latest/specta/
- pydantic-to-typescript (IPC only): https://github.com/phillipdupuis/pydantic-to-typescript
- Electron: https://www.electronjs.org/docs
- WebSocket: https://developer.mozilla.org/en-US/docs/Web/API/WebSocket
- egui: https://docs.rs/egui/latest/egui
- Qt 6: https://doc.qt.io/qt-6
- CMake: https://cmake.org/cmake/help/latest/
- Ninja: https://ninja-build.org/manual.html
- vcpkg: https://learn.microsoft.com/en-us/vcpkg/
- VTK: https://vtk.org/documentation/
- ITK: https://docs.itk.org/
- PySide6: https://doc.qt.io/qtforpython-6
- Python: https://docs.python.org/3
- uv: https://docs.astral.sh/uv/
- Ruff: https://docs.astral.sh/ruff/
- ty: https://docs.astral.sh/ty/
- Rust: https://doc.rust-lang.org
