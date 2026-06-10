# Rust Crate

- Put single crates under `crates/{name}`.
- Use `main.rs` for binary entry.
- Use `lib.rs` when reusable library code exists.
- Split behavior into `cli.rs`, `app.rs`, and focused modules as needed.
- Use `foo.rs` plus `foo/` child modules.
- Do not create `mod.rs`.

```text
crates/{name}/
├─ Cargo.toml
└─ src/
   ├─ main.rs
   ├─ lib.rs
   ├─ cli.rs
   └─ app.rs
```

```toml
[package]
name = "{name}"
version = "0.1.0"
edition = "2021"

[dependencies]
```

```text
src/
├─ app.rs
└─ app/
   ├─ config.rs
   └─ runner.rs
```
