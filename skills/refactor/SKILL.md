---
name: refactor
description: >-
  Guides structural refactors: cohesive modules (no god files), deduplication,
  consistent wiring for routes/CLIs/pages, preference for registry-based UI
  components (e.g. shadcn-style install/copy) over hand-rolled controls, and
  language-specific layout (Rust non–mod.rs, empty Python `__init__.py`). Use
  when designing file layout, splitting large files, removing duplication, Rust
  crate structure, Python package layout, code review, UI refactors, or when
  the user mentions cohesion, boundaries, mod.rs, `__init__.py`, god files,
  shadcn, component registries, or "where should this live?"
---

# Refactor

## Language-agnostic

Principles that apply regardless of stack.

### Modular (no god files)

Changes should land in **small, cohesive units** with obvious homes. Prefer many clear files over one warehouse file.

#### Before adding or extending code

1. **Single reason to change**: Would two unrelated features both edit this same file? If yes, introduce a boundary (subfolder, service, hook, adapter) instead of growing the god file.
2. **Naming matches responsibility**: File name reflects what it owns; avoid `utils.ts`, `helpers.ts`, or `stuff.ts` as dumping grounds unless scope is narrowly defined (e.g. `date-utils.ts` only date formatting).
3. **Layers stay thin at the top**: Routes/pages/containers orchestrate; they do not own every type, transformer, and side effect inline.
4. **Imports tell a story**: If a module imports widely unrelated domains, split by domain or by layer (presentation vs logic vs IO).

#### God file warning signs

Treat as a refactoring cue (not shame):

- Mixed **UI + domain rules + persistence + ad-hoc I/O** in one file.
- Hard to describe the file in **one sentence** without "and also".
- **Everyone** edits the same file for unrelated tickets; frequent merge conflicts.
- **Circular or grab-bag barrels** (`index.ts` re-exporting unrelated subsystems).
- File length is a symptom only: a 200-line cohesive module can be fine; 200 lines of five unrelated behaviors is not.

#### How to split (default playbook)

Pick the smallest extraction that restores cohesion:

| Symptom | Typical extraction |
|--------|---------------------|
| Big React/Vue/Svelte surface | Subcomponents, hooks/composables, view-model module |
| Types + runtime mixed | `types.ts` or colocated `*.types.ts` next to owner |
| API/client + parsing | `client.ts`, `mappers.ts`, keep components dumb |
| Shared constants across features | Feature-local constants first; promote only with clear shared scope |
| Test-only complexity | Colocate `*.test.*` or `__tests__` next to subject |

**Colocation**: Keep things **next to** the feature that owns them. Promote to `shared/` or `lib/` only when a **second** real consumer exists or the contract is stable.

#### Rules of thumb

- **New concern** → new file or subfolder under the owning feature, not a new section at the bottom of a megaclass.
- **Barrels**: export a **coherent slice**; do not re-export the whole app from one `index.ts`.
- **Refactors**: move code **without behavior change** first; then split; keep PRs reviewable.
- **Do not** solve structure by endless `// SECTION` comments in a 2k-line file—split instead.

#### When the user asks "where should this go?"

Answer with: **owner feature → layer (ui / domain / data) → file name**. If no owner exists yet, create a minimal folder for that feature rather than defaulting to the nearest big file.

### Deduplication

**One owner for one idea.** When the same logic, type shape, or validation appears in more than one place, consolidate into a single module (or a small shared contract) with a clear owner and import it—do not copy-paste with tiny variations. If two snippets differ only in parameters, use one parameterized function or shared helper rather than parallel near-duplicates. Prefer deleting redundant paths after callers migrate over removing duplicates piecemeal across PRs.

### UI component primitives (prefer registry over hand-roll)

When building or refactoring interactive UI, **default to adding components from the project’s established pattern** (for example shadcn/ui via its CLI or copy-from-registry flow, Radix/Base UI–backed kits, MUI primitives, coss, or whatever the repo already documents) instead of **manually** reimplementing dialogs, popovers, selects, comboboxes, menus, sheets, tooltips, and similar controls from raw `div`s and local state.

