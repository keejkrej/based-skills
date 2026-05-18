---
name: refactor
description: >-
  Guides structural refactors: cohesive modules (no god files), deduplication,
  consistent wiring for routes/CLIs/pages, registry UI primitives over
  hand-rolled controls, component vs state/effect splitting, slider/input
  commit models, package source vs artifact exports in monorepos, and
  language-specific layout (Rust non-mod.rs, empty Python `__init__.py`). Use
  when designing file layout, splitting large files, removing duplication, Rust
  crate structure, Python package layout, code review, UI refactors, state or
  effect refactors, or when the user mentions cohesion, boundaries, mod.rs,
  `__init__.py`, god files, shadcn, registry primitives, component registries,
  or "where should this live?"
---

# Refactor

Use these rules as short checks while editing, reviewing, or proposing structure.

## Structure

- Keep changes in small, cohesive units with one clear owner.
- Give each file one reason to change.
- Name files by responsibility; avoid broad `utils`, `helpers`, or `stuff` files unless the scope is narrow.
- Keep routes, pages, screens, commands, and containers thin; move types, transforms, side effects, and domain rules into owned modules.
- Split files that mix UI, domain rules, persistence, and ad-hoc I/O.
- Split modules whose imports cross unrelated domains or layers.
- Treat file length as a weak signal; cohesion matters more than line count.
- Add a new file or subfolder for a new concern instead of another section in a large file.
- Use `// SECTION` comments only as temporary orientation, not as a substitute for modules.
- Prefer colocating code beside the feature that owns it.
- Promote to `shared/` or `lib/` only after a second real consumer exists or the contract is stable.
- Export coherent slices from barrels; do not re-export unrelated subsystems from one `index.ts`.
- Move code without behavior changes first; split behavior changes into follow-up edits when practical.
- When asked where something goes, answer: owner feature -> layer (`ui` / `domain` / `data`) -> file name.
- If no owner exists, create a minimal feature folder instead of growing the nearest large file.

## Deduplication

- Keep one owner for one idea.
- Consolidate repeated logic, type shapes, validation, or transformations into one clearly owned module.
- Parameterize tiny variations instead of keeping parallel near-duplicates.
- Delete redundant paths after callers migrate.

## UI Primitives

- Prefer the repo's registry primitives for dialogs, popovers, selects, comboboxes, menus, sheets, tooltips, and similar controls.
- Use the same primitive source the repo already uses: shadcn CLI, Radix/Base UI-backed kits, MUI, coss, or the documented local registry.
- Do not edit registry primitive files for feature behavior or one-off styling.
- Wrap or compose primitives in feature-owned modules.
- Reuse focus traps, portals, scroll locking, typeahead, and ARIA wiring from primitives unless there is a documented gap.
- Treat dense overlay `useEffect`, `onKeyDown`, and outside-click logic as a signal to replace custom UI with a primitive.
- If the repo has no UI library, introduce a small primitive layer through the normal generator or registry instead of growing ad-hoc controls.

## Component, State, and Effect Boundaries

- Split UI components by rendering responsibility: visible slots, repeated items, dense panels, independent forms, and reusable controls.
- Name component files by what they render, not by the state they read.
- Do not mirror component or layout boundaries in state; avoid `leftStore`, `dockState`, and effect files named after UI regions.
- Split state by domain ownership and lifecycle: workspace/source, frame data, grid, selection, history, save/export, preferences, and transient drafts.
- Keep one workflow-facing hook or store facade while a screen is simple; prefer `useXPageState()` over many tiny stores.
- Extract selectors and domain actions before splitting stores.
- Keep server/cache state in the query/cache layer unless local editing or offline workflow requires otherwise.
- Keep local interaction state near the component: hover, open/closed UI, draft input, drag preview, focus, and measurement state.
- Split effects by external system or lifecycle: subscriptions, persistence, data loading, URL sync, canvas measurement, and timers.
- Do not split simple state/effects for symmetry; wait for readability, reuse, testability, or lifecycle pressure.
- Preserve the app-facing state API during internal refactors.

## Input Commit Models

- Be explicit about commit semantics for sliders, numeric inputs, range controls, filters, and other high-frequency inputs.
- Use immediate commit only when every change should update app state and trigger rendering, transforms, fetching, persistence, or URL updates.
- Use delayed commit when local draft state should update during interaction and expensive work should run on blur, Enter, pointer release, debounce, or Apply.
- Avoid accidental immediate commits for expensive rendering, heavy data transformation, network fetching, cache invalidation, or persistent state.
- Keep draft state feature-local.
- Name handlers clearly: `onValueChange` vs `onValueCommit`, `onDraftChange` vs `onApply`.
- Preserve existing commit semantics during refactors unless the behavior change is deliberate and tested.

## Wiring

- Use one registration pattern per entry-point class: HTTP routes, pages/screens, CLI commands, workers, jobs, plugins, and extension points.
- Keep declaration, discovery, and naming consistent within each class.
- Prefer one manifest, one convention-based layout, or one feature-local registration style per layer.
- Mirror existing registration steps when adding handlers.
- Do not mix central dispatch, convention discovery, and one-off imports unless the repo documents the split.

## Package Artifacts

- In monorepos, check whether `packages/*` consumers import source or built artifacts.
- Inspect `package.json` `exports`, `main`, `module`, `types`, workspace aliases, TypeScript paths, bundler aliases, and dev-server caches.
- Remember that fixing package source may not affect a running app if it imports stale `dist/`, `build/`, generated CSS, declarations, or packaged entry points.
- Check whether Turborepo or the task runner watches and rebuilds dependent package artifacts during dev.
- Run the package build/watch task, adjust the dev pipeline, or point local dev at source before reworking a source fix that already looks correct.
- Verify which file the app is executing before assuming the bug still exists.

## Python

- Keep `__init__.py` empty or limited to thin, intentional public re-exports.
- Do not put business rules, algorithms, helpers, I/O, side effects, or substantive logic in `__init__.py`.
- Implement behavior in ordinary modules.

## Rust

- Prefer `foo.rs` as the module root plus `foo/` for child modules.
- Avoid adding new `foo/mod.rs` files.
- Add children as `foo/bar.rs` and declare them in `foo.rs`.
- When touching existing `path/to/mod.rs`, consider moving it to `path/to.rs` while preserving `mod` declarations and `pub use` behavior.
- Leave generated, vendored, or explicitly repo-standard `mod.rs` trees alone unless asked to normalize.

## More Detail

- For extra review prompts and framework notes, read [reference.md](reference.md).
