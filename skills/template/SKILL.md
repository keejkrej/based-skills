---
name: template
description: Standardize project scaffolding with fixed starter layouts for Python Hatchling apps, Bun apps, TypeScript monorepo apps, Typer + FastAPI apps, QtPy MVP apps, and Rust crates. Use when the request is to initialize a new project structure or copy scaffold templates.
---

# Template

Create minimal, conventional starter structures. Do not add extra frameworks, folders, or docs unless the user asks.

## Defaults

- Keep names consistent with the requested project name.
- Place scaffolds under the user-provided target directory.
- Keep paths relative to the target root.
- Start with minimal placeholders.
- Prefer established package manager files over ad-hoc setup.
- Avoid extra top-level folders.

## Scaffolds

- Pick one variant; read its reference for trees and snippets before generating files.
- Python Hatchling app → [scaffolds/python-app.md](scaffolds/python-app.md)
- TypeScript monorepo → [scaffolds/ts-monorepo.md](scaffolds/ts-monorepo.md)
- TypeScript Bun app → [scaffolds/ts-bun-app.md](scaffolds/ts-bun-app.md)
- Python Typer + FastAPI app → [scaffolds/python-typer-fastapi.md](scaffolds/python-typer-fastapi.md)
- QtPy MVP app → [scaffolds/qtpy-mvp.md](scaffolds/qtpy-mvp.md)
- Rust crate → [scaffolds/rust-crate.md](scaffolds/rust-crate.md)
