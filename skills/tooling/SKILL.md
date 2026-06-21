---
name: tooling
description: >-
  Development workflow, verification, and scaffolding. Lint/format/typecheck,
  CI, agent verification, Vite+ frontend toolchain, greenfield scaffolds, and
  npx command reference. Use after `techstack` decides the product-domain stack.
---

# Tooling

How to develop, verify, and scaffold projects. Complements `techstack`, which decides **what** libraries and runtimes to use.

## Domains

- Development conventions (lint, format, typecheck, CI, package managers, supply-chain gating) → [domains/dev.md](domains/dev.md)
- Frontend build toolchain (Vite+, Tailwind, component install) → [domains/frontend.md](domains/frontend.md)
- Greenfield starter structures → [scaffolds/README.md](scaffolds/README.md)

## npx command reference

Quick invocations for agent-focused CLI tools. For strategy on when to use each, see the domains above.

### Global usage rule

- Run these tools via `npx`; do not install them globally unless a repo convention requires it

### npx @playwright/cli

Token-efficient Playwright CLI for coding agents. Also invokable as `npx playwright-cli`.

- Help / skills: `npx @playwright/cli --help`; `npx @playwright/cli install --skills`
- Open / navigate: `npx @playwright/cli open [url]`; `npx @playwright/cli goto <url>`
- Snapshot: `npx @playwright/cli snapshot` or `npx @playwright/cli snapshot --filename=f`
- Screenshot: `npx @playwright/cli screenshot [ref] --filename=f`
- Interact by ref or selector: `click <ref>`, `fill <ref> <text>`, `type <text>`, `press <key>`, `check <ref>`, `hover <ref>`, `select <ref> <value>`
- Dev probes: `npx @playwright/cli eval "() => document.title"`, `npx @playwright/cli console [min-level]`
- Tabs: `tab-list`, `tab-new [url]`, `tab-select <index>`, `tab-close [index]`
- Network: `route <pattern>`, `route-list`, `unroute [pattern]`, `requests`, `request <num>`
- Sessions: `-s=<name>` / `--session=<name>`; `list`; `close-all`; `--persistent` to keep profile on disk
- Browser: `--headed`, `--browser=chrome|firefox|webkit|msedge`
- Full test suites → main `playwright` package (`npx playwright test`) or Python `playwright`; deeper workflows → `webapp-testing` skill

### npx agent-browser

Browser automation CLI built for agents. Compact accessibility-tree snapshots with element refs (`@eN`).

- Install Chrome first run: `npx agent-browser install`
- Open a page: `npx agent-browser open example.com`
- Snapshot: `npx agent-browser snapshot -i`
- Click by ref: `npx agent-browser click @e2`
- Fill by ref: `npx agent-browser fill @e2 "text"`
- Screenshot: `npx agent-browser screenshot page.png`
- Close: `npx agent-browser close`
- Also ships as an MCP server → `https://agent-browser.dev`

### npx ctx7

Context7 CLI for up-to-date library docs.

- Help: `npx ctx7 --help`
- Discover library ID: `npx ctx7 library react "How to clean up useEffect"`
- Query by ID: `npx ctx7 docs /facebook/react "How to use hooks"`
- Versioned query: `npx ctx7 docs /vercel/next.js/v14.3.0-canary.87 "app router setup"`
- JSON output: append `--json`
- Setup: `npx ctx7 setup --mcp --claude` or `npx ctx7 setup --cli --claude`
- Auth: `npx ctx7 login` or `export CONTEXT7_API_KEY=...`
- Telemetry opt-out: `CTX7_TELEMETRY_DISABLED=1 npx ctx7 ...`
- Library IDs always start with `/`

### npx shadcn

shadcn/ui CLI. Adds copy-paste components into an existing project; can also scaffold a new project with `init`.

- Help: `npx shadcn@latest --help`
- Init in existing project: `npx shadcn@latest init`
- Scaffold new project: `npx shadcn@latest init --name my-app` (or `npx shadcn@latest create`, alias for `init`)
- Init with template and preset: `npx shadcn@latest init -t next --preset nova`
- Add components: `npx shadcn@latest add button card input label`
- Add all components: `npx shadcn@latest add --all`
- Add from a registry namespace: `npx shadcn@latest add @acme/button`
- Inspect before writing: `npx shadcn@latest add button --dry-run` / `--diff` / `--view`
- Common flags: `-y` (skip prompts), `-o` / `--overwrite`, `-p <path>` (target path), `-c <cwd>` (working dir)

### npx create-turbo

Turborepo scaffolding CLI.

- Scaffold interactively: `npx create-turbo@latest`
- Pick package manager: `npx create-turbo@latest -m bun`
- Use an example: `npx create-turbo@latest --example [name|github-url]`
- Example subdirectory: `npx create-turbo@latest --example [github-url] --example-path foo/bar`
- Skip install: `npx create-turbo@latest --skip-install`
- Skip code transforms: `npx create-turbo@latest --skip-transforms`
- Pin turbo version: `npx create-turbo@latest --turbo-version <version>`
