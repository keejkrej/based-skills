# Modular — reference

## Review / self-check prompts

- What is the **one responsibility** of this file?
- If I deleted this file, would the **blast radius** be one feature or the whole system?
- Can I **test** the core logic without rendering, network, or filesystem?
- Does this file **name** still match what it does after my change?

## Line counts

Line count is a **weak** signal. Use it only with cohesion:

- **High line count + multiple concerns** → split.
- **High line count + one concern** (e.g. generated types, exhaustive switch) → may be acceptable; consider submodules if readability suffers.

## Framework notes (generic)

- **React**: Container vs presentational split when it reduces prop drilling without inventing useless layers. Prefer hooks for stateful logic reused across components.
- **Next.js**: Keep server-only code out of client components; isolation often **requires** separate files anyway—use that as the boundary.
- **Node services**: Route handler thin; validation, use-cases, and repositories in dedicated modules.

## Anti-patterns

- **Kitchen-sink context** holding unrelated state because "it's global".
- **Catch-all hooks** (`useEverything`) mirroring a god component.
- **Deep relative imports** across features (`../../../other-feature/...`) — fix with boundaries or explicit shared API, not by merging files.
