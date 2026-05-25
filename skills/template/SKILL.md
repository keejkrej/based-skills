---
name: template
description: Standardize project scaffolding with fixed starter layouts for Python Hatchling apps, Bun apps, TypeScript monorepo apps, Typer CLIs, QtPy MVP apps, and Rust crates. Use when the request is to initialize a new project structure or copy scaffold templates.
---

# Template

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
- Define the Typer app in `app.py`.
- One module per command in `commands/`; import `app` from `{name}.app`.

```text
{project-root}/
├─ pyproject.toml
└─ src/
   └─ {name}/
      ├─ __init__.py
      ├─ app.py
      ├─ main.py
      ├─ commands/
      │  ├─ __init__.py
      │  └─ {command}.py
      ├─ core/
      │  ├─ {concern}.py
      │  └─ {other_concern}.py
      └─ utils/
         └─ {concern}.py
```

`src/{name}/app.py`:

```python
import typer

app = typer.Typer()
```

`src/{name}/commands/{command}.py`:

```python
from {name}.app import app
from {name}.core.{concern} import {concern}
from {name}.core.{other_concern} import {other_concern}


@app.command()
def {command}() -> None:
    {concern}()
    {other_concern}()
```

## QtPy MVP App

- Use the Python `src/{name}` layout.
- Import Qt through QtPy; use PySide6 as the backend.
- MVP: `models/`, passive `views/`, `presenters/` connect them.

```text
{project-root}/
├─ pyproject.toml
└─ src/
   └─ {name}/
      ├─ __init__.py
      ├─ main.py
      ├─ models/
      │  └─ {feature}_model.py
      ├─ presenters/
      │  └─ {feature}_presenter.py
      └─ views/
         └─ {feature}_view.py
```

`src/{name}/main.py`:

```python
import sys

from qtpy.QtWidgets import QApplication

from {name}.models.{feature}_model import {Feature}Model
from {name}.presenters.{feature}_presenter import {Feature}Presenter
from {name}.views.{feature}_view import {Feature}View


def run() -> None:
    app = QApplication(sys.argv)
    view = {Feature}View()
    _ = {Feature}Presenter(view, {Feature}Model())
    view.show()
    app.exec()
```

`src/{name}/models/{feature}_model.py`:

```python
from dataclasses import dataclass


@dataclass
class {Feature}Model:
    title: str = ""
```

`src/{name}/views/{feature}_view.py`:

```python
from qtpy.QtWidgets import QMainWindow


class {Feature}View(QMainWindow):
    def set_title(self, title: str) -> None:
        self.setWindowTitle(title)
```

`src/{name}/presenters/{feature}_presenter.py`:

```python
from {name}.models.{feature}_model import {Feature}Model
from {name}.views.{feature}_view import {Feature}View


class {Feature}Presenter:
    def __init__(self, view: {Feature}View, model: {Feature}Model) -> None:
        view.set_title(model.title)
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
