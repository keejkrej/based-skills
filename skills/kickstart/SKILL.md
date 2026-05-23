---
name: kickstart
description: Standardize project scaffolding with fixed starter layouts for Python Hatchling apps, Bun apps, TypeScript monorepo apps, Typer CLIs, PySide6 MVVM apps, and Rust crates. Use when the request is to initialize a new project structure.
---

# Kickstart

Create minimal, conventional starter structures. Do not add extra frameworks, folders, or docs unless the user asks.

## Defaults

- Keep names consistent with the requested project name.
- Place scaffolds under the user-provided target directory.
- Keep paths relative to the target root.
- Start with minimal placeholders.
- Prefer established package manager files over ad-hoc setup.
- Avoid extra top-level folders.

## Python App

- Use `src/{name}` layout.
- Use Hatchling.
- Keep package behavior out of `__init__.py`.

```text
{project-root}/
├─ pyproject.toml
└─ src/
   └─ {name}/
      └─ __init__.py
```

```toml
[build-system]
requires = ["hatchling>=1.21.0"]
build-backend = "hatchling.build"

[project]
name = "{project-name}"
version = "0.1.0"
requires-python = ">=3.10"
dependencies = []
```

## TypeScript Monorepo

- Use a root workspace for apps and shared packages.
- Put browser UI in `apps/web`.
- Put API/backend service code in `apps/server`.
- Put shared libraries under root `packages/`, not inside an app.
- Source export is preferred for `packages/*`.
- Add `packages/ui` only when multiple web frontends must share one UI layer.
- Keep `crates/`, `zig/`, and `python/` as optional root namespaces when cross-language work is expected.
- Configure workspaces in the root `package.json` (`"workspaces": ["apps/*", "packages/*"]`).
- Commit `bun.lock` at the repo root.
- Put shared TypeScript defaults in root `tsconfig.base.json`; apps extend it, packages extend it with emit settings.
- Use root `tsconfig.json` with project references for `tsc -b` typechecking.
- Install shadcn primitives into `apps/web/src/components/ui` unless the app folder differs.

```text
{repo-root}/
├─ package.json
├─ bun.lock
├─ tsconfig.base.json
├─ tsconfig.json
├─ apps/
│  ├─ web/
│  │  ├─ package.json
│  │  ├─ tsconfig.json
│  │  └─ src/components/ui/
│  └─ server/
│     ├─ package.json
│     ├─ tsconfig.json
│     └─ src/
├─ packages/
│  ├─ contracts/
│  │  ├─ package.json
│  │  └─ tsconfig.json
│  └─ utils/
│     ├─ package.json
│     └─ tsconfig.json
├─ crates/
├─ zig/
└─ python/
```

`tsconfig.base.json`:

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "Bundler",
    "strict": true,
    "skipLibCheck": true,
    "isolatedModules": true,
    "esModuleInterop": true,
    "resolveJsonModule": true,
    "forceConsistentCasingInFileNames": true,
    "noFallthroughCasesInSwitch": true,
    "moduleDetection": "force"
  }
}
```

Root `tsconfig.json`:

```json
{
  "files": [],
  "references": [
    { "path": "./apps/web" },
    { "path": "./apps/server" },
    { "path": "./packages/contracts" },
    { "path": "./packages/utils" }
  ]
}
```

`apps/web/tsconfig.json`:

```json
{
  "extends": "../../tsconfig.base.json",
  "compilerOptions": {
    "lib": ["ES2022", "DOM", "DOM.Iterable"],
    "jsx": "react-jsx",
    "noEmit": true
  },
  "include": ["src/**/*"]
}
```

`apps/server/tsconfig.json`:

```json
{
  "extends": "../../tsconfig.base.json",
  "compilerOptions": {
    "lib": ["ES2022"],
    "types": ["bun"],
    "allowImportingTsExtensions": true,
    "noEmit": true
  },
  "include": ["src/**/*"]
}
```

`packages/*/tsconfig.json`:

```json
{
  "extends": "../../tsconfig.base.json",
  "compilerOptions": {
    "composite": true,
    "declaration": true,
    "declarationMap": true,
    "rootDir": "src",
    "outDir": "dist"
  },
  "include": ["src/**/*"]
}
```

## TypeScript Bun App

- Use ESM.
- Run with Bun.
- Put the entrypoint under `src/index.ts`.
- Keep runtime wiring in `src/app.ts`.
- Keep config loading in `src/config.ts`.

```text
{project-root}/
├─ package.json
├─ tsconfig.json
└─ src/
   ├─ index.ts
   ├─ app.ts
   └─ config.ts
