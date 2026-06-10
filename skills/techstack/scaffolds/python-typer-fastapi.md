# Python Typer + FastAPI App

- Use the Python `src/{name}` layout.
- Default hybrid layout; scaffold **CLI only**, **API only**, or **both** — keep `services/` and `core/` either way.
- Typer in `app.py`; FastAPI in `api.py`; `main.py` imports `commands/`, `api.py` imports `routes/`.
- `services/` orchestrates `core/`; `commands/` and `routes/` are thin adapters into the same service.
- One module per use case in `services/`; add matching modules in `commands/` and/or `routes/` for each surface.
- Import `app` or `api` in adapters; register with `@app.command()` or `@api.post()` — no routers.
- Enforce Ruff + ty in `pyproject.toml` — same `[tool.ruff]` / `[tool.ty]` blocks as [python-app.md](python-app.md).

```text
{project-root}/
├─ pyproject.toml
└─ src/
   └─ {name}/
      ├─ __init__.py
      ├─ app.py
      ├─ api.py
      ├─ main.py
      ├─ commands/
      │  ├─ __init__.py
      │  └─ {command}.py
      ├─ routes/
      │  ├─ __init__.py
      │  └─ {command}.py
      ├─ services/
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

`src/{name}/api.py`:

```python
from fastapi import FastAPI

api = FastAPI()

from {name} import routes  # noqa: F401
```

`src/{name}/services/{command}.py`:

```python
from {name}.core.{concern} import {concern}
from {name}.core.{other_concern} import {other_concern}


def run_{command}() -> None:
    {concern}()
    {other_concern}()
```

`src/{name}/commands/{command}.py`:

```python
from {name}.app import app
from {name}.services.{command} import run_{command}


@app.command()
def {command}() -> None:
    run_{command}()
```

`src/{name}/routes/{command}.py`:

```python
from {name}.api import api
from {name}.services.{command} import run_{command}


@api.post("/{command}")
def {command}() -> None:
    run_{command}()
```
