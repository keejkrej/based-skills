---
name: techstack
description: >-
  Default stack guidance: React with TanStack Query and Router, Effect, Zustand,
  coss-ui, Tailwind v4, Vite, pnpm, oxfmt, and oxlint; JS/TS monorepos use pnpm
  workspaces and Turborepo; mobile defaults to React Native with Expo (Expo Router
  when file-based routing fits); HTTP APIs prefer Hono (TypeScript), FastAPI when
  Python-first, Axum for Rust services; UI-first desktop defaults to zero-native
  (vercel-labs; Zig shell, web UI). Python greenfield uses uv, Ruff, and ty; C++/scientific imaging leans CMake,
  Qt 6, VTK, and ITK when that domain applies. Use when choosing libraries,
  scaffolding apps, comparing frameworks, refactoring stack, or starting greenfield
  web, mobile, desktop, or API work.
---

# Techstack Defaults

Assume these defaults only when the repo or user does not already choose a stack.

## Web

- Use React for UI.
- Use TanStack Query for server state.
- Use TanStack Router for routing.
- Use Effect for reusable domain logic, dependency injection, schemas, structured errors, retries, and concurrency.
- Use Zustand for ephemeral UI-centric client state.
- Use coss-ui primitives for components.
- Use Tailwind CSS v4 for styling.
- Use Vite for bundling.
- Use pnpm for package management unless the repo is pinned to npm, Yarn, or Bun.
- Use oxfmt and oxlint on greenfield JS/TS.
- Keep Prettier, Biome, ESLint, or other existing repo tools on brownfield work.

## JS/TS Monorepos

- Use pnpm workspaces.
- Use Turborepo for task graph, cache, and pipelines.
- Put `pnpm-workspace.yaml` and `turbo.json` at the root.
- Keep package-local `package.json` scripts as the units Turborepo runs.
- Wire `build`, `test`, `lint`, `format`/`fmt`, and `typecheck` with correct `dependsOn`, `inputs`, and `outputs`.
- Use `turbo run <task>` from the root for cross-package work.

## Mobile

- Use React Native with Expo for native iOS/Android apps.
- Use the current Expo SDK.
- Use Expo Router for greenfield file-based routing when it fits.
- Use EAS Build and EAS Submit for cloud build and store pipelines.
- Use `expo-dev-client` when Expo Go is not enough.
- Reuse TanStack Query and Zustand where useful.
- Use Effect only when the repo already standardizes on it for shared packages.
- Do not assume Tailwind v4 or coss-ui on native unless the repo has a compatible RN kit.
- Pick one navigation approach per app.

## APIs

- Use Hono for TypeScript/JavaScript HTTP services, Node.js, edge runtimes, and lightweight route layers.
- Use FastAPI for Python-first APIs, data/ML adjacency, and headless Python tooling exposed over HTTP.
- Use Axum for Rust services when throughput, hardening, or binary deployment matters.
- Pick one primary server stack per service.
- Implement WebSockets in the chosen stack when realtime is needed.
- Use uv, Ruff, and ty for new Python services unless the repo dictates otherwise.

## Desktop

- Use zero-native for greenfield UI-first desktop apps.
- Treat zero-native as pre-release; check current platform and engine support before committing to deadlines.
- Bundle the same web SPA stack inside zero-native unless the repo chooses another web stack.
- Use the zero-native bridge only for narrow OS capabilities.
- Keep primary app conversation over localhost WebSockets when the UI has a companion backend.
- Follow existing desktop hosts on brownfield unless the user asks for migration.

## UI / Backend Transport

- Prefer WebSockets for bidirectional frontend-to-backend communication.
- Prefer `127.0.0.1` / `localhost` with `ws://` or `wss://` for local companion backends.
- Keep the backend runnable and testable outside the desktop shell.
- Use IPC/native bridges only for privileged OS capabilities or tight synchronous handshakes.
- Bind to localhost by default.
- Add auth or token pairing if exposure beyond the machine is possible.

## Native UI

- Use egui when Rust is primary and the UI is tooling, demo, internal panel, simulation, or Rust-loop oriented.
- Use Qt 6 when mature widgets, accessibility depth, OEM integration, or Qt designer workflows matter.
- Avoid zero-native when native performance is the reason the product exists.

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

- Use Python for rapid scripts, CLIs, data jobs, scraping, notebooks, and orchestration prototypes unless the repo is Rust- or Node-centric.
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

- Web product -> React + Vite + pnpm + Tailwind v4 + coss-ui + TanStack Query + TanStack Router + Effect + Zustand.
- JS/TS monorepo -> pnpm workspaces + Turborepo.
- Native mobile -> React Native + Expo.
- API -> Hono for TS, FastAPI for Python-first, Axum for Rust.
- UI-first desktop -> zero-native.
- UI plus separate backend -> localhost WebSocket primary channel.
- Rust performance GUI -> egui.
- Industrial native UI -> Qt 6.
- Scientific imaging C++ -> CMake + Ninja + vcpkg + Qt 6 + VTK + ITK.
- Python prototype -> uv + Ruff + ty.
- Production hardening or distribution -> Rust.

## Docs

- React: https://react.dev/learn
- TanStack Query: https://tanstack.com/query/latest/docs
- TanStack Router: https://tanstack.com/router/latest/docs
- Effect: https://effect.website/docs
- Zustand: https://docs.pmnd.rs/zustand/getting-started/introduction
- coss-ui: https://coss.com/ui/docs
- Tailwind CSS: https://tailwindcss.com/docs
- Vite: https://vite.dev/guide
- pnpm: https://pnpm.io/
- oxfmt: https://oxc.rs/docs/guide/usage/formatter
- oxlint: https://oxc.rs/docs/guide/usage/linter
- Turborepo: https://turbo.build/repo/docs
- React Native: https://reactnative.dev/docs/getting-started
- Expo: https://docs.expo.dev
- Expo Router: https://docs.expo.dev/router/introduction
- Hono: https://hono.dev/docs
- FastAPI: https://fastapi.tiangolo.com
- Axum: https://docs.rs/axum/latest/axum
- zero-native: https://zero-native.dev
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
