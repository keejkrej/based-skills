# QtPy MVVM Migration

Use during **refactor** Phase 3 (Separate) and Phase 4 (Pattern align). Behavior-preserving unless the user approved otherwise.

## God widget / window split

When one `QWidget` or `QMainWindow` subclass owns layout, state, rules, and I/O:

1. **Model** — extract dataclasses, validation, calculations, load/save logic (Qt-free plain Python).
2. **ViewModel** — extract `QObject` with signals/properties; hold model; expose `load()`, `save()`, error/busy signals.
3. **View** — keep widget tree, layouts, `.ui` load; shrink to connect viewmodel and forward clicks/edits.
4. Delete duplicated state from the old monolith after wiring.

Do not rename public behavior in the same slice as the physical split.

## Mixed file → triplet

| Code in mixed file | Target |
|--------------------|--------|
| `@dataclass`, enums, pure functions | `models/{feature}_model.py` |
| File/network/DB access | `models/{feature}_repository.py` or `models/services/` (Qt-free) |
| `QObject`, `Signal`, `Property`, command methods | `viewmodels/{feature}_viewmodel.py` |
| `QWidget` subclasses, layouts, styles | `views/{feature}_view.py` |
| `QUiLoader` / `.ui` load | `views/{feature}_view.py` + `views/ui/{feature}.ui` |

## Logic in `main.py`

| Code in main | Target |
|--------------|--------|
| Domain setup, config parsing | model or small bootstrap helper in `models/` |
| Feature state and commands | viewmodel |
| Window construction | view |
| Leave in main | `QApplication`, construct graph, `show()`, `exec()` |

## Qt Designer `.ui` files

- Store under `views/ui/{feature}.ui`.
- Load from `{Feature}View` only.
- Connect widgets to viewmodel in the view class (`connect` calls or small private `_bind()` method).
- Do not add slots with business logic in the generated UI class; prefer viewmodel methods.

## Threads and long work

- Worker and I/O logic stays **Qt-free** in `models/` or `models/services/` (plain Python, stdlib `threading`/`concurrent.futures`, or blocking calls invoked from a worker thread).
- ViewModel owns `QThread`, `QRunnable`, or `moveToThread` wiring and exposes progress/completion/error **signals**.
- View shows progress UI; does not run blocking I/O on the GUI thread.
- Do not pass widgets into workers.
- Do not put `QObject` or `Signal` in the model to report progress — viewmodel polls, awaits, or receives callbacks and re-emits Qt signals.

## Qt-backed vendor SDKs

- Keep domain rules and protocols Qt-free in `models/`.
- Place Qt-required vendor glue in `adapters/` or `infrastructure/` outside `models/`.
- ViewModel or `main.py` composes adapter + model; view still never imports model directly.

## Dialogs

- **Simple confirmation** (yes/no on an action): view shows `QMessageBox`; result forwarded to viewmodel method.
- **Dialog with domain state** (forms, wizards): separate `{feature}_dialog_view.py` + `{feature}_dialog_viewmodel.py` or sub-viewmodel; still no view→model imports.

## Multiple windows / same feature

- One viewmodel can feed multiple views if the workflow is shared.
- Prefer one primary `{feature}_view.py` plus smaller view partials colocated under `views/` — not separate viewmodels per panel unless lifecycle differs.

## Cross-view / cross-feature wiring

- Prefer **signal connections** wired in `main.py` or through a coordinator/facade viewmodel — not direct calls between views or viewmodels.
- Domain coordination: shared model/service API; each viewmodel emits to its own view.
- Presentation coordination: coordinator viewmodel signals that multiple views/viewmodels connect to.
- Red flag: `other_view.refresh()`, `other_viewmodel.load(...)`, or cross-feature view/viewmodel imports.

## Repositories and test seams

- Define persistence behind a plain Python protocol or ABC in `models/`.
- Implement the protocol with Qt-free code in `models/` when possible.
- ViewModel depends on the protocol; concrete repository stays in `models/`.
- Models and model services are unit-testable without `QApplication`.

## Anti-patterns while migrating

- Do not leave `from ...models import` inside views “temporarily” — use viewmodel surface instead.
- Do not move widget code into viewmodel to “fix” imports.
- Do not re-export models from viewmodels for view convenience.
- Do not add `QObject` or `Signal` to models for convenience — use viewmodel signals.
- Do not skip Phase 4 contracts and wire ad hoc in Phase 5.

## Slice sizing

One refactor slice = one feature triplet or one god-widget split. Record broken imports in `PROGRESS.md`; fix in Phases 4–5 per **refactor** [separation-tactics.md](../refactor/separation-tactics.md).
