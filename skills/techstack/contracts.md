# Contracts

How surfaces agree on message shapes — HTTP, WebSocket, and IPC.

Every TypeScript client (web, mobile, Electron) should have typed HTTP and WebSocket boundaries. Pick the contract strategy by backend language.

## Rules (all pairings)

- Treat compile-time types as necessary but not sufficient — decode or validate at runtime on untrusted input
- Use one contract per transport: shared `Schema` or OpenAPI for HTTP; shared `Schema` or AsyncAPI for WebSockets
- Wrap HTTP and socket IO in Effect programs — keep React components free of raw `fetch` and manual JSON parsing
- Prefer typed message envelopes (`{ "type": "...", "payload": ... }`) for WebSocket protocols
- CI regen and drift checks → [dev.md](dev.md)

## TS ↔ TS

Strongest guarantee — one monorepo, one schema graph, no codegen between app and API.

- Put `Schema`, tagged errors, and domain services in a shared `domain` package
- Put `HttpApi` / handler wiring in a shared `api` package; import the same schemas on client and server
- Encode requests and decode responses with the same `Schema` on both sides
- WebSocket payloads: same `Schema` definitions in `domain`; no OpenAPI or AsyncAPI step needed internally
- Export OpenAPI from Effect Platform only when external consumers need it

## TS ↔ Python

Compile-time types from OpenAPI; runtime validation on both sides.

- Backend: Pydantic v2 models on every FastAPI route body, query model, and response; type-check Python with `ty` or Pyright
- HTTP: FastAPI publishes `/openapi.json` from those models — treat it as the contract; generate TS with `openapi-typescript`
- Client: wrap generated types in Effect programs; add `Schema` decoding on responses when runtime validation matters
- WebSocket: maintain AsyncAPI for message shapes; implement payloads as Pydantic models that match the spec

## TS ↔ Rust

Compile-time types from OpenAPI; serde-backed runtime checks on the server.

- Backend: `serde` types on Axum handlers; derive OpenAPI with `utoipa` — no `serde_json::Value` in domain code
- HTTP: export OpenAPI from `utoipa` → `openapi-typescript` on the TS side
- WebSocket: `asyncapi-rust` with Axum (or a checked-in AsyncAPI spec) aligned with `serde` message enums

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
