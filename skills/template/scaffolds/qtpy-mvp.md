# QtPy MVP App

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
