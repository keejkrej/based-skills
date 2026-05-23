# QtPy MVVM Examples

Refactor-oriented snippets only. File templates and target triplet code: **template** § Python PySide6 MVVM App.

## Before: god window

```python
# views/main_window.py  (mixed — violates MVVM)
from qtpy.QtWidgets import QMainWindow, QLineEdit, QPushButton, QMessageBox
from pathlib import Path


class MainWindow(QMainWindow):
    def __init__(self) -> None:
        super().__init__()
        self._path = Path("data.txt")
        self._edit = QLineEdit(self)
        self._btn = QPushButton("Save", self)
        self._btn.clicked.connect(self._save)
        self._edit.setText(self._path.read_text(encoding="utf-8") if self._path.exists() else "")

    def _save(self) -> None:
        text = self._edit.text().strip()
        if not text:
            QMessageBox.warning(self, "Save", "Text cannot be empty.")
            return
        self._path.write_text(text, encoding="utf-8")
        QMessageBox.information(self, "Save", "Saved.")
```

## After: where each piece goes

| Extract from god window | Target |
|-------------------------|--------|
| `Path`, read/write, validation (`if not text.strip()`) | `models/{feature}_model.py` (Qt-free) |
| `save_failed` / `save_succeeded`, `text_changed`, `save()` command | `viewmodels/{feature}_viewmodel.py` |
| `QLineEdit`, `QPushButton`, layouts, `QMessageBox` | `views/{feature}_view.py` |
| `QApplication`, construct graph | `main.py` |

Scaffold the target files from **template**; apply [migration.md](migration.md) for slice order.

## View must not import model

```python
# BAD — view layer violation
from myapp.models.document_model import DocumentModel

# GOOD — view receives viewmodel only
from myapp.viewmodels.document_viewmodel import DocumentViewModel
```

## ViewModel must not import widgets

```python
# BAD
from qtpy.QtWidgets import QMessageBox

class DocumentViewModel(QObject):
    def save(self) -> None:
        QMessageBox.information(None, "Save", "Saved.")

# GOOD — emit signal; view shows dialog
save_succeeded = Signal()
```

## ViewModel → View: signals, not direct calls

```python
# BAD — viewmodel drives the view imperatively
class DocumentViewModel(QObject):
    def __init__(self, model: DocumentModel, view: DocumentView) -> None:
        super().__init__()
        self._model = model
        self._view = view

    def load(self) -> None:
        self._model.load()
        self._view.set_text(self._model.text)  # direct call into view

# GOOD — viewmodel emits; view connected in _bind_viewmodel
class DocumentViewModel(QObject):
    text_changed = Signal(str)

    def load(self) -> None:
        self._model.load()
        self.text_changed.emit(self._model.text)
```

## View: connect once, do not poll after every action

```python
# BAD — poll getters after each click
def _on_save(self) -> None:
    self._viewmodel.save()
    self._edit.setText(self._viewmodel.text())  # should have been a signal

# GOOD — subscribe in _bind_viewmodel
def _bind_viewmodel(self) -> None:
    self._viewmodel.text_changed.connect(self._edit.setText)
    self._save_btn.clicked.connect(self._viewmodel.save)
```
