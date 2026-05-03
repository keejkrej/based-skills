---
name: modular
description: >-
  Keeps code organized into focused modules and prevents god files (single files
  that mix unrelated concerns, types, UI, data access, and glue). Use when
  designing or refactoring file layout, splitting large components, reviewing
  structure, or when the user mentions god objects, giant files, monolith
  modules, cohesion, boundaries, or "where should this live?"
---

# Modular (no god files)

## Goal

Changes should land in **small, cohesive units** with obvious homes. Prefer many clear files over one warehouse file.

## Before adding or extending code

1. **Single reason to change**: Would two unrelated features both edit this same file? If yes, introduce a boundary (subfolder, service, hook, adapter) instead of growing the god file.
2. **Naming matches responsibility**: File name reflects what it owns; avoid `utils.ts`, `helpers.ts`, or `stuff.ts` as dumping grounds unless scope is narrowly defined (e.g. `date-utils.ts` only date formatting).
3. **Layers stay thin at the top**: Routes/pages/containers orchestrate; they do not own every type, transformer, and side effect inline.
4. **Imports tell a story**: If a module imports widely unrelated domains, split by domain or by layer (presentation vs logic vs IO).

## God file warning signs

Treat as a refactoring cue (not shame):

- Mixed **UI + domain rules + persistence + ad-hoc I/O** in one file.
- Hard to describe the file in **one sentence** without "and also".
- **Everyone** edits the same file for unrelated tickets; frequent merge conflicts.
- **Circular or grab-bag barrels** (`index.ts` re-exporting unrelated subsystems).
- File length is a symptom only: a 200-line cohesive module can be fine; 200 lines of five unrelated behaviors is not.

## How to split (default playbook)

Pick the smallest extraction that restores cohesion:

| Symptom | Typical extraction |
|--------|---------------------|
| Big React/Vue/Svelte surface | Subcomponents, hooks/composables, view-model module |
| Types + runtime mixed | `types.ts` or colocated `*.types.ts` next to owner |
| API/client + parsing | `client.ts`, `mappers.ts`, keep components dumb |
| Shared constants across features | Feature-local constants first; promote only with clear shared scope |
| Test-only complexity | Colocate `*.test.*` or `__tests__` next to subject |

**Colocation**: Keep things **next to** the feature that owns them. Promote to `shared/` or `lib/` only when a **second** real consumer exists or the contract is stable.

## Rules of thumb (override with project conventions)

- **New concern** → new file or subfolder under the owning feature, not a new section at the bottom of a megaclass.
- **Barrels**: export a **coherent slice**; do not re-export the whole app from one `index.ts`.
- **Refactors**: move code **without behavior change** first; then split; keep PRs reviewable.
- **Do not** solve structure by endless `// SECTION` comments in a 2k-line file—split instead.

## When the user asks "where should this go?"

Answer with: **owner feature → layer (ui / domain / data) → file name**. If no owner exists yet, create a minimal folder for that feature rather than defaulting to the nearest big file.

## Additional detail

For extended heuristics and review prompts, see [reference.md](reference.md).