```

```json
{
  "name": "{name}",
  "version": "0.1.0",
  "type": "module",
  "scripts": {
    "dev": "bun --watch src/index.ts",
    "start": "bun src/index.ts",
    "typecheck": "tsc -p tsconfig.json",
    "test": "echo No tests configured"
  },
  "dependencies": {},
  "devDependencies": {
    "typescript": "^5.7.0",
    "@types/bun": "latest"
  }
}
```

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "Bundler",
    "lib": ["ES2022"],
    "types": ["bun"],
    "strict": true,
    "skipLibCheck": true,
    "isolatedModules": true,
    "esModuleInterop": true,
    "resolveJsonModule": true,
    "forceConsistentCasingInFileNames": true,
    "noFallthroughCasesInSwitch": true,
    "moduleDetection": "force",
    "allowImportingTsExtensions": true,
    "noEmit": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules"]
}
```

## Python Typer CLI

- Use the Python `src/{name}` layout.
- Define the Typer app in `core.py`.
- Keep command modules under `commands/`.
- Use `main.py` to import commands and call `app()`.
- Add a `[project.scripts]` entry in `pyproject.toml`.

```text
{project-root}/
├─ pyproject.toml
└─ src/
   └─ {name}/
      ├─ main.py
      ├─ core.py
      ├─ __init__.py
      └─ commands/
         ├─ __init__.py
         └─ {name}.py
```

## Python PySide6 MVVM App

- Use the Python `src/{name}` layout.
- Use Hatchling.
- Use PySide6.
- Keep package behavior out of `__init__.py`.
- Put widgets and windows in `views/`.
- Put presentation state and Qt bindings in `viewmodels/` (`QObject` subclasses with signals/properties).
- Put domain/data logic in `models/` with no Qt widget imports.
- Wire the app in `main.py`: create `QApplication`, instantiate view-model, pass it to the view, call `show()`, then `exec()`.
- Add a `[project.scripts]` entry in `pyproject.toml`.
- Add `views/ui/` only when using Qt Designer `.ui` files.
- Do not import models from views; views talk to view-models only.

```text
{project-root}/
├─ pyproject.toml
└─ src/
   └─ {name}/
      ├─ __init__.py
      ├─ main.py
      ├─ models/
      │  ├─ __init__.py
      │  └─ {feature}_model.py
      ├─ viewmodels/
      │  ├─ __init__.py
      │  └─ {feature}_viewmodel.py
      └─ views/
         ├─ __init__.py
         └─ {feature}_view.py
```

```toml
[build-system]
requires = ["hatchling>=1.21.0"]
build-backend = "hatchling.build"

[project]
name = "{project-name}"
version = "0.1.0"
requires-python = ">=3.10"
dependencies = ["PySide6>=6.6.0"]

[project.scripts]
{name} = "{name}.main:run"
```

`src/{name}/main.py`:

```python
import sys

from PySide6.QtWidgets import QApplication

from {name}.views.{feature}_view import {Feature}View
from {name}.viewmodels.{feature}_viewmodel import {Feature}ViewModel


def run() -> None:
    app = QApplication(sys.argv)
    viewmodel = {Feature}ViewModel()
    view = {Feature}View(viewmodel)
    view.show()
    raise SystemExit(app.exec())
```

`src/{name}/models/{feature}_model.py`:

```python
from dataclasses import dataclass


@dataclass
class {Feature}Model:
    title: str = ""
```

`src/{name}/viewmodels/{feature}_viewmodel.py`:

```python
from PySide6.QtCore import Property, QObject, Signal

from {name}.models.{feature}_model import {Feature}Model


class {Feature}ViewModel(QObject):
    title_changed = Signal(str)

    def __init__(self, model: {Feature}Model | None = None) -> None:
        super().__init__()
        self._model = model or {Feature}Model()

    def get_title(self) -> str:
        return self._model.title

    def set_title(self, value: str) -> None:
        if value != self._model.title:
            self._model.title = value
            self.title_changed.emit(value)

    title = Property(str, get_title, set_title, notify=title_changed)
```

`src/{name}/views/{feature}_view.py`:

```python
from PySide6.QtWidgets import QMainWindow

from {name}.viewmodels.{feature}_viewmodel import {Feature}ViewModel


class {Feature}View(QMainWindow):
    def __init__(self, viewmodel: {Feature}ViewModel) -> None:
        super().__init__()
        self._viewmodel = viewmodel
        self.setWindowTitle(viewmodel.title)
        viewmodel.title_changed.connect(self.setWindowTitle)
```

## Rust Crate

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
