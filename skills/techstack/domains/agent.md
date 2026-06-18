# Agent

Durable, production-grade agentic applications on the Vercel ecosystem.

## When to use this domain

- Building agents as first-class products (durable sessions, tools, sandbox, subagents, human-in-the-loop).
- Agents need to safely generate UIs, control browsers, run shell commands, or surface on multiple channels.
- Rapid prototyping with AI-generated code followed by structured agent runtimes.
- Targeting the full Vercel AI stack: Eve + Workflow + Sandbox + Ollama (OpenAI-compatible at 127.0.0.1:11434) + Drop.

Not for: one-off scripts, pure frontend SPAs, or non-agent backend services (use frontend/backend domains).

## Core pieces

- **Next.js** — host app shell, API routes, and generative UI surfaces.
- **Eve** — filesystem-first durable agent framework. An agent is a directory:
  - `agent/instructions.md` (system prompt)
  - `agent/agent.ts` (runtime via `defineAgent`; model configured via AI SDK openai + Ollama below)
  - `agent/tools/*.ts` (typed actions with Zod schemas; filename = tool name)
  - `agent/skills/*.md` (on-demand procedures)
  - `agent/channels/*.ts` (HTTP, Slack, Discord, etc.)
  - `agent/connections/*.ts` (MCP servers, OAuth via Vercel Connect)
  - `agent/sandbox/` (isolated compute; swap adapters)
  - `agent/subagents/` (specialist child agents)
  - `agent/schedules/` (cron-driven durable runs)
- **json-render** — generative UI. Model emits constrained JSON specs; you render safely across platforms.
  - Catalog defines allowed components + actions.
  - Renderers: React, Next.js (`@json-render/next` for full apps), React Native, terminal (ink), PDF, email, 3D, etc.
  - Pre-built shadcn/ui catalog available.
- **agent-browser** — browser automation CLI for agents.
  - Snapshot with `@eN` refs (token-efficient accessibility tree).
  - Commands: `open`, `snapshot -i`, `click @e3`, `fill @e2 "text"`, `screenshot`, etc.
  - Also ships as MCP server.
- **wterm + just-bash** — web terminal + pure-TS in-browser Bash.
  - Useful for in-agent terminal UX and local sandbox fallbacks.
- **Vercel Workflow** — durability engine (checkpointed sessions, resume after cold starts/deploys). Baked into Eve.
- **Vercel Sandbox** — isolated Firecracker microVMs for untrusted code (prod default for Eve sandboxes).
- **Vercel Drop** — drag a folder or `.zip` (from Bolt.new, Claude Design, etc.) for instant production URLs. Creates a new project each time; great for prototypes.
- **zero-native** — native agent surfaces and runtimes (pair with React Native renderers or dedicated native agent hosts).
- **Ollama (OpenAI-compatible)** — local/cloud models via `http://127.0.0.1:11434/v1`. Use AI SDK `openai` provider with custom `baseURL`. Model names use `:cloud` suffix for cloud models (e.g. `llama3:cloud`, `{model}:cloud`).

## Architecture

- Eve agents deploy as ordinary Vercel projects (often colocated inside a Next.js app or as a standalone service).
- The `agent/` tree is the source of truth — no manual registration.
- Use json-render catalogs to let agents emit UIs without arbitrary code execution.
- Tools can orchestrate agent-browser, sandbox commands, external MCP connections, and subagents.
- Channels decouple "what the agent does" from "where it lives" (web chat, Slack, cron, API).
- Durability is default: every session is a Workflow. Human approvals, long-running tools, and pauses are first-class.

### Typical layout (agentic product)

```
apps/
  web/                 # Next.js shell + json-render renderers + chat UI
  agent/               # Eve agent tree (or lives under web/app/agent)
packages/
  ui/                  # json-render catalog + component registry
  contracts/           # shared schemas for tools/results
```

Eve sandboxes run untrusted/model-generated code in isolation. Keep privileged actions behind explicit tools with `needsApproval`.

## Local development

- `npx eve@latest init my-agent` (or add to existing Next.js app).
- `eve dev` for interactive terminal UI against the agent.
- `next dev` for the host UI.
- Swap sandbox backend locally: Docker, microsandbox, or just-bash.

## Production & deployment

- `vercel deploy` (standard git or CLI flow).
- Vercel Drop for one-shot prototypes (drag export → live URL).
- After Drop, connect a Git repo to the project for ongoing push-to-deploy.
- Observability: Agent Runs in Vercel dashboard (turns, tools, token usage). Export OpenTelemetry spans if needed.
- Evals: ship test suites with scoring; run on deploy or schedule.

## Rules

- One primary agent definition per Eve project (`agent/` tree).
- Keep agent capabilities as files, not runtime registration.
- Generative UI must go through a json-render catalog — never let the model emit raw React or arbitrary components.
- Sandbox everything untrusted. Use `needsApproval` for high-impact actions.
- Model access: use AI SDK `openai` provider pointing at Ollama (`baseURL: 'http://127.0.0.1:11434/v1'`); use `{model}:cloud` for Ollama cloud models.
- Channels and connections live in the agent tree; do not hard-code platform specifics inside tools.

## Compositions

- Full agentic app: Next.js (json-render UI) + Eve (durable brain) + agent-browser (web actions) + wterm/just-bash (terminal surface) + Vercel Drop for quick previews. Models via local Ollama + AI SDK openai compat.
- Multi-channel agent: single Eve tree + channels for web + Slack + cron.
- Generative dashboard: json-render catalog + Next.js renderer; agent emits specs, UI renders them.
- Code-writing agent: Eve + Sandbox + self-healing Workflow loop (generate → test → fix).

## Docs

- Eve: https://vercel.com/docs/eve and https://beta.eve.dev/docs
- json-render: https://json-render.dev
- agent-browser: https://agent-browser.dev
- wterm: https://wterm.dev
- Vercel Drop: https://vercel.com/docs/drop
- Vercel Workflow / Sandbox / Connect: see Eve concepts
- Ollama (OpenAI compat): https://ollama.com (local at 127.0.0.1:11434/v1)
- Vercel platform: https://vercel.com/docs
