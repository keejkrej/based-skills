# Frontend tooling

Build, style, and component-install workflow for web UIs. The architectural choices (React, Effect Atom, TanStack Router, package map) live in techstack `domains/frontend.md`.

## Toolchain

- Use Vite+ (`vp`) as the unified frontend toolchain
- Default commands and config → [dev.md](dev.md)
- Runtime/package manager: Bun underneath `vp`

## Styling

- Use Tailwind CSS v4
- Relative imports → always extensionless → see `domains/dev.md`

## Components

- Use coss-ui primitives as the default
- Add shadcn-style components only through `npx shadcn@latest add` when a coss equivalent is missing
- Concrete `npx shadcn` commands → [../SKILL.md](../SKILL.md)

## Monorepo tasks

- SPA frontend + Rust/Python backend: Vite+ tasks (`vp run`, `vp lint`, `vp build`, `vp test`, `vp check`) → `domains/dev.md`
- Next.js / fullstack Vercel: Turborepo task wiring (`build`, `test`, `lint`, `format`/`fmt`, `typecheck`) → `domains/dev.md`; scaffold with `npx create-turbo@latest` (flags → [../SKILL.md](../SKILL.md))

## Scaffolds

- Web monorepo → [../scaffolds/ts-monorepo.md](../scaffolds/ts-monorepo.md)
- Single Bun app → [../scaffolds/ts-bun-app.md](../scaffolds/ts-bun-app.md)

## Docs

- Vite+: https://viteplus.dev
- Tailwind CSS: https://tailwindcss.com/docs
- Vite: https://vite.dev/guide
- Bun: https://bun.sh/docs
- oxfmt: https://oxc.rs/docs/guide/usage/formatter
- oxlint: https://oxc.rs/docs/guide/usage/linter
