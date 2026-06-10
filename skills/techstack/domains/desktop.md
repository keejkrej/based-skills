# Desktop

Desktop and native apps on the user's machine.

## Electron (UI-first default)

- Use Electron for greenfield UI-first desktop apps
- Bundle the same web SPA stack inside Electron → [frontend.md](frontend.md)
- Use Electron preload/IPC only for narrow OS capabilities
- Run companion backends as Effect Platform services on Bun over localhost when the UI needs a separate process → [backend.md](backend.md)
- Keep primary app conversation over localhost WebSockets when the UI has a companion backend
- Contracts for companion backends → [contracts.md](contracts.md)

## Transport

- Prefer WebSockets for bidirectional frontend-to-backend communication
- Prefer `127.0.0.1` / `localhost` with `ws://` or `wss://` for local companion backends
- Keep the backend runnable and testable outside the desktop shell
- Use Effect Platform `Socket` in Effect backends; use IPC/native bridges only for privileged OS capabilities or tight synchronous handshakes
- Bind to localhost by default
- Add auth or token pairing if exposure beyond the machine is possible
- Non-HTTP IPC contracts → [contracts.md](contracts.md)

## Native UI

- Use egui when Rust is primary and the UI is tooling, demo, internal panel, simulation, or Rust-loop oriented
- Use Qt 6 when mature widgets, accessibility depth, OEM integration, or Qt designer workflows matter
- Avoid Electron when native performance is the reason the product exists
- Scientific imaging with Qt → [scientific.md](scientific.md)

## Python + Qt

- Use PySide6 only to extend existing QtPy, PyQt, or PySide-style codebases
- Reassess Qt bindings, Rust UI, or web UI for greenfield Python GUI work
- Scaffold → `template` skill `scaffolds/qtpy-mvp.md`

## Docs

- Electron: https://www.electronjs.org/docs
- WebSocket: https://developer.mozilla.org/en-US/docs/Web/API/WebSocket
- egui: https://docs.rs/egui/latest/egui
- Qt 6: https://doc.qt.io/qt-6
- PySide6: https://doc.qt.io/qtforpython-6
