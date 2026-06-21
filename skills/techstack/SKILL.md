---
name: techstack
description: >-
  Default product-domain stack guidance — frontend, backend, desktop, agent,
  scientific, and cross-surface contracts. Covers React, Effect Atom, TanStack
  Router, Effect Platform on Bun, coss-ui, Tailwind v4, Turborepo, Expo,
  NativeWind, React Native Reusables, FastAPI, Axum, Electron, CMake/Qt/VTK/ITK,
  and contracts (Effect Schema, OpenAPI, AsyncAPI). Use when choosing libraries,
  runtimes, architecture, refactoring stack, or comparing frameworks.
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

- Web SPA: React + Bun + Tailwind v4 + coss-ui + Effect Atom + TanStack Router
- Client IO in Effect programs; state in atoms — not raw `fetch` in components
- Mobile: React Native + Expo + NativeWind + React Native Reusables (+ Expo Router when file-based routing fits)
- Monorepo: Bun workspaces + Turborepo; `contracts` + `client` + `utils` + `ui-headless` + `ui` + `web-app`
- Build toolchain, lint/format, component install → `tooling` skill
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

## Pick by domain

| Domain | Read |
|--------|------|
| User-facing SPA or mobile UI | [domains/frontend.md](domains/frontend.md) |
| API, service, script, or data job | [domains/backend.md](domains/backend.md) |
| Desktop or native app | [domains/desktop.md](domains/desktop.md) |
| Agentic apps (Eve + json-render + agent-browser + wterm + Vercel primitives; models via Ollama at 127.0.0.1:11434/v1 with AI SDK openai + `{model}:cloud`) | [domains/agent.md](domains/agent.md) |
| Imaging, numerics, or scientific compute | [domains/scientific.md](domains/scientific.md) |
| UI/service boundary or cross-language types | [domains/contracts.md](domains/contracts.md) |
| Supply chain, install hardening, release-age gating | [domains/security.md](domains/security.md) |
| Development tooling, lint/format, CI, scaffolds | `tooling` skill |

## Scaffolds

Greenfield starter structures live in `tooling/scaffolds/`. Read `tooling/scaffolds/README.md` before generating files.

## Compositions

- Desktop UI + local backend → [domains/desktop.md](domains/desktop.md) + [domains/backend.md](domains/backend.md) + [domains/contracts.md](domains/contracts.md)
- Full agentic stack (Next.js + Eve + json-render + agent-browser + wterm + Vercel + Ollama via AI SDK openai): [domains/agent.md](domains/agent.md)
- New repo setup → domain file(s) + `tooling` skill (`tooling/domains/dev.md`, `tooling/scaffolds/`) + [domains/security.md](domains/security.md)
