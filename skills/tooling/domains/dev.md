# Dev tooling

Lint, format, typecheck, CI, and monorepo tasks.

## Defaults

- Follow repo conventions when already standardized (Poetry, mypy, ESLint, etc.)
- Greenfield JS/TS → Vite+ (`vp`) — single entry point for install/dev/build/test/lint/fmt/pack/run; Bun remains the runtime/package manager underneath
- Greenfield Python → uv + Ruff + ty
- Greenfield Rust → `cargo fmt` + `cargo clippy`
- Monorepo → Turborepo over Bun workspaces
- Registry release-age gating on greenfield → techstack `domains/security.md` (`bunfig.toml`, `.npmrc`, uv `exclude-newer`)

## Research

- Clone library repos; read source, examples, and tests — do not rely on doc links alone

## Agent verification

Verify your own work — start services, run tools, capture evidence; do not ask the user to eyeball fixes.

- Loop: reproduce → observe (snapshot, log, screenshot) → hypothesis → change → re-run until green
- Runtime evidence beats static reasoning — a change that "looks right" still needs a verification pass
- Start dev servers yourself when needed; wait for app-ready signals (`networkidle`, health check, Metro ready) before asserting

### Web (Playwright)

- Default recon tool for local web UIs — navigate, interact, inspect rendered state
- Quick pass: `npx @playwright/cli` — `open`, `goto`, `snapshot`, `screenshot`, `eval` for DOM/console probes
- Concrete command reference → [../SKILL.md](../SKILL.md)
- Scripted checks: Python `playwright` against `localhost` — especially when the repo already uses uv
- Reconnaissance-then-action: snapshot or screenshot first, then click/fill using discovered refs or selectors
- Deeper workflows → `webapp-testing` skill; stuck regressions → `diagnose` skill

### iOS (pymobiledevice3)

- Default tool for physical iOS devices and simulators — logs, installs, screenshots, device state
- Install: `uv add pymobiledevice3` (or use repo's existing Python env)
- Common passes: `syslog` (stream logs), `apps list` / `install`, `developer dvt screenshot`, `usbmux list`
- USB device must be trusted and paired; simulator uses its own socket — pick the right target before iterating
- React Native / Expo: combine Metro/Expo output with `pymobiledevice3 syslog` for native-layer failures

## Enforce in linters

Mirror stack conventions in linter config when a tool supports it. Keep skill-only guidance where no linter exists.

### TypeScript (oxlint + tsconfig)

- Extensionless relative imports → oxlint `import/extensions` with `js`/`jsx`/`ts`/`tsx`: `"never"` and `ignorePackages: true` → [../scaffolds/ts-monorepo.md](../scaffolds/ts-monorepo.md)
- Bundler module resolution → tsconfig `moduleResolution: "Bundler"` + `noEmit: true` (typecheck config, not oxlint)
- No manual memo on React web apps → techstack discourages `useMemo`/`useCallback`/`memo`; oxlint `warn` on `**/*.{tsx,jsx}` via `no-restricted-imports` + react-hooks-js `use-memo`, `void-use-memo`, `preserve-manual-memoization`
- Effect/Atom/TanStack patterns, typed runtime boundaries → techstack skill; not a linter rule

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

- Contract regen and drift checks in CI (Spectral/OpenAPI lint is partial)
- Stack-architecture conventions (Effect, one server stack, coss-ui, Electron, scientific CMake) → techstack skill

## TypeScript / JavaScript

- Applies to all TS/TSX — web, server routes / API surfaces, shared packages
- Relative imports to local modules: always extensionless — `from './module'`, `from '../path/to/module'`
- Never put a file extension on relative import paths — not `.js`, `.jsx`, `.ts`, or `.tsx`
- Cross-package imports: package name (`from '@repo/domain'`), not relative paths with extensions
- Greenfield apps: `moduleResolution: "Bundler"` + `noEmit: true` — not `"NodeNext"` unless the repo already standardizes on it
- Packages that must emit: prefer source exports in `package.json` (`"exports": { ".": "./src/index.ts" }`); keep import statements extensionless
- Runtime/package manager: Bun (used via `vp`, not directly)
- Unified toolchain: Vite+ (`vp`) — `vp install`, `vp dev`, `vp build`, `vp test`, `vp check`, `vp run`, `vp pack`; do not invoke npm/pnpm/Yarn/Bun directly for these tasks
- Format: `vp fmt` (Oxfmt under the hood)
- Lint: `vp lint` (Oxlint under the hood)
- Keep tool config in `vite.config.ts`; `vp migrate` merges `.oxlintrc*`, `.oxfmtrc*`, and lint-staged config
- Enforce extensionless imports: oxlint `import/extensions` — see **Enforce in linters** above
- React Compiler on greenfield apps: `babel-plugin-react-compiler` via `@vitejs/plugin-react`
- Do not use manual `useMemo`, `useCallback`, or `memo` on greenfield React — rely on React Compiler; escape hatch only when the compiler can't optimize a hot path
- Oxlint warns (not errors) on TSX/JSX: `no-restricted-imports` for `useMemo`/`useCallback`/`memo` from `react`; react-hooks-js `use-memo`, `void-use-memo`, `preserve-manual-memoization` (`eslint-plugin-react-hooks` as JS plugin) — surfaces in `oxlint` output without failing CI by default
- Suppress with `// oxlint-disable-next-line` only for documented hot-path escapes
- Oxlint config in scaffolds → [../scaffolds/ts-monorepo.md](../scaffolds/ts-monorepo.md), [../scaffolds/ts-bun-app.md](../scaffolds/ts-bun-app.md)

## Monorepo (Turborepo)

- Put root `package.json`, `bun.lock`, and `turbo.json` at the root
- Use Turborepo for task graph, cache, and pipelines; drive tasks with `vp run` (or `turbo run` when explicit)
- Pin shared dependency versions with a Bun workspace **catalog** in root `package.json`
- Keep package-local `package.json` scripts as the units Turborepo runs
- Wire `build`, `test`, `lint`, `format`/`fmt`, and `typecheck` with correct `dependsOn`, `inputs`, and `outputs`
- Use `turbo run <task>` from the root for cross-package work
- Create a new monorepo: `npx create-turbo@latest`; concrete flags → [../SKILL.md](../SKILL.md)
- Package layout for web monorepos → techstack `domains/frontend.md` + [../scaffolds/ts-monorepo.md](../scaffolds/ts-monorepo.md)
- Durable agent notes → `docs/agents/` (memory skill)

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

## Supply chain

- Greenfield JS/TS (Bun) → root `bunfig.toml` with `minimumReleaseAge = 604800`
- Greenfield Python (uv) → `[tool.uv] exclude-newer = "7 days"` in `pyproject.toml`
- npm/pnpm repos → see [../../techstack/domains/security.md](../../techstack/domains/security.md)
- Apply on scaffold/bootstrap — do not defer to a later security pass

## Contracts in CI

- Regenerate cross-language clients from specs; fail on drift → techstack `domains/contracts.md`
- Regenerate `openapi.json` and TS client output for Python and Rust backends
- Optionally lint OpenAPI specs with Spectral

## Docs

- Playwright: https://playwright.dev/docs/intro
- pymobiledevice3: https://github.com/doronz88/pymobiledevice3
- oxfmt: https://oxc.rs/docs/guide/usage/formatter
- oxlint: https://oxc.rs/docs/guide/usage/linter
- eslint-plugin-react-hooks: https://react.dev/reference/eslint-plugin-react-hooks
- Vite+: https://viteplus.dev
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
