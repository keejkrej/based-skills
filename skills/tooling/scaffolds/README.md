# Scaffolds

Minimal starter structures for greenfield work. Read the specific scaffold before generating files.

## Rules

- Keep names consistent with the requested project name
- Place scaffolds under the user-provided target directory
- Keep paths relative to the target root
- Start with minimal placeholders
- Prefer established package manager files over ad-hoc setup
- Avoid extra top-level folders
- Do not add extra frameworks, folders, or docs unless the user asks
- Pick one variant; read its reference for trees and snippets before generating files

## Variants

- Python Hatchling app → [python-app.md](python-app.md)
- Python Typer + FastAPI app → [python-typer-fastapi.md](python-typer-fastapi.md)
- QtPy MVP app → [qtpy-mvp.md](qtpy-mvp.md)
- Rust crate → [rust-crate.md](rust-crate.md)
- TypeScript Bun app → [ts-bun-app.md](ts-bun-app.md)
- TypeScript monorepo → [ts-monorepo.md](ts-monorepo.md)

## Stack context

- Framework and runtime choices → techstack skill
- Lint/format/CI conventions → [../domains/dev.md](../domains/dev.md)
- Frontend build toolchain → [../domains/frontend.md](../domains/frontend.md)
- Component install → [../SKILL.md](../SKILL.md)
