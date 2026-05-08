---
name: techstack
description: >-
  Preferred default frontend: React with TanStack Query, TanStack Router,
  Effect, Zustand, coss-ui, Tailwind v4, Vite, and pnpm; JS/TS monorepos use
  pnpm workspaces plus Turborepo; preferred HTTP APIs on
  Hono (TypeScript), FastAPI when the backend is Python-first, Axum for Rust
  production services. Maps UI-first desktop to Tauri or Electron by where logic
  belongs; prefers WebSockets over other sockets and over heavy Electron/Tauri
  IPC when splitting UI and backend for separate debugging; prefers egui/Qt6
  when performance drives the product; reserves PySide6 for Qt/Python migrations;
  favors Python headless prototypes and Rust for shipped production binaries.
  Use when choosing libraries, scaffolding apps, refactoring stack, comparing
  frameworks, desktop vs web, or starting new projects.
---

# Techstack defaults

Assume this stack unless the repo or user explicitly contradicts it.

## Web and UI-heavy clients

Default **UI framework** is [**React**](https://react.dev/learn) (TanStack libs below assume React unless the repo standardizes on Solid or another supported adapter).

| Layer | Choice |
|-------|--------|
| UI framework | [**React**](https://react.dev/learn) |
| Data / server state | [**TanStack Query**](https://tanstack.com/query/latest/docs) |
| Routing | [**TanStack Router**](https://tanstack.com/router/latest/docs) |
| Typed app layer (effects, services, schemas, errors) | [**Effect**](https://effect.website/docs) |
| Ephemeral UI-centric client state | [**Zustand**](https://docs.pmnd.rs/zustand/getting-started/introduction) |
| Components | [**coss-ui**](https://coss.com/ui/docs) primitives (dialogs, selects, menus, forms, tabs, inputs, toasts, etc.—see project coss skill if present) |
| Styling | [**Tailwind CSS v4**](https://tailwindcss.com/docs) |
| Bundler | [**Vite**](https://vite.dev/guide/) |
| Package manager | [**pnpm**](https://pnpm.io/) (install, scripts, tooling—follow existing repo lockfiles if pinned to npm/yarn/bun) |
| Monorepo (workspaces + task graph, cache, pipelines) | [**Turborepo**](https://turbo.build/repo/docs) on top of **pnpm `workspaces`**

For **multiple packages or apps** in one repository (shared UI, BFF + web, design system + apps): define **`pnpm-workspace.yaml`**, put **`turbo.json`** at the repo root, and wire **`tasks`** so `build`, `test`, `lint`, and `typecheck` declare **`dependsOn`** and **`outputs`** where applicable so remote cache and CI stay correct (older monorepos may still use **`pipeline`**). Prefer **`turbo run <task>`** from the root for cross-package scripts; keep package-local **`package.json` `scripts`** as the unit of work Turborepo orchestrates.

Use **Effect** for reusable domain logic, dependency injection, parsing/validation, structured errors, retries, and concurrency; keep **Zustand** for local UI state stores; keep **TanStack Query** as the cache and sync layer for remote data.

Compose new frontend work around these picks; migrate toward them gradually on brownfield repos.

## HTTP / API backends

Pick one primary server stack per service; mix only when boundaries (e.g. BFF + worker) justify it.

| Situation | Choice |
|-----------|--------|
| Same **TypeScript/JavaScript** world as the React/Vite stack; **Node.js** or edge runtimes; lightweight HTTP, RPC-style routes, edge-friendly habits | [**Hono**](https://hono.dev/docs) |
| **Python-first** APIs—rapid iteration, data/ML adjacency, extending headless Python tooling into HTTP | [**FastAPI**](https://fastapi.tiangolo.com/) |
| **Rust** services where production hardening, throughput, or binary deployment matches the “Rust for production” goal | [**Axum**](https://docs.rs/axum/latest/axum/) |

Implement **WebSocket** endpoints on whichever stack you choose (Node `http`/`https`, FastAPI websockets, Axum `upgrade`, Hono with a compatible runtime)—the **transport preference** in the next section is independent of framework.

## Desktop shell (product is primarily a GUI)

Pick **either** [**Tauri**](https://v2.tauri.app/start/) **or** [**Electron**](https://www.electronjs.org/docs/latest/)—never default blindly; split on **where the non-UI work should live**:

| Prefer **Electron** when | Prefer **Tauri** when |
|--------------------------|------------------------|
| Most domain logic fits **TypeScript** cheaply—CRUD, orchestration, file I/O through Node-ish APIs | Logic is **CPU-heavy**, memory-tight, or benefits from **Rust** crates and native crates |
| Team wants maximum **npm/JS ecosystem** and fastest iteration matching the Vite SPA stack above | Goal is **smaller binaries**, tighter **sandbox**, or shipping **Rust** as the system layer with a thin web UI |
| Prototyping and “web app wrapped as desktop” is enough | FFI, audio/video pipelines, cryptography, parsers, simulation, or other work that fights JS |

Hybrid is fine only when justified: expose small native bridges in Electron or call into sidecar processes—but **prefer Tauri when Rust should own core logic.**

Bundle the **same SPA stack** (React, TanStack Query, TanStack Router, Effect, Zustand, coss-ui, Tailwind v4, Vite) inside the shell unless the codebase already dictates otherwise.

## UI ↔ backend transport (debuggable split)

Prefer **WebSockets** as the default **bidirectional** channel between frontend and companion backend over ad-hoc **raw TCP**, custom framing, UNIX sockets with one-off protocols, or similar lower-level sockets—unless throughput/latency requirements or existing standards dictate otherwise.

In **Electron** and **Tauri**, favor that **same WebSocket** path (backend listens on **`127.0.0.1` / `localhost`** with **`ws://`** or **`wss://`**; the embedded web UI connects like any browser tab) instead of leaning on **Electron IPC** (`contextBridge`, `ipcRenderer`, `ipcMain`) or **Tauri IPC** (`invoke`, event plugins) **for the primary application conversation**. Reasons:

- The **backend** runs as its **own process** with normal debuggers, logging, CLI flags, integration tests without the desktop shell.
- The **frontend** can be exercised against a **standalone** backend (or mocks) inside Vite during development.
- Narrower blast radius—UI and server contracts stay framed as networked messages rather than brittle main-world glue.

Reserve **thin IPC or native bridges** only for OS capabilities the webview alone cannot expose (chosen files, privileged hooks, tightly scoped synchronous handshakes). Keep those surfaces **minimal**—business logic stays on the WS-accessible backend.

Security: bind to localhost by default for local apps; add auth or token pairing if exposure beyond the machine becomes possible.

## Native UI when **logic / performance leads**, UI follows

Prefer **immediate-mode Rust UI** vs **traditional widget toolkits**:

| Situation | Direction |
|-----------|-----------|
| Rust is already the primary language; fast iteration tools/demos/internal panels; tight coupling to Rust game/sim-like loops | [**egui**](https://docs.rs/egui/latest/egui/) (or similar Rust-first GUIs)—crate docs are the reference; keep stacks small |
| Mature widgets, accessibility depth, OEM/platform integration expectations, designers used to Qt toolchain | [**Qt 6**](https://doc.qt.io/qt-6/) (C++/Rust/other bindings) |

Avoid bolting Electron/Tauri solely for visuals when native performance—not web UX—is why the project exists.

## Python + Qt caveat

Use [**PySide6**](https://doc.qt.io/qtforpython-6/) **only when** adapting or extending an **existing QtPy / PyQt / PySide-style** codebase. For new Python/Qt work without that legacy constraint, reassess Qt6 bindings or whether the UI should migrate to Rust or web per sections above—not an automatic PySide6 default.

## Headless tooling and prototyping

For **rapid development** scripts, CLIs, data jobs, scraping, notebooks, orchestration prototypes: [**Python**](https://docs.python.org/3/) first unless the repo is already Rust- or Node-centric.

Treat these as disposable or transitional—structure so core algorithms can migrate.

## Production and distribution targets

Ship **maintained binaries, services, installers, CDN assets, crates, or reproducible deployments** targeting [**Rust**](https://doc.rust-lang.org/) when quality, distribution, longevity, or performance dominate the brief.

Do **not** replace a working Rust production path with Python for marginal convenience unless the user clearly wants that tradeoff.

## Official documentation

| Tech | Documentation |
|------|----------------|
| React | https://react.dev/learn |
| TanStack Query | https://tanstack.com/query/latest/docs |
| TanStack Router | https://tanstack.com/router/latest/docs |
| Effect | https://effect.website/docs |
| Zustand | https://docs.pmnd.rs/zustand/getting-started/introduction |
| coss-ui | https://coss.com/ui/docs |
| Tailwind CSS | https://tailwindcss.com/docs |
| Vite | https://vite.dev/guide |
| pnpm | https://pnpm.io/ |
| Turborepo | https://turbo.build/repo/docs |
| Hono | https://hono.dev/docs |
| FastAPI | https://fastapi.tiangolo.com |
| Axum | https://docs.rs/axum/latest/axum |
| Tauri | https://v2.tauri.app/start |
| Electron | https://www.electronjs.org/docs/latest |
| WebSockets (browser) | https://developer.mozilla.org/en-US/docs/Web/API/WebSocket |
| egui | https://docs.rs/egui/latest/egui |
| Qt 6 | https://doc.qt.io/qt-6 |
| PySide6 / Qt for Python | https://doc.qt.io/qtforpython-6 |
| Python | https://docs.python.org/3 |
| Rust | https://doc.rust-lang.org |

## Summary decision flow

```text
New UI product with web ergonomics → React + Vite + pnpm + Turborepo (if monorepo)
  + Tailwind v4 + coss-ui + TanStack Query + TanStack Router + Effect + Zustand
API layer → Hono (TS default) / FastAPI (Python-first) / Axum (Rust production)
Needs desktop framing → Electron (TS-heavy) vs Tauri (Rust-heavy / slim / native)
UI + separate backend/debuggable core → localhost WebSocket primary channel; IPC only thin OS bridges
Perf-first GUI, Rust core → egui; industrial Qt needs → Qt 6
Qt-in-Python continuity only → PySide6 with legacy Qt codebase
Hack fast → Python headless; ship hard → Rust
```
