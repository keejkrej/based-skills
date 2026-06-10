# Dev tooling

Lint, format, typecheck, CI, and monorepo tasks.

## Defaults

- Follow repo conventions when already standardized (Poetry, mypy, ESLint, etc.)
- Greenfield JS/TS → Bun + oxfmt + oxlint
- Greenfield Python → uv + Ruff + ty
- Greenfield Rust → `cargo fmt` + `cargo clippy`
- Monorepo → Turborepo over Bun workspaces
- Registry release-age gating → `security` skill

## Research

- Clone library repos; read source, examples, and tests — do not rely on doc links alone

## TypeScript / JavaScript

- Package manager: Bun (`bun install`, `bun run`)
- Format: oxfmt
- Lint: oxlint
- React Compiler on greenfield apps: `babel-plugin-react-compiler` via `@vitejs/plugin-react`
- Enforce no manual memo: oxlint `react-hooks-js/use-memo`, `react-hooks-js/void-use-memo` (`eslint-plugin-react-hooks` as JS plugin)
- Skip manual `useMemo`, `useCallback`, and `memo` unless the compiler can't optimize a hot path
- Oxlint config in scaffolds → `template` skill `scaffolds/ts-monorepo.md`

## Monorepo (Turborepo)

- Put root `package.json`, `bun.lock`, and `turbo.json` at the root
- Use Turborepo for task graph, cache, and pipelines
- Keep package-local `package.json` scripts as the units Turborepo runs
- Wire `build`, `test`, `lint`, `format`/`fmt`, and `typecheck` with correct `dependsOn`, `inputs`, and `outputs`
- Use `turbo run <task>` from the root for cross-package work

## Python

- Project/lock/run: uv (`uv run`)
- Lint + format: `ruff check`, `ruff format`
- Types: `ty check`
- CI: run all three; fail on drift
- Escape hatch: Poetry, PDM, Hatch, pip-tools, conda, pip+venv, mypy, Pyright, or basedpyright when repo already uses them

## Rust

- Format: `cargo fmt`
- Lint: `cargo clippy`

## C++

- Build: CMake; prefer Ninja generator
- Use vcpkg manifest mode on greenfield dependency management
- Use Visual Studio or Xcode generators when the team workflow requires them

## Contracts in CI

- Regenerate cross-language clients from specs; fail on drift → [contracts.md](contracts.md)
- Regenerate `openapi.json` and TS client output for Python and Rust backends
- Optionally lint OpenAPI specs with Spectral

## Docs

- oxfmt: https://oxc.rs/docs/guide/usage/formatter
- oxlint: https://oxc.rs/docs/guide/usage/linter
- eslint-plugin-react-hooks: https://react.dev/reference/eslint-plugin-react-hooks
- Turborepo: https://turbo.build/repo/docs
- uv: https://docs.astral.sh/uv/
- Ruff: https://docs.astral.sh/ruff/
- ty: https://docs.astral.sh/ty/
- Ninja: https://ninja-build.org/manual.html
- vcpkg: https://learn.microsoft.com/en-us/vcpkg/
- OpenAPI: https://swagger.io/specification/
- AsyncAPI: https://www.asyncapi.com/docs
- openapi-typescript: https://openapi-ts.dev
- openapi-fetch: https://openapi-ts.dev/openapi-fetch/
- asyncapi-rust: https://github.com/mlilback/asyncapi-rust
