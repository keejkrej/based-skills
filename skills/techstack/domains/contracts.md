# Contracts

How surfaces agree on message shapes — HTTP, WebSocket, and IPC.

Every TypeScript client (web, mobile, Electron) should have typed HTTP and WebSocket boundaries. Pick the contract strategy by backend language.

## Rules (all pairings)

- Treat compile-time types as necessary but not sufficient — decode or validate at runtime on untrusted input
- Use one contract per transport: shared `Schema` or OpenAPI for HTTP; shared `Schema` or AsyncAPI for WebSockets
- Wrap HTTP and socket IO in Effect programs — keep React components free of raw `fetch` and manual JSON parsing
- Prefer typed message envelopes (`{ "type": "...", "payload": ... }`) for WebSocket protocols
- CI regen and drift checks → `tooling/domains/dev.md`

## TS ↔ TS

Strongest guarantee — one monorepo, one schema graph, no codegen between app and API.

- Put `Schema`, wire types, decode helpers, and `HttpApi` definitions in `packages/contracts`
- Put runtime, `HttpApiClient`, ports, and shared query atoms in `packages/client`
- `domain` / `api` naming is equivalent when a repo already uses it — prefer `contracts` / `client` on greenfield
- Encode requests and decode responses with the same `Schema` on both sides
- WebSocket payloads: same `Schema` definitions in `contracts`; no OpenAPI or AsyncAPI step needed internally
- Export OpenAPI from Effect Platform when external consumers or cross-language backends need it

## TS ↔ Python

Compile-time types from OpenAPI; runtime validation on both sides.

- Backend: Pydantic v2 models on every FastAPI route body, query model, and response; type-check Python with `ty` or Pyright
- HTTP: FastAPI publishes `/openapi.json` from those models — treat it as the contract; generate TS with `openapi-typescript`
- Client: wrap generated types in Effect programs; add `Schema` decoding on responses when runtime validation matters
- WebSocket: maintain AsyncAPI for message shapes; implement payloads as Pydantic models that match the spec

## TS ↔ Rust

Compile-time types from OpenAPI; serde-backed runtime checks on the server.

- SSOT: Effect `Schema` + `HttpApi` in `packages/contracts` — generate `openapi.json` and a JSON Schema bundle from it
- Backend: generate Rust types (`typify` or equivalent) from the JSON Schema bundle; Axum handlers use `serde` — no `serde_json::Value` in domain code
- Client: `HttpApiClient.make` in `packages/client` from the same `HttpApi` definition
- CI: regen specs and generated types; fail on drift (`check:contracts`)
- WebSocket: `asyncapi-rust` with Axum (or a checked-in AsyncAPI spec) aligned with `serde` message enums
- `utoipa` + `openapi-typescript` is an alternative when Rust owns the OpenAPI source instead of Effect Schema

## Non-HTTP boundaries

For Tauri commands, Electron IPC, or other non-REST surfaces without OpenAPI:

- Rust: Specta (`specta::Type`) → TypeScript export
- Python: Pydantic models → TypeScript via `pydantic-to-typescript` when AsyncAPI does not apply

## Docs

- OpenAPI: https://swagger.io/specification/
- AsyncAPI: https://www.asyncapi.com/docs
- openapi-typescript: https://openapi-ts.dev
- Specta (IPC only): https://docs.rs/specta/latest/specta/
- pydantic-to-typescript (IPC only): https://github.com/phillipdupuis/pydantic-to-typescript
