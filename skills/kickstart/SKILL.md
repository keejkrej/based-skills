---
name: kickstart
description: Standardize project scaffolding with fixed starter layouts for Python Hatchling apps, Node apps, TypeScript monorepo apps, Typer CLIs, and Rust crates. Use when the request is to initialize a new project structure.
---

# Kickstart

Create minimal, conventional starter structures. Do not add extra frameworks, folders, or docs unless the user asks.

## Defaults

- Keep names consistent with the requested project name.
- Place scaffolds under the user-provided target directory.
- Keep paths relative to the target root.
- Start with minimal placeholders.
- Prefer established package manager files over ad-hoc setup.
- Avoid extra top-level folders.

## Python App

- Use `src/{name}` layout.
- Use Hatchling.
- Keep package behavior out of `__init__.py`.

```text
{project-root}/
├─ pyproject.toml
└─ src/
   └─ {name}/
      └─ __init__.py
```

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

## TypeScript Monorepo

- Use a root workspace for apps and shared packages.
- Put browser UI in `apps/web`.
- Put API/backend service code in `apps/server`.
- Put shared libraries under root `packages/`, not inside an app.
- Add `packages/ui` only when multiple web frontends must share one UI layer.
- Keep `crates/`, `zig/`, and `python/` as optional root namespaces when cross-language work is expected.
- Configure workspaces in the root package manager file (`pnpm-workspace.yaml`, npm/yarn `workspaces`, etc.).
- Install shadcn primitives into `apps/web/src/components/ui` unless the app folder differs.

```text
{repo-root}/
├─ package.json
├─ apps/
│  ├─ web/
│  │  ├─ package.json
│  │  └─ src/components/ui/
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

## TypeScript Node App

- Use ESM.
- Put the entrypoint under `src/index.ts`.
- Keep runtime wiring in `src/app.ts`.
- Keep config loading in `src/config.ts`.

```text
{project-root}/
├─ package.json
├─ tsconfig.json
└─ src/
   ├─ index.ts
   ├─ app.ts
   └─ config.ts
```

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

## Python Typer CLI

- Use the Python `src/{name}` layout.
- Define the Typer app in `core.py`.
- Keep command modules under `commands/`.
- Use `main.py` to import commands and call `app()`.
- Add a `[project.scripts]` entry in `pyproject.toml`.

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

## Rust Crate

- Put single crates under `crates/{name}`.
- Use `main.rs` for binary entry.
- Use `lib.rs` when reusable library code exists.
- Split behavior into `cli.rs`, `app.rs`, and focused modules as needed.
- Use `foo.rs` plus `foo/` child modules.
- Do not create `mod.rs`.

```text
crates/{name}/
├─ Cargo.toml
└─ src/
   ├─ main.rs
   ├─ lib.rs
   ├─ cli.rs
   └─ app.rs
```

```toml
[package]
name = "{name}"
version = "0.1.0"
edition = "2021"

[dependencies]
```

```text
src/
├─ app.rs
└─ app/
   ├─ config.rs
   └─ runner.rs
```
