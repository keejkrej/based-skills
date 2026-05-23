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
- **Logic ownership:** the **model owns most logic** — domain rules, validation, transforms, persistence, and worker/I/O behavior. The viewmodel is a thin presentation adapter (Qt signals/properties, UI state shape, command routing, thread glue); it maps model state to the UI and forwards user actions to the model — it does not reimplement business rules. The view owns display and input routing only.
- **Signal-first binding:** viewmodel → view updates flow through **Qt signals** (and properties where appropriate), not direct view method calls or post-action getter polling. See **Signal-based wiring** below.
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

**Owns:** presentation state, commands/actions for the UI, `QObject` signals and properties, mapping model state/errors to UI-facing signals, Qt thread/worker wiring, orchestration of async results back to the UI. Keep thin: delegate domain work to the model; do not duplicate validation, transforms, or persistence logic here.

**May import:** `qtpy.QtCore` (`QObject`, `Signal`, `Property`, `Slot`, timers when presentation-scoped), models, model services/repositories.

**Expose:** bindable properties and signals; callable methods/slots for user actions (`load`, `save`, `apply`, `cancel`). Push state **out** via signals — never call into the view.

**Must not:** hold a view reference; invoke view methods (`refresh`, `set_values`, `show_error`); construct layouts, import widgets, show dialogs, or import views.

### View (`views/*_view.py`)

**Owns:** widgets, layouts, `.ui` loading, visual styling, connecting viewmodel **signals → widget slots**, forwarding user input via **widget signals → viewmodel slots/methods**.

**May import:** `qtpy.QtWidgets`, `qtpy.QtGui`, `qtpy.uic` / `QUiLoader`, viewmodels only.

**Must not:** import models; embed business rules, file I/O, network calls, or persistence; poll viewmodel getters after every action instead of subscribing to viewmodel signals; accept callbacks from viewmodel that mutate widgets directly.

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
- **Cross-view / cross-feature:** prefer **Qt signals** wired at composition time (`main.py` or a coordinator viewmodel) — not direct calls between views or viewmodels. Domain coordination goes through model/service APIs; presentation coordination goes through shared or coordinator viewmodel signals. See **Cross-view communication** below.

## Signal-based wiring

Prefer **Qt signal/slot connections** over imperative cross-layer calls.

| Direction | Preferred | Avoid |
|-----------|-----------|-------|
| ViewModel → View | Emit signals (`state_changed`, `error_occurred`, `busy_changed`); view connects once in `_bind_viewmodel` | ViewModel stores `self._view` and calls `self._view.set_values(...)` / `refresh()` |
| View → ViewModel | Connect widget signals to viewmodel `@Slot`s or published command methods | View reaches into `viewmodel._model`; viewmodel synchronously drives widget state as return value of a command |
| Initial bind | Read viewmodel properties/getters once at connect time; then rely on signals | Re-call getters after every button click to repaint the whole form |
| Errors / toasts | ViewModel emits `error_occurred(str)`; view shows `QMessageBox` or status bar | ViewModel imports widgets and shows dialogs |

- Wire view ↔ viewmodel connections in the **view** (or a dedicated `_bind_viewmodel` method), not scattered in `main.py`.
- ViewModel command methods update the model, then **emit** — they do not push UI updates themselves.

## Cross-view communication

When one UI area must react to another (open a panel, sync selection, refresh a list):

- **Prefer signals.** Source emits; target connects in `main.py`, its own `_bind_*`, or via a shared/coordinator viewmodel both sides observe.
- **Domain changes:** feature A's viewmodel updates a shared model/service; feature B's viewmodel reacts and emits its own signals to its view.
- **Presentation-only:** use a coordinator or facade viewmodel with signals both features connect to — not view→view or viewmodel→viewmodel method calls.

| Preferred | Avoid |
|-----------|-------|
| `coordinator.selection_changed.connect(list_viewmodel.load_for_selection)` in `main.py` | `self._other_view.refresh()` from a view |
| `shared_model` change → viewmodel B emits → view B updates | `self._other_viewmodel.load(id)` from a view or foreign viewmodel |
| Viewmodel A emits `item_open_requested`; viewmodel B slot handles it | View imports another feature's view or viewmodel |

