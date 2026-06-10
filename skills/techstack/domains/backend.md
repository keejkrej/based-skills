# Backend

APIs, services, scripts, and data jobs.

## TypeScript API (default)

- Colocated UI+API in TanStack Start → [fullstack.md](fullstack.md)
- Use `@effect/platform` for TypeScript/JavaScript HTTP services — not a separate router framework
- Prefer `HttpApi` + `HttpApiBuilder` for declarative route and schema definitions
- Use `HttpRouter` when a lower-level router fits better than `HttpApi`
- Serve on Bun with `@effect/platform-bun` (`BunHttpServer.layer(…)`)
- Implement handlers as `Effect` programs; provide dependencies through `Layer`
- Reuse the same `Schema` types the web and mobile clients decode
- Implement WebSockets with Effect Platform `Socket` when realtime is needed in Effect services
- Model business logic as `Effect` programs with `Effect.gen`
- Define services with `Context.Tag` and wire them with `Layer`
- Use `Schema` for validation, decoding, and shared contracts
- Model failures with tagged errors (`Data.TaggedError`) instead of thrown exceptions
- Run servers and CLIs with `BunRuntime.runMain`
- Test services and handlers by providing test `Layer`s instead of mocking globals
- Effect patterns → `effect-ts` skill
- Scaffold → `template` skill `scaffolds/ts-bun-app.md`

## Python API

- Use FastAPI for Python-first APIs, data/ML adjacency, and headless Python tooling exposed over HTTP
- Use Pydantic v2 models on all FastAPI boundaries; publish OpenAPI for TS clients → [contracts.md](contracts.md)
- Use Python for rapid scripts, CLIs, data jobs, scraping, notebooks, and orchestration prototypes unless the repo is Rust- or Bun-centric
- Treat prototypes as transitional; keep core algorithms easy to migrate
- Tooling → [dev.md](dev.md)
- Scaffold → `template` skill `scaffolds/python-typer-fastapi.md` or `scaffolds/python-app.md`

## Rust API

- Use Axum for Rust services when throughput, hardening, or binary deployment matters
- Type Axum handlers with `serde` and export OpenAPI via `utoipa` when Rust serves TypeScript clients
- Use `foo.rs` plus `foo/` child modules; never use `mod.rs`
- Tooling → [dev.md](dev.md)
- Scaffold → `template` skill `scaffolds/rust-crate.md`

## Rules

- Pick one primary server stack per service
- When the backend is not TypeScript, keep contracts machine-readable → [contracts.md](contracts.md)

## Production

- Prefer Rust for maintained binaries, services, installers, crates, reproducible deployments, performance, and long-lived production code
- Do not replace a working Rust production path with Python for convenience unless the user explicitly wants that tradeoff

## Docs

- Effect Platform: https://effect.website/docs/platform/introduction
- Effect Platform README (HttpApi, HttpServer): https://github.com/Effect-TS/effect/blob/main/packages/platform/README.md
- FastAPI: https://fastapi.tiangolo.com
- Pydantic: https://docs.pydantic.dev
- Axum: https://docs.rs/axum/latest/axum
- serde: https://serde.rs
- utoipa: https://docs.rs/utoipa/latest/utoipa/
- Python: https://docs.python.org/3
- Rust: https://doc.rust-lang.org
