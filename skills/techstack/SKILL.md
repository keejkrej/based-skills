---
name: techstack
description: >-
  Default stack guidance by product domain — frontend, fullstack, backend,
  desktop, scientific, dev tooling, and cross-surface contracts. Covers React,
  Effect Atom, TanStack Router, TanStack Start, Effect Platform on Bun, coss-ui,
  Tailwind v4, Vite, Turborepo, Expo, FastAPI, Axum, Electron, uv/Ruff/ty,
  CMake/Qt/VTK/ITK, oxfmt/oxlint, and contracts (Effect Schema, OpenAPI,
  AsyncAPI). Use when choosing libraries, scaffolding apps, comparing
  frameworks, refactoring stack, setting up lint/CI, or starting greenfield work.
---

# Techstack

## Global rules

- Effect is the TS domain/IO backbone — not ad hoc Promises
- One primary server stack per service
- Repo conventions beat greenfield defaults when they conflict
- Typed boundaries at every seam — decode/validate at runtime on untrusted input
- Effect implementation depth in Effect repos → `effect-ts` skill
- Greenfield scaffolds → `template` skill

## Defaults at a glance

Enough to choose libraries and direction; read the linked file before implementing details.

### Frontend

- Web SPA: React + Vite + Bun + Tailwind v4 + coss-ui + Effect Atom + TanStack Router
- Client IO in Effect programs; state in atoms — not raw `fetch` in components
- Mobile: React Native + Expo (+ Expo Router when file-based routing fits)
- Monorepo: Bun workspaces + Turborepo; shared `domain` / `api` Effect packages
- → [domains/frontend.md](domains/frontend.md)

### Fullstack

- SSR or colocated UI+API: TanStack Start + Effect Platform `HttpApi` on Bun + Effect Atom
- TypeScript backend only — Python/Rust reserved for split front/back ([frontend](domains/frontend.md) + [backend](domains/backend.md))
- Route loaders call Effect programs; server-state in `AtomHttpApi` — not TanStack Query by default
- → [domains/fullstack.md](domains/fullstack.md)

### Backend

- TS API: `@effect/platform` (`HttpApi` or `HttpRouter`) on Bun — not a separate router framework
- Python API/scripts: FastAPI + Pydantic v2 + uv
- Rust API: Axum + serde + utoipa
- WebSockets in Effect services: Effect Platform `Socket`
- Prototype in Python; harden/distribute in Rust
- → [domains/backend.md](domains/backend.md)

### Desktop

- UI-first: Electron bundling the same web stack
- Separate process: companion backend on `localhost` over WebSocket; preload/IPC only for OS APIs
- Native UI: egui (Rust tooling/sim) or Qt 6 (industrial widgets) — not Electron when native perf is the point
- → [domains/desktop.md](domains/desktop.md)

### Scientific

- C++ imaging: CMake + Qt 6 + VTK + ITK
- Rust numerics: `ndarray` ecosystem — not hand-rolled NumPy ports
- → [domains/scientific.md](domains/scientific.md)

### Contracts

- TS ↔ TS: shared Effect `Schema` + `HttpApi` in monorepo — no codegen between app and API
- TS ↔ Python: FastAPI OpenAPI → `openapi-typescript`; WebSocket → AsyncAPI
- TS ↔ Rust: `utoipa` OpenAPI → `openapi-typescript`; WebSocket → AsyncAPI or `asyncapi-rust`
- IPC (Electron/Tauri): Specta or pydantic-to-typescript
- → [domains/contracts.md](domains/contracts.md)

### Dev

- JS/TS: Bun + oxfmt + oxlint; React Compiler on greenfield — no manual `useMemo`/`memo`
- Python: uv + Ruff + ty
- Rust: `cargo fmt` + `cargo clippy`
- Regenerate contract clients in CI; fail on spec drift
- → [domains/dev.md](domains/dev.md)

## Pick by domain

| Domain | Read |
|--------|------|
| User-facing SPA or mobile UI | [domains/frontend.md](domains/frontend.md) |
| SSR, server functions, or colocated TS UI+API | [domains/fullstack.md](domains/fullstack.md) |
| API, service, script, or data job | [domains/backend.md](domains/backend.md) |
| Desktop or native app | [domains/desktop.md](domains/desktop.md) |
| Imaging, numerics, or scientific compute | [domains/scientific.md](domains/scientific.md) |
| Lint, format, typecheck, CI, monorepo tasks | [domains/dev.md](domains/dev.md) |
| UI/service boundary or cross-language types | [domains/contracts.md](domains/contracts.md) |

## Compositions

- Desktop UI + local backend → [domains/desktop.md](domains/desktop.md) + [domains/backend.md](domains/backend.md) + [domains/contracts.md](domains/contracts.md)
- New repo setup → domain file(s) + [domains/dev.md](domains/dev.md)
