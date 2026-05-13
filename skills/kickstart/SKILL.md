---
name: kickstart
description: Standardize project scaffolding with fixed starter layouts for Python Hatchling apps, Node apps, TypeScript monorepo apps, Typer CLIs, and Rust crates. Use when the request is to initialize a new project structure.
---

# Kickstart

## Overview

Use this skill when setting up new project structures with consistent conventions.

## Python App (Hatchling)

Use `src` layout with Hatchling, and keep package code isolated under `src/{name}`.

Suggested layout:

```text
{project-root}/
├─ pyproject.toml
└─ src/
   └─ {name}/
      └─ __init__.py
```

Minimal `pyproject.toml` sections:

```toml
[build-system]
requires = ["hatchling>=1.21.0"]
build-backend = "hatchling.build"

[project]
name = "{project-name}"
version = "0.1.0"
requires-python = ">=3.10"
dependencies = []
```

## Monorepo (TypeScript + cross-language workspace)

Use a monorepo root that keeps TS apps, shared packages, and cross-language workspaces. Shared libraries live under root `packages/`, not inside an app.

Suggested layout:

```text
{repo-root}/
├─ package.json
├─ apps/
│  ├─ web/
│  │  ├─ package.json
│  │  └─ src/
│  │     └─ components/
│  │        └─ ui/            # shadcn CLI primitives (default install path)
│  └─ server/
│     ├─ package.json
│     └─ src/
├─ packages/
│  ├─ contracts/package.json
│  └─ utils/package.json
├─ crates/
├─ zig/
└─ python/
```

In the **repo root** `package.json`, set workspaces to include apps and packages (for example `pnpm`: `"packages": ["apps/*", "packages/*"]`, or npm/yarn `workspaces: ["apps/*", "packages/*"]`).

**Apps:** Expect `apps/web` (frontend) and `apps/server` (API or backend service) as sibling workspaces. Only `apps/web` carries browser UI and shadcn output.

**UI location:** With a single web app (`apps/web`), that app owns its UI. The shadcn CLI installs component primitives under `apps/web/src/components/ui` by default; rename `web` if your app folder differs.

**`packages/ui`:** Add `packages/ui/package.json` only when **multiple web frontends** under `apps/` (for example two SPAs) must share the same UI layer. A `web` + `server` pair alone does not imply `packages/ui`.

Keep these package folders for now:
- `packages/contracts/package.json`
- `packages/utils/package.json`
- `crates/` as an empty Rust workspace root placeholder
- `zig/` as a root namespace for Zig packages or tooling (can be empty initially)
- `python/` as a root namespace for Python packages (can be empty initially)

## TypeScript Node App

Use a Node app written in TypeScript with entrypoint under `src/`.

Suggested layout:

```text
{project-root}/
├─ package.json
├─ tsconfig.json
└─ src/
   ├─ index.ts
   ├─ app.ts
   └─ config.ts
```

Minimal `package.json` sections:

```json
{
  "name": "{name}",
  "version": "0.1.0",
  "type": "module",
  "scripts": {
    "dev": "ts-node src/index.ts",
    "build": "tsc -p tsconfig.json",
    "start": "node dist/index.js",
    "test": "echo No tests configured"
  },
  "dependencies": {},
  "devDependencies": {
    "typescript": "^5.7.0",
    "@types/node": "^22.0.0",
    "ts-node": "^10.9.0"
  }
}
```

Minimal `tsconfig.json`:

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ES2022",
    "moduleResolution": "Bundler",
    "strict": true,
    "outDir": "dist",
    "rootDir": "src",
    "skipLibCheck": true
  },
  "include": ["src/**/*"]
}
```

Minimal content shape:
- `src/index.ts`: application bootstrap/entrypoint.
- `src/app.ts`: runtime wiring and service wiring.
- `src/config.ts`: typed config loading placeholder.

## Python Typer App (also in Python layout)

Typer projects still use the Python `src/{name}` convention.

Keep Typer commands split: one command loader and one command per file under `src/{name}`.

Suggested layout:

```text
{project-root}/
├─ pyproject.toml
└─ src/
   └─ {name}/
      ├─ main.py
      ├─ core.py
      ├─ __init__.py
      └─ commands/
         ├─ __init__.py
         └─ {name}.py
```

Minimal content shape:

- `pyproject.toml`: standard Hatchling/`[project]` metadata and `project.scripts` entry for command discovery.
- `core.py`: define `app = typer.Typer(...)`
- `src/{name}/main.py`: import command modules, then run `app()`
- `src/{name}/commands/{name}.py`: define command functions with `@app.command()`

## Rust App (single crate, no mod.rs)

Use a crate under `crates/{name}` with Rust module files in `foo.rs` + `foo/` directory form.

Suggested layout:

```text
crates/{name}/
├─ Cargo.toml
└─ src/
   ├─ main.rs
   ├─ lib.rs
   ├─ cli.rs
   └─ app.rs
```

Minimal `Cargo.toml` sections:

```toml
[package]
name = "{name}"
version = "0.1.0"
edition = "2021"

[dependencies]
```

Module rule:
- Split logic into `foo.rs` plus an optional `foo/` sibling directory for its submodules.
- Example:
  - `app.rs`
  - `app/config.rs`
  - `app/runner.rs`
- Declare modules in `main.rs` or `lib.rs` using:
  - `mod app;`
  - `mod cli;`
- Do not use `mod.rs` files.

## Additional Conventions

- Keep naming consistent with the requested `{name}` and avoid extra top-level folders.
- Default to minimal placeholders first; do not add extras unless the user requests them.
- If the user gives a target directory, place the scaffold under that root and keep paths relative to it.
