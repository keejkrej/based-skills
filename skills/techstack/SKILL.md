---
name: techstack
description: >-
  Default stack guidance by product goal — frontend, backend, desktop, scientific,
  dev tooling, and cross-surface contracts. Covers React, Effect Atom, TanStack
  Router, Effect Platform on Bun, coss-ui, Tailwind v4, Vite, Turborepo, Expo,
  FastAPI, Axum, Electron, uv/Ruff/ty, CMake/Qt/VTK/ITK, oxfmt/oxlint, and
  contracts (Effect Schema, OpenAPI, AsyncAPI). Use when choosing libraries,
  scaffolding apps, comparing frameworks, refactoring stack, setting up lint/CI,
  or starting greenfield work.
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

- Web: React + Vite + Bun + Tailwind v4 + coss-ui + Effect Atom + TanStack Router
- Client IO in Effect programs; state in atoms — not raw `fetch` in components
- Mobile: React Native + Expo (+ Expo Router when file-based routing fits)
- Monorepo: Bun workspaces + Turborepo; shared `domain` / `api` Effect packages
- → [frontend.md](frontend.md)

### Backend

- TS API: `@effect/platform` (`HttpApi` or `HttpRouter`) on Bun — not a separate router framework
- Python API/scripts: FastAPI + Pydantic v2 + uv
- Rust API: Axum + serde + utoipa
- WebSockets in Effect services: Effect Platform `Socket`
- Prototype in Python; harden/distribute in Rust
- → [backend.md](backend.md)

### Desktop

- UI-first: Electron bundling the same web stack
- Separate process: companion backend on `localhost` over WebSocket; preload/IPC only for OS APIs
- Native UI: egui (Rust tooling/sim) or Qt 6 (industrial widgets) — not Electron when native perf is the point
- → [desktop.md](desktop.md)

### Scientific

- C++ imaging: CMake + Qt 6 + VTK + ITK
- Rust numerics: `ndarray` ecosystem — not hand-rolled NumPy ports
- → [scientific.md](scientific.md)

### Contracts

- TS ↔ TS: shared Effect `Schema` + `HttpApi` in monorepo — no codegen between app and API
- TS ↔ Python: FastAPI OpenAPI → `openapi-typescript`; WebSocket → AsyncAPI
- TS ↔ Rust: `utoipa` OpenAPI → `openapi-typescript`; WebSocket → AsyncAPI or `asyncapi-rust`
- IPC (Electron/Tauri): Specta or pydantic-to-typescript
- → [contracts.md](contracts.md)

### Dev

- JS/TS: Bun + oxfmt + oxlint; React Compiler on greenfield — no manual `useMemo`/`memo`
- Python: uv + Ruff + ty
- Rust: `cargo fmt` + `cargo clippy`
- Regenerate contract clients in CI; fail on spec drift
- → [dev.md](dev.md)

## Pick by goal

| Goal | Read |
|------|------|
| User-facing UI (web or mobile) | [frontend.md](frontend.md) |
| API, service, script, or data job | [backend.md](backend.md) |
| Desktop or native app | [desktop.md](desktop.md) |
| Imaging, numerics, or scientific compute | [scientific.md](scientific.md) |
| Lint, format, typecheck, CI, monorepo tasks | [dev.md](dev.md) |
| UI/service boundary or cross-language types | [contracts.md](contracts.md) |

## Full-stack

- UI + API → defaults above + [contracts.md](contracts.md); detail in [frontend.md](frontend.md) + [backend.md](backend.md)
- Desktop UI + local backend → [desktop.md](desktop.md) + [backend.md](backend.md) + [contracts.md](contracts.md)
- New repo setup → goal file(s) + [dev.md](dev.md)
