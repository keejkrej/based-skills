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

## Enforce in linters

Mirror stack conventions in linter config when a tool supports it. Keep skill-only guidance where no linter exists.

### TypeScript (oxlint + tsconfig)

- Extensionless relative imports → oxlint `import/extensions` with `js`/`jsx`/`ts`/`tsx`: `"never"` and `ignorePackages: true` → [../scaffolds/ts-monorepo.md](../scaffolds/ts-monorepo.md)
- Bundler module resolution → tsconfig `moduleResolution: "Bundler"` + `noEmit: true` (typecheck config, not oxlint)
- No manual memo on React web apps (soft lint) → oxlint `no-restricted-imports` on `useMemo`/`useCallback`/`memo`; react-hooks-js `use-memo`, `void-use-memo`, `preserve-manual-memoization` — all `warn` on `**/*.{tsx,jsx}`
- Effect/Atom/TanStack patterns, typed boundaries at runtime → skill only

### Python (Ruff + ty)

- Absolute package imports over parent-relative imports → Ruff `TID252` + `[tool.ruff.lint.flake8-tidy-imports] ban-relative-imports = "parents"`
- Style, imports, pyupgrade, bugbear → Ruff `select = ["E", "F", "I", "UP", "B", "TID252"]`
- Types on public APIs and call sites → `ty check` with `[tool.ty.rules] all = "error"`
- Typer/FastAPI layer layout, Hatchling src layout → skill only

### Rust (clippy + fmt)

- Idiomatic Rust → `[lints.clippy] all = "warn"` (or `"deny"` in CI) in `Cargo.toml`
- `foo.rs` + `foo/` child modules, not `mod.rs` → skill only; optional CI: fail if `src/**/mod.rs` exists
- Axum/serde/utoipa handler patterns → skill only

### Skill-only (no practical linter)

- Effect as TS domain/IO backbone
- One primary server stack per service
- Contract regen and drift checks in CI (Spectral/OpenAPI lint is partial)
- coss-ui composition, Electron transport choices, scientific CMake layout

## TypeScript / JavaScript

- Applies to all TS/TSX — web, server, shared packages, TanStack Start routes
- Relative imports to local modules: always extensionless — `from './module'`, `from '../path/to/module'`
- Never put a file extension on relative import paths — not `.js`, `.jsx`, `.ts`, or `.tsx`
- Cross-package imports: package name (`from '@repo/domain'`), not relative paths with extensions
- Greenfield apps: `moduleResolution: "Bundler"` + `noEmit: true` — not `"NodeNext"` unless the repo already standardizes on it
- Packages that must emit: prefer source exports in `package.json` (`"exports": { ".": "./src/index.ts" }`); keep import statements extensionless
- Package manager: Bun (`bun install`, `bun run`)
- Format: oxfmt
- Lint: oxlint
- Enforce extensionless imports: oxlint `import/extensions` — see **Enforce in linters** above
- React Compiler on greenfield apps: `babel-plugin-react-compiler` via `@vitejs/plugin-react`
- Soft-enforce no manual memo on TSX/JSX: `warn` on `no-restricted-imports` for `useMemo`/`useCallback`/`memo` from `react`; `warn` on react-hooks-js `use-memo`, `void-use-memo`, `preserve-manual-memoization` (`eslint-plugin-react-hooks` as JS plugin) — warnings surface in `oxlint` output without failing CI by default
- Skip manual `useMemo`, `useCallback`, and `memo` unless the compiler can't optimize a hot path; suppress with `// oxlint-disable-next-line` only for documented hot-path escapes
- Oxlint config in scaffolds → [../scaffolds/ts-monorepo.md](../scaffolds/ts-monorepo.md), [../scaffolds/ts-bun-app.md](../scaffolds/ts-bun-app.md)

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
- Enforce import and style rules in `pyproject.toml` — see **Enforce in linters** above; scaffolds → [../scaffolds/python-app.md](../scaffolds/python-app.md), [../scaffolds/python-typer-fastapi.md](../scaffolds/python-typer-fastapi.md)
- CI: run all three; fail on drift
- Escape hatch: Poetry, PDM, Hatch, pip-tools, conda, pip+venv, mypy, Pyright, or basedpyright when repo already uses them

## Rust

- Format: `cargo fmt`
- Lint: `cargo clippy`
- Enforce clippy in `Cargo.toml` `[lints.clippy]` — see **Enforce in linters** above; scaffold → [../scaffolds/rust-crate.md](../scaffolds/rust-crate.md)

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
