---
name: refactor
description: >-
  Runs a language-agnostic phased codebase refactor: diagnose anti-patterns,
  propose a target code tree, physically separate code, align each branch to the
  pattern with boundary APIs/signals only, wire integrations, and verify. Each
  phase has its own plan in `.refactor/plans/`. Deliverables: `DIAGNOSIS.md`,
  `TARGET_TREE.md`, `PROGRESS.md`. Use when refactoring any codebase or when the
  user asks for a refactor strategy.
---

# Refactor

Language-agnostic process for structural cleanup in any codebase. Adapt scan tactics, module boundaries, and verification commands to the project's stack and layout.

Execute refactors as a recorded, phased workflow in the target repo. Do not start moving code until diagnosis is written and reviewed.

For stack-specific structure rules, use **bestpractice** or the repo's own conventions while diagnosing and fixing.

## Artifacts

Keep refactor state under `.refactor/` in the target repo:

```
.refactor/
├── DIAGNOSIS.md              # Phase 1 deliverable
├── TARGET_TREE.md            # Phase 2 deliverable
├── PROGRESS.md               # live status across phases
└── plans/
    ├── phase-1-diagnose.md
    ├── phase-2-target-tree.md
    ├── phase-3-separate.md
    ├── phase-4-pattern-align.md
    ├── phase-5-wire.md
    └── phase-6-verify.md
```

| Path | Phase | Purpose |
|------|-------|---------|
| `plans/phase-N-*.md` | 1–6 | **Plan** for that phase — write before executing |
| `DIAGNOSIS.md` | 1 | Anti-patterns, evidence, severity |
| `TARGET_TREE.md` | 2 | Target code tree matching the chosen pattern |
| `PROGRESS.md` | 3–6 | Execution status vs the active phase plan |

Plan templates: [plans/README.md](plans/README.md).

Add `.refactor/` to version control unless the user asks for a local-only audit.

## Phase 1 — Diagnose

**Goal:** Name concrete anti-patterns with file-level evidence. No production code changes in this phase.

### Workflow

1. Write `.refactor/plans/phase-1-diagnose.md` from [plans/phase-1-diagnose.md](plans/phase-1-diagnose.md).
2. Confirm refactor scope with the user when ambiguous (whole repo, one app, one feature, one layer).
3. Scan the scoped tree: entry points, largest units, cross-boundary dependencies, duplicated logic, test layout, package or module boundaries.
4. Infer the intended architecture (MVC, MVVM, layered, hexagonal, etc.) from docs, layout, and framework defaults; see [architecture-patterns.md](architecture-patterns.md).
5. Check against the anti-pattern catalog in [anti-patterns.md](anti-patterns.md).
6. Record every finding in `.refactor/DIAGNOSIS.md` using [diagnosis-template.md](diagnosis-template.md).
7. Rank findings by impact and coupling; mark blockers vs deferrable.
8. Mark phase plan exit criteria met. Stop and present the diagnosis summary. Do not propose a target tree or edit production code until the user confirms scope and priorities (unless diagnose-only).

### Diagnosis rules

- Cite paths and brief evidence for every finding (size, dependency direction, duplicate symbols, mixed concerns).
- Separate **symptoms** (large unit) from **anti-patterns** (unrelated concerns in one owner).
- Note what is intentionally deferred and why.
- Do not prescribe folder moves in diagnosis; reserve layout targets for `TARGET_TREE.md`.
- Re-read diagnosis when new areas are discovered mid-refactor; append a **Revision** section with date and delta.

## Phase 2 — Target tree

**Goal:** Propose a code tree that matches the chosen pattern and gives later phases a fixed layout to aim at.

1. Write `.refactor/plans/phase-2-target-tree.md` from [plans/phase-2-target-tree.md](plans/phase-2-target-tree.md).
2. Read confirmed `DIAGNOSIS.md` — especially **Intended architecture**, clusters, and deferred items.
3. Pick or confirm the target pattern from [architecture-patterns.md](architecture-patterns.md). Resolve hybrids explicitly.
4. Write `.refactor/TARGET_TREE.md` using [target-tree-template.md](target-tree-template.md):
   - ASCII or indented tree for the scoped repo root
   - Pattern role per major path
   - Dependency rules for the tree
   - **Current → target** mapping for each diagnosis cluster
   - New units to introduce and what stays unchanged
5. Ensure every critical/high finding maps to a target path or mapping row.
6. Mark phase plan exit criteria met. Stop and present the target tree summary. Do not separate code or edit production logic until the user confirms the layout (unless target-tree-only).

### Target tree rules

- Optimize for **stable boundaries**, not a mirror of today's filenames.
- Prefer incremental reachability — the tree should be achievable via Phase 3 separation slices.
- List every branch and leaf that Phase 4 will walk in **Role map**.
- Do not invent empty top-level folders with no mapped code unless the diagnosis requires a new boundary.
- When diagnosis and pattern defaults conflict, document the choice in **Open decisions** rather than silently blending models.

## Phase 3 — Separate

**Goal:** Physically slice, copy, paste, and group related code into the files and folders in `TARGET_TREE.md`. **Do not care about broken wiring in this phase.**

### Workflow

1. Write `.refactor/plans/phase-3-separate.md` from [plans/phase-3-separate.md](plans/phase-3-separate.md) using **Current → target** mapping — one slice per cohesive move.
2. Create or update `.refactor/PROGRESS.md` with a **Separation** checklist mirroring the phase plan.
3. Work one slice at a time using [separation-tactics.md](separation-tactics.md):
   - Move cohesive code blocks into target paths.
   - Prefer **intact split** tactics (mixins, partials, traits, same-package files, extensions) so call sites stay valid while code is physically separated.
   - When intact split is not available, cut-and-paste or lift-and-shift anyway; leave broken references for Phases 4–5.
