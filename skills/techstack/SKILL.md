---
name: techstack
description: >-
  Default stack guidance and greenfield scaffolds by product domain — frontend,
  backend, desktop, scientific, dev tooling, and cross-surface
  contracts. Covers React, Effect Atom, TanStack Router, Effect
  Platform on Bun, coss-ui, Tailwind v4, Vite+, Turborepo, Expo, NativeWind,
  React Native Reusables, FastAPI, Axum,
  Electron, uv/Ruff/ty, CMake/Qt/VTK/ITK, oxfmt/oxlint, Playwright, pymobiledevice3,
  and contracts (Effect Schema, OpenAPI, AsyncAPI). Includes starter layouts for Python Hatchling apps,
  Bun apps, TypeScript monorepos, Typer + FastAPI, QtPy MVP, and Rust crates. Use
  when choosing libraries, initializing project structure, comparing frameworks,
  refactoring stack, setting up lint/CI, hardening installs, or starting greenfield
  work.
---

# Techstack

## Global rules

- Effect is the TS domain/IO backbone — structured errors, DI, resource safety, retries — not ad hoc Promises
- One primary server stack per service
- Repo conventions beat greenfield defaults when they conflict
- Typed boundaries at every seam — decode/validate at runtime on untrusted input
- Greenfield installs: seven-day minimum registry release age — see [domains/security.md](domains/security.md)
- TS relative imports (all apps, packages, routes, components): always extensionless — `from './module'`, never `from './module.js'` or `from './module.ts'`
- Effect implementation depth in Effect repos → `effect-ts` skill

## Defaults at a glance

Enough to choose libraries and direction; read the linked file before implementing details.

### Frontend

- Web SPA: React + Vite+ (`vp`) + Bun runtime + Tailwind v4 + coss-ui + Effect Atom + TanStack Router
- Client IO in Effect programs; state in atoms — not raw `fetch` in components
- Mobile: React Native + Expo + NativeWind + React Native Reusables (+ Expo Router when file-based routing fits)
- Monorepo: Bun workspaces + Turborepo; `contracts` + `client` + `utils` + `ui-headless` + `ui` + `web-app`
- → [domains/frontend.md](domains/frontend.md)

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

### Agent

- Durable agentic apps on Vercel: Next.js + Eve (filesystem-first) + json-render (generative UI) + agent-browser + wterm/just-bash + Vercel Workflow/Sandbox/Drop
- Models via Ollama (OpenAI-compatible): `http://127.0.0.1:11434/v1` using AI SDK `openai` provider; cloud models as `{model}:cloud`
- Agent is a directory under `agent/`: instructions, tools, skills, channels, connections, sandbox, subagents, schedules
- Generative UI via constrained json-render catalogs
- Sandbox untrusted code; use `needsApproval` for high-impact actions
- Prototypes via Vercel Drop; move to git for iteration
- → [domains/agent.md](domains/agent.md)

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

- JS/TS: Bun + oxfmt + oxlint; React Compiler on greenfield — no manual `useMemo`/`useCallback`/`memo` (oxlint warns; see [domains/dev.md](domains/dev.md))
- Python: uv + Ruff + ty
- Rust: `cargo fmt` + `cargo clippy`
- Agent verification: Playwright (web), pymobiledevice3 (iOS) — debug and iterate on your own; don't ask the user to check
- Regenerate contract clients in CI; fail on spec drift
- Supply chain: seven-day registry release-age gating on greenfield — Bun `bunfig.toml`, uv `exclude-newer`, etc.
- → [domains/dev.md](domains/dev.md) + [domains/security.md](domains/security.md)

## Pick by domain

| Domain | Read |
|--------|------|
| User-facing SPA or mobile UI | [domains/frontend.md](domains/frontend.md) |
| API, service, script, or data job | [domains/backend.md](domains/backend.md) |
| Desktop or native app | [domains/desktop.md](domains/desktop.md) |
| Agentic apps (Eve + json-render + agent-browser + wterm + Vercel primitives; models via Ollama at 127.0.0.1:11434/v1 with AI SDK openai + `{model}:cloud`) | [domains/agent.md](domains/agent.md) |
| Imaging, numerics, or scientific compute | [domains/scientific.md](domains/scientific.md) |
| Lint, format, typecheck, CI, monorepo tasks | [domains/dev.md](domains/dev.md) |
| Supply chain, install hardening, release-age gating | [domains/security.md](domains/security.md) |
| UI/service boundary or cross-language types | [domains/contracts.md](domains/contracts.md) |

## Scaffolds

Create minimal starter structures. Do not add extra frameworks, folders, or docs unless the user asks.

- Keep names consistent with the requested project name
- Place scaffolds under the user-provided target directory
- Keep paths relative to the target root
- Start with minimal placeholders
- Prefer established package manager files over ad-hoc setup
- Avoid extra top-level folders
- Pick one variant; read its reference for trees and snippets before generating files
- Python Hatchling app → [scaffolds/python-app.md](scaffolds/python-app.md)
- TypeScript monorepo → [scaffolds/ts-monorepo.md](scaffolds/ts-monorepo.md)
- TypeScript Bun app → [scaffolds/ts-bun-app.md](scaffolds/ts-bun-app.md)
- Python Typer + FastAPI app → [scaffolds/python-typer-fastapi.md](scaffolds/python-typer-fastapi.md)
- QtPy MVP app → [scaffolds/qtpy-mvp.md](scaffolds/qtpy-mvp.md)
- Rust crate → [scaffolds/rust-crate.md](scaffolds/rust-crate.md)

## Compositions

- Desktop UI + local backend → [domains/desktop.md](domains/desktop.md) + [domains/backend.md](domains/backend.md) + [domains/contracts.md](domains/contracts.md)
- Full agentic stack (Next.js + Eve + json-render + agent-browser + wterm + Vercel + Ollama via AI SDK openai): [domains/agent.md](domains/agent.md)
- New repo setup → domain file(s) + [domains/dev.md](domains/dev.md) + [domains/security.md](domains/security.md) + scaffold when generating files