- **Reuse the stack**: install or paste the same way existing components were added; do not fork a parallel bespoke version “to avoid a dependency” when the missing piece is standard inventory for that codebase.
- **Composition over reinvention**: own **product-specific** layout, copy, and wiring on top of primitives; do not re-create focus traps, portals, scroll locking, typeahead, or ARIA wiring unless there is a documented gap the registries cannot cover.
- **Leave shared primitive sources stable**: Treat copied registry or design-system files (for example `components/ui/*`) as **shared infrastructure**. Do not edit them to carry feature-specific behavior, domain rules, or one-off hacks during unrelated refactors; **wrap or compose** from feature-owned modules instead. Change those files only for intentional upgrades, accessibility fixes that apply app-wide, or documented design-system work—not as a shortcut for a single screen.
- **Refactor signal**: dense `useEffect` / `onKeyDown` / manual outside-click logic for overlay behavior is usually a cue to **replace** with an installed registry component and delete the custom shell.

If the repo has no UI library yet, prefer **introducing** a small primitive layer through the normal generator/registry for that stack rather than growing ad-hoc controls that the next refactor must untangle.

### Unified wiring (routes, pages, CLI, jobs, plugins)

For each **class of entry point**—HTTP routes, UI pages or screens, CLI subcommands, queue workers, extension points—pick **one registration pattern** and use it everywhere that class appears: same story for “where it is declared,” “how it is discovered,” and “how it is named.” Prefer a single manifest, a single convention-based layout, or a single feature-local registration style per layer; do not mix, for example, half the CLI behind a central dispatcher and half via one-off imports, unless the repo documents an intentional split. When adding a new handler, mirror existing files and registration steps so navigation and onboarding stay predictable.

## Language-specific

Conventions tied to Python and Rust module systems.

### Python packages (`__init__.py`)

#### Rule

**Never put logic in `__init__.py`.** No business rules, algorithms, substantive helpers, I/O, or import-time side effects. Implement behavior in ordinary modules; use `__init__.py` only for package structure and thin, intentional re-exports (for example `__all__` and stable public names), not for executable logic.

### Rust modules (no `mod.rs`)

#### Rule

For a module named `foo`:

- **Preferred:** `foo.rs` (module root) **and** `foo/` (child modules live here as `foo/bar.rs`, etc.).
- **Avoid:** `foo/mod.rs` as the root of `foo`. Do not introduce new `mod.rs` files; migrate away when touching that area.

`lib.rs` / `main.rs` follow the same idea: `mod foo;` resolves to `foo.rs` or `foo/mod.rs` — choose **`foo.rs` + `foo/`**.

#### Layout sketch

```text
src/
  lib.rs
  foo.rs          # pub mod bar; mod util;
  foo/
    bar.rs
    util.rs
```

Not:

```text
src/
  lib.rs
  foo/
    mod.rs        # avoid
    bar.rs
```

#### When editing or adding modules

1. Add **submodules** as `parent/child.rs` and declare them in **`parent.rs`** (the file next to the `parent/` directory).
2. If you see **`path/to/mod.rs`**, plan a move to **`path/to.rs`** beside **`path/to/`** (same module tree, different root file location). Keep `mod` declarations and `pub use` behavior equivalent.
3. **Do not** create new `mod.rs` for convenience or “convention from old tutorials.”

#### Exceptions (rare)

- **Generated code** or **vendored third-party trees** — leave as-is unless the user asks to normalize.
- If the **project’s documented standard** still requires `mod.rs`, follow the repo; otherwise apply this skill.

#### Doc anchor

Rust book: modules can be `module_name.rs` **or** `module_name/mod.rs`; **prefer the `module_name.rs` + `module_name/` form** for new work and refactors.

## Additional detail

For extended heuristics and review prompts, see [reference.md](reference.md).
