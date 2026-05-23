# Separation Tactics

Use in **Phase 3 — Separate**. Goal: related code lives in the right files and folders per `TARGET_TREE.md`. Wiring may be wrong or broken until Phase 4.

Pick tactics that match the stack. Prefer **intact split** when the language merges physical files into one logical unit without rewriting call sites.

## Principles

- Move **cohesive chunks** — one concern, one cluster, one pattern role at a time.
- Match **destination paths** in `TARGET_TREE.md`; do not invent ad hoc folders mid-slice.
- **Do not rewire** in this phase: no import cleanup, export fixes, route registration, DI graph edits, or API redesign unless required for an intact-split mechanism.
- **No behavior changes** — same logic, different file boundaries.
- A broken build after a slice is acceptable; record it in `PROGRESS.md` and continue unless the user asked to keep the tree green.

## Intact split — one logical unit, many files

Use when the stack composes multiple files into one type, module, or object.

| Mechanism | Stacks | What to do |
|-----------|--------|------------|
| Same package / module | Go, Java, Kotlin, many others | Move functions and types into new files under the same package; package clause unchanged |
| Partial types | C#, VB | Split methods across `partial class` files; keep one type name |
| Mixins / concerns | Ruby, Swift (protocol extensions), some JS | Move method groups into included modules; host class unchanged at call sites |
| Traits | Rust, PHP, Scala | Move impl blocks or trait methods into child modules/files; keep public type surface |
| Extensions | Swift, Kotlin | Move method groups to extension files in the target folder |
| Categories / classifications | Objective-C | Move method groups to category files |
| Augmentation / merging | TypeScript | Split interfaces or namespace members across files; merge at compile time |
| Include / import composition | C, C++, some legacy | Physical split with include or import of implementation units |
| Nested module files | Python, Rust | Move code to sibling modules; leave thin re-export or import stub in old file for Phase 4 |
| Split implementation | C++, separate `.h` / `.cpp` | Declarations vs definitions in pattern-aligned paths |

When using intact split, leave **minimal glue** in the original file (include, import, mixin, partial declaration) — full wiring cleanup belongs in Phase 4.

## Physical split — wiring will break

Use when intact split is unavailable or too awkward.

| Tactic | When | Phase 3 action |
|--------|------|----------------|
| Cut and paste | Clear boundary, small unit | Move block to target file; leave broken reference or empty stub |
| Extract file | God unit sections | One new file per cohesive section; old file keeps remainder |
| Extract folder | Feature cluster | Move related files into target directory; paths change, links break |
| Copy then trim | Risky cut | Copy chunk to target, verify duplicate exists, then delete from source in same slice |
| Lift and shift | Whole module mis-placed | Move file(s) to target path as-is; fix imports in Phase 4 |

Leave `# TODO wire` stubs for Phase 5 — Phase 4 defines contracts only.

## Slice sizing

| Size | Good for |
|------|----------|
| One section → one file | God unit first pass |
| One feature cluster → one folder | Modular monolith moves |
| One pattern role → one layer folder | Layered / hexagonal separation |
| One diagnosis cluster | Tied to finding IDs in `plans/phase-3-separate.md` |

One slice = one PR-sized physical move. Do not mix unrelated clusters.

## What not to do in Phase 3

- Rename public symbols for aesthetics
- Change algorithms or control flow
- Fix tests by editing expectations
- Update route tables, config manifests, or build scripts (unless moving the file they reference)
- Dedupe logic across files — dedupe in Phase 4 or later if still needed
- Collapse or merge target-tree folders because wiring is hard

## Record per slice in PROGRESS.md

- Slice ID and finding IDs
- Source paths → destination paths
- Tactic used (intact split vs physical split)
- Glue left behind for Phase 4
- Known broken cross-branch refs — Phase 4 defines contracts; Phase 5 wires
