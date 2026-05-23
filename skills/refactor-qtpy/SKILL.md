---
name: refactor-qtpy
description: >-
  Defines canonical QtPy MVVM layout and layer boundaries for Python desktop
  refactors: Qt-free models/*_model.py, viewmodels/*_viewmodel.py,
  views/*_view.py. Use with refactor when restructuring QtPy, PySide6, PyQt5, or PyQt6 codebases,
  splitting god widgets, or aligning presentation code to MVVM.
---

# Refactor QtPy

QtPy MVVM target structure for **refactor** Phases 1–5. Use **refactor** for the six-phase workflow and artifacts. Use **template** (Python PySide6 MVVM App) for project tree, `pyproject.toml`, and copy-paste file templates. Use **bestpractice** for generic cohesion rules inside each layer.

## Intended pattern

- Pattern: **MVVM** via QtPy.
- Dependency rule: **View → ViewModel → Model**; model unaware of presentation.
- Import Qt through **qtpy** in viewmodels and views only; see **Qt-free models** below.

## Target layout

One feature triplet under `src/{package}/`: `models/{feature}_model.py`, `viewmodels/{feature}_viewmodel.py`, `views/{feature}_view.py`. Composition in `main.py`.

Refactor-only additions (not in template scaffold):

- `models/{feature}_repository.py` or `models/services/` for heavy I/O (Qt-free)
- `adapters/` or `infrastructure/` for Qt-backed vendor SDKs (outside `models/`)
- `views/ui/{feature}.ui` when using Qt Designer

Full tree, Hatchling setup, and greenfield templates: **template** § Python PySide6 MVVM App.

## Qt-free models

- **`models/` is Qt-free.** No `qtpy`, `PySide6`, or `PyQt` imports anywhere under `models/`.
- Models use plain Python: dataclasses, enums, protocols, exceptions, return values, callbacks, or stdlib concurrency — not `QObject`, `Signal`, or `Property`.
- The **viewmodel** is the only layer that turns domain changes into Qt signals/properties for the view.
- After a model method runs, the viewmodel reads new state (or handles a callback/return value) and emits UI signals.
- Vendor SDKs that require Qt belong in an adapter **outside** `models/` (e.g. `adapters/`, `infrastructure/`); inject them into viewmodels or Qt-free protocols implemented without Qt in `models/`.

## Layer boundaries

### Model (`models/*_model.py`, `models/services/`, `models/*_repository.py`)

**Owns:** domain types, validation, transforms, persistence interfaces, pure business rules, plain-Python I/O and worker logic.

**May import:** stdlib, typing, dataclasses, pydantic, protocol/ABC types, non-Qt third-party libraries.

**Must not:** import `qtpy`, `PySide6`, `PyQt5`, `PyQt6`, or any Qt module; subclass `QObject`; define or emit signals; import viewmodels or views.

### ViewModel (`viewmodels/*_viewmodel.py`)

**Owns:** presentation state, commands/actions for the UI, `QObject` signals and properties, mapping model state/errors to UI-facing signals, Qt thread/worker wiring, orchestration of async results back to the UI.

**May import:** `qtpy.QtCore` (`QObject`, `Signal`, `Property`, `Slot`, timers when presentation-scoped), models, model services/repositories.

**Must not:** construct layouts, import widgets, show dialogs, or import views.

**Expose:** bindable properties and signals; callable methods/slots for user actions (`load`, `save`, `apply`, `cancel`).

### View (`views/*_view.py`)

**Owns:** widgets, layouts, `.ui` loading, visual styling, connecting viewmodel signals to widget slots, forwarding user input to viewmodel methods.

**May import:** `qtpy.QtWidgets`, `qtpy.QtGui`, `qtpy.uic` / `QUiLoader`, viewmodels only.

**Must not:** import models; embed business rules, file I/O, network calls, or persistence.

**Local UI state OK:** hover, focus, open/closed sub-widgets, drag preview, draft text before commit to viewmodel.

### Composition root (`main.py`)

- Composition only: `QApplication`, construct models → viewmodels → views, `show()`, `exec()`.
- Must not hold domain rules, widget layout, or feature logic.
- Wiring shape: **template** `main.py` template.

## Dependency matrix

| Importer → | Model | ViewModel | View |
|------------|-------|-----------|------|
| Model | — | no | no |
| ViewModel | yes | — | no |
| View | no | yes | — |
| main.py | yes (construct) | yes (construct) | yes (construct) |

- ViewModel must never import View.
- View must never import Model.
- Cross-feature calls go through model/service APIs or shared viewmodel facades — not view-to-view imports.

## Phase hooks (with refactor)

| Phase | Use this skill for |
|-------|-------------------|
| 1 Diagnose | Record intended pattern MVVM; file QtPy boundary violations (see checklist below) |
| 2 Target tree | Map current modules → target paths above; note `{feature}` ownership |
| 3 Separate | Physical moves to target paths only; stubs OK |
| 4 Pattern align | Evict wrong-layer code; publish viewmodel public signals/methods; model stays Qt-free |
| 5 Wire | `main.py` composition; view ↔ viewmodel signal connections; inject models into viewmodels |
| 6 Verify | Import lint mentally: no view→model; build/run smoke test |

## Phase 1 violation checklist

Record path-level evidence for each hit:

- View or widget class imports from `models/`
- Any file under `models/` imports `qtpy`, `PySide6`, `PyQt5`, `PyQt6`, or defines `QObject`/`Signal`
- Model emits signals or exposes Qt types to notify the UI
- Business rules, validation, or persistence inside a view or widget subclass
- `QMessageBox`, file dialogs, or network I/O in viewmodel without a model/service delegate
- ViewModel imports a view or builds widgets
- God widget/window mixing layout, state, domain rules, and I/O in one class
- Signal wiring scattered in `main.py` instead of view or viewmodel edge
- Feature logic in `main.py` beyond composition
- Direct PySide6/PyQt imports instead of qtpy (note for normalization)

Generic structural smells: still use **refactor** [anti-patterns.md](../refactor/anti-patterns.md).

## Phase 4 contracts

Publish at the viewmodel edge before Phase 5 wiring:

- **Signals** the view subscribes to (state + errors + busy)
- **Properties** or getters the view reads for initial bind
- **Methods/slots** the view calls for user actions
- **Constructor inputs** (model or service dependencies)

Views connect only to that surface; do not reach into viewmodel private `_model` fields from the view.

## Phase 5 wiring order

1. Models and model services (no outward presentation deps)
2. ViewModels (construct with models; define signals/properties)
3. Views (accept viewmodel in `__init__`; connect signals)
4. `main.py` last (composition root)

Match **refactor** [wiring.md](../refactor/wiring.md) walk order mapped to MVVM layers.

## Input commit (sliders, spinboxes, filters)

- **Immediate commit:** every tick should update viewmodel/model and trigger expensive work — connect directly to viewmodel setter.
- **Delayed commit:** local draft in view during drag/type; commit on Enter, focus out, debounce, or Apply — use view-local draft + explicit `commit()` on viewmodel.
- Preserve existing commit semantics during refactor unless behavior change is approved.

## More detail

- Move recipes and mixed-file splits: [migration.md](migration.md)
- God-widget split walkthrough and import anti-patterns: [examples.md](examples.md)
