---
name: rust-no-modrs
description: >-
  Enforces Rust’s non–mod.rs module layout: a module `m` uses `m.rs` next to
  directory `m/` for children, not `m/mod.rs`. Use for Rust crates, module
  refactors, new features, code review, or when the user mentions mod.rs,
  no modrs, module structure, src layout, or splitting modules.
---

# Rust modules (no `mod.rs`)

## Rule

For a module named `foo`:

- **Preferred:** `foo.rs` (module root) **and** `foo/` (child modules live here as `foo/bar.rs`, etc.).
- **Avoid:** `foo/mod.rs` as the root of `foo`. Do not introduce new `mod.rs` files; migrate away when touching that area.

`lib.rs` / `main.rs` follow the same idea: `mod foo;` resolves to `foo.rs` or `foo/mod.rs` — choose **`foo.rs` + `foo/`**.

## Layout sketch

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

## When editing or adding modules

1. Add **submodules** as `parent/child.rs` and declare them in **`parent.rs`** (the file next to the `parent/` directory).
2. If you see **`path/to/mod.rs`**, plan a move to **`path/to.rs`** beside **`path/to/`** (same module tree, different root file location). Keep `mod` declarations and `pub use` behavior equivalent.
3. **Do not** create new `mod.rs` for convenience or “convention from old tutorials.”

## Exceptions (rare)

- **Generated code** or **vendored third-party trees** — leave as-is unless the user asks to normalize.
- If the **project’s documented standard** still requires `mod.rs`, follow the repo; otherwise apply this skill.

## Doc anchor

Rust book: modules can be `module_name.rs` **or** `module_name/mod.rs`; **prefer the `module_name.rs` + `module_name/` form** for new work and refactors.