Views must not import other views or foreign viewmodels. Viewmodels must not call methods on other viewmodels directly — connect signals or go through model/service APIs.

## Red-flag fingerprints

Phase 1 size and wiring smells — record path + metric:

| Fingerprint | What it usually means |
|-------------|-------------------------|
| **View dominates triplet size** — `{feature}_view.py` LOC is much larger than `{feature}_viewmodel.py` and `{feature}_model.py` (rule of thumb: view alone > ~60% of triplet LOC, or view > ~2× the larger of model/viewmodel) | Logic, validation, I/O, or orchestration leaked into the widget; candidate for god-widget split |
| ViewModel calls view methods or holds a view reference | Layer inversion; replace with viewmodel signals |
| View polls viewmodel getters after every user action instead of signal subscriptions | Missing viewmodel change signals; tight coupling |
| Large `_on_*` handlers in view with parsing, validation, or file/network calls | Belongs in model; view should forward and react |
| ViewModel thicker than model for the same feature | Logic drifted up from model |
| View or viewmodel **directly calls** another view or foreign viewmodel (`other_view.open()`, `other_vm.load()`) | Missing signal/coordinator wiring; feature coupling |
| View imports another feature's view or viewmodel | Cross-feature edge violation; wire via signals in `main.py` or shared viewmodel |

Compare LOC per `{feature}` triplet during Phase 1; line count is a heuristic, not a hard rule — a layout-heavy `.ui`-backed view may be large without violation if it contains little Python logic.

**Re-check during Phases 3–5:** run the violation checklist and red-flag fingerprints on each new/refactored triplet before using it as the pattern for the next feature. Update Phase 1 findings when a **structural** smell appears — do not replicate a bad split across all components.

**Defer OK in Phase 3:** copy-paste between files, mixin/glue helpers, duplicate widget binding code, or temporary stubs — when dedupe or polish would risk correctness during the move. Record deferred items; resolve in Phases 4–5. Do **not** defer layer violations (view→model, viewmodel→view calls, cross-view direct calls).

## Phase hooks (with refactor)

| Phase | Use this skill for |
|-------|-------------------|
| 1 Diagnose | Record intended pattern MVVM; file QtPy boundary violations (see checklist below) |
| 2 Target tree | Map current modules → target paths above; note `{feature}` ownership |
| 3 Separate | Physical moves to target paths only; stubs OK; **re-check Phase 1–2 after each slice** |
| 4 Pattern align | Evict wrong-layer code; publish viewmodel public signals/methods; model stays Qt-free; **update DIAGNOSIS / TARGET_TREE if roles drift** |
| 5 Wire | `main.py` composition; view ↔ viewmodel signal connections; inject models into viewmodels; **stop and steer if cross-view direct calls appear** |
| 6 Verify | Import lint mentally: no view→model; build/run smoke test; confirm Phase 1 findings resolved or deferred |

## Phase 1 violation checklist

Record path-level evidence for each hit:

- View or widget class imports from `models/`
- Any file under `models/` imports `qtpy`, `PySide6`, `PyQt5`, `PyQt6`, or defines `QObject`/`Signal`
- Model emits signals or exposes Qt types to notify the UI
- Business rules, validation, or persistence inside a view or widget subclass
- Business rules, validation, transforms, or persistence in viewmodel instead of model (viewmodel should delegate)
- ViewModel thicker than model for the same feature (logic drifted up from model)
- **View dominates triplet size** — view LOC ≫ viewmodel + model (see **Red-flag fingerprints**)
- ViewModel holds view reference or calls view methods (`refresh`, `set_*`, `show_*`) instead of emitting signals
- View polls viewmodel getters after actions instead of connecting to viewmodel change signals
- View or viewmodel directly calls another view or foreign viewmodel instead of signal wiring (see **Cross-view communication**)
- View imports another feature's view or viewmodel
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

**Signal-first:** list the signals the view will connect to for ongoing updates — not imperative callbacks or view methods the viewmodel will invoke.

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