4. Within a slice:
   - No behavior changes, symbol renames for style, or deduplication unless required to compile an intact split.
   - Do **not** fix cross-branch imports, exports, DI, routes, or tests — record expected breaks in `PROGRESS.md`.
   - Mark slice done when code **physically lives** at the target location per `TARGET_TREE.md`.
5. If separation reveals new anti-patterns or invalidates the target tree, append to `DIAGNOSIS.md`, revise `TARGET_TREE.md` and `plans/phase-3-separate.md`, then continue.
6. Mark phase plan exit criteria met. Stop after all separation slices are done (or agreed subset). Present summary before Phase 4.

### Separation rules

- Phase 3 is **layout only** — contracts and wiring come in Phases 4 and 5.
- Group by **related responsibility**, not by alphabetical or incidental proximity.
- A red build or failing tests after a slice is expected unless intact split kept the unit whole.
- Do not mix unrelated diagnosis clusters in one slice.

## Phase 4 — Pattern align

**Goal:** Refactor **each leaf and branch** in `TARGET_TREE.md` to match the pattern role and publish **boundary APIs/signals only** — no cross-branch wiring.

### Workflow

1. Write `.refactor/plans/phase-4-pattern-align.md` from [plans/phase-4-pattern-align.md](plans/phase-4-pattern-align.md) — one entry per branch/leaf from **Role map**.
2. Read `TARGET_TREE.md`, `PROGRESS.md` **Separation** notes, and the phase plan.
3. Add a **Pattern align** section to `PROGRESS.md`; see [branch-refactor.md](branch-refactor.md).
4. Walk branches per plan order (default: inner/core → application → infrastructure → presentation → bootstrap slots).
5. For each leaf or branch:
   - **Inventory** — files, symbols, Phase 3 glue
   - **Role fit** — evict code that belongs elsewhere; replace concrete cross-layer deps with port/contract types
   - **Internal shape** — apply **bestpractice** inside the branch
   - **Define API/signals** — ports, interfaces, commands, events, public module surfaces at the branch edge
   - **Local verify** — branch compiles in isolation or with mocks; mark `contracts defined`
6. Work **one branch at a time**.
7. Mark phase plan exit criteria met. Stop when every in-scope branch is `contracts defined` or `deferred`. Present contract summary before Phase 5.

### Pattern align rules

- Phase 4 produces **contracts**, not connections — callers bind in Phase 5.
- Replace illegal cross-branch imports with abstractions; leave `TODO wire` where binding is needed.
- Default to behavior-preserving internal refactors; behavior changes need explicit user approval.
- Do **not** register DI, routes, event handlers, or fix cross-branch import graphs in Phase 4.

## Phase 5 — Wire

**Goal:** Connect branch boundaries using the APIs and signals defined in Phase 4.

### Workflow

1. Write `.refactor/plans/phase-5-wire.md` from [plans/phase-5-wire.md](plans/phase-5-wire.md) — one task per integration edge from Phase 4 contracts.
2. Read `TARGET_TREE.md`, `PROGRESS.md` **Pattern align**, and the phase plan; see [wiring.md](wiring.md).
3. Add a **Wiring** section to `PROGRESS.md` mirroring the plan.
4. Wire one integration edge at a time (port → adapter, route → handler, event → subscriber, DI registration).
5. Order: adapters and infrastructure → application → presentation → bootstrap/composition root.
6. Remove Phase 3 stubs and duplicates as each edge goes live.
7. Run build and tests incrementally; mark each wiring task done when the edge works.
8. Mark phase plan exit criteria met. Stop when the refactor scope builds and tests pass (or failures are documented). Present summary before Phase 6.

### Wiring rules

- Connect through **Phase 4 contracts** only — do not bypass published boundaries.
- Do not change contract shapes casually; update Phase 4 plan/progress if the API must change.
- Do not move code between branches — send layout issues to Phase 3, pattern issues to Phase 4.
- Default to behavior-preserving connections.

## Phase 6 — Verify

**Goal:** Confirm layout, pattern contracts, wiring, and behavior for the refactor scope.

1. Write `.refactor/plans/phase-6-verify.md` from [plans/phase-6-verify.md](plans/phase-6-verify.md).
2. Execute checks in the plan: layout vs `TARGET_TREE.md`, pattern contracts, wiring completeness, diagnosis re-scan.
3. Run standard test, lint, and build commands listed in the plan.
4. Add **Outcome** to `PROGRESS.md`: resolved, deferred, follow-ups, gaps.
5. Mark phase plan exit criteria met. Present final summary.

## Operating constraints

- **Write the phase plan before executing that phase** — plans live in `.refactor/plans/`, one file per phase.
- Phases 3 → 4 → 5 are intentionally split: **layout → contracts → connections**.
- One concern per commit when the user asks for commits.
- Do not rename `.refactor/` artifacts or plan files mid-flight without updating all references.
- `TARGET_TREE.md` is the layout and role authority; update it deliberately when scope changes.
- When diagnosis, target tree, and bestpractice conflict, follow bestpractice for structure rules and this skill for process order.

## References

- Phase plans: [plans/README.md](plans/README.md)
- Architecture patterns: [architecture-patterns.md](architecture-patterns.md)
- Anti-pattern catalog: [anti-patterns.md](anti-patterns.md)
- Diagnosis template: [diagnosis-template.md](diagnosis-template.md)
- Target tree template: [target-tree-template.md](target-tree-template.md)
- Separation tactics: [separation-tactics.md](separation-tactics.md)
- Branch refactor guide: [branch-refactor.md](branch-refactor.md)
- Wiring guide: [wiring.md](wiring.md)
