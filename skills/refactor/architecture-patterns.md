# Architecture Patterns

Reference for Phase 1 diagnosis. Use to compare **stated or implied intent** vs **actual dependency flow**.

Do not impose a pattern the project never chose. First infer the target pattern from docs, folder layout, framework defaults, or user input; then record mismatches as findings.

## How to use during diagnosis

1. Record the **intended pattern** (or hybrid) in diagnosis **Meta**.
2. Map major folders or modules to pattern roles.
3. Trace dependency direction between roles.
4. File a finding when code breaks the pattern's boundaries or dependency rules.

## MVC — Model-View-Controller

| Role | Owns |
|------|------|
| **Model** | Domain state, rules, persistence coordination |
| **View** | Rendering and user-visible layout |
| **Controller** | Input handling, orchestration, model ↔ view coordination |

**Dependency rule:** View and Controller depend on Model abstractions; Model does not depend on View or Controller.

**Common violations**

- View or template contains business rules or direct persistence calls
- Controller grows domain logic instead of delegating to Model
- Model imports presentation or transport types

**Typical in:** server-rendered web apps, desktop toolkits, early mobile stacks

## MVVM — Model-View-ViewModel

| Role | Owns |
|------|------|
| **Model** | Domain and data access |
| **View** | Visual structure; declarative when possible |
| **ViewModel** | Presentation state, commands, formatting for the View |

**Dependency rule:** View binds to ViewModel; ViewModel depends on Model; Model is unaware of View/ViewModel.

**Common violations**

- ViewModel contains layout-specific logic or direct UI framework references everywhere
- View reaches around ViewModel into Model
- Two-way binding causes Model or persistence updates on every keystroke without an explicit commit boundary

**Typical in:** WPF, Avalonia, SwiftUI-like binding stacks, reactive UI frameworks

## MVP — Model-View-Presenter

| Role | Owns |
|------|------|
| **Model** | Domain and data |
| **View** | Passive display and input surface |
| **Presenter** | Presentation logic; drives the View |

**Dependency rule:** Presenter depends on View interface and Model; View is passive and knows Presenter only through a narrow contract.

**Common violations**

- View initiates workflows or mutates Model directly
- Presenter becomes a god object for unrelated screens
- No view contract — concrete UI types leak into Presenter

**Typical in:** older Android, some test-heavy UI codebases, widget toolkits favoring explicit presenters

## Layered — N-tier

| Layer | Owns |
|-------|------|
| **Presentation** | Delivery mechanism — HTTP, CLI, UI, messages |
| **Application / use case** | Workflows, coordination, transaction boundaries |
| **Domain** | Entities, invariants, domain services |
| **Infrastructure** | Persistence, messaging, external APIs, filesystem |

**Dependency rule:** Outer layers depend inward; Domain has no outward dependencies.

**Common violations**

- Domain imports database drivers, HTTP clients, or UI types
- Application layer skipped — presentation calls infrastructure directly
- Infrastructure types appear in public domain contracts

**Typical in:** enterprise services, CRUD backends, modular monoliths

## Hexagonal — Ports and Adapters

| Role | Owns |
|------|------|
| **Core / domain** | Business rules independent of delivery and infrastructure |
| **Ports** | Interfaces the core needs or exposes |
| **Adapters** | Concrete implementations for UI, persistence, queues, third parties |

**Dependency rule:** Adapters depend on ports and core; core depends on nothing outside itself except port abstractions it defines.

**Common violations**

- Adapters imported directly from core instead of through ports
- Ports shaped like a specific database or framework API
- Multiple unrelated adapters wired inside core

**Typical in:** services needing swappable infrastructure, test-heavy domains, DDD-style backends

## Clean / Onion Architecture

| Ring | Owns |
|------|------|
| **Entities** | Core business objects and rules |
| **Use cases** | Application-specific workflows |
| **Interface adapters** | Controllers, presenters, gateways, mappers |
| **Frameworks / drivers** | DB, web framework, device APIs |

**Dependency rule:** Dependencies point inward; inner rings define interfaces outer rings implement.

**Common violations**

- Entities depend on ORM or web framework annotations throughout
- Use cases return transport DTOs owned by outer layers
- Framework configuration scattered inside entity modules

**Typical in:** long-lived backends, DDD projects, microservice boundaries with a strong domain core

## Component-based / modular monolith

| Unit | Owns |
|------|------|
| **Feature module** | One bounded capability end-to-end within its boundary |
| **Shared kernel** | Cross-cutting contracts only — not a dumping ground |
| **Platform / bootstrap** | Composition, config, shared runtime wiring |

**Dependency rule:** Features depend on shared kernel and platform abstractions; features do not depend on each other's internals.

**Common violations**

- Feature A imports Feature B's internal folders
- Shared kernel accumulates single-consumer helpers
- Bootstrap knows every feature's internals instead of registration hooks

**Typical in:** large single-repo apps, plugin systems, vertical-slice layouts

## Pattern mismatch signals (any pattern)

| Signal | Likely diagnosis |
|--------|------------------|
| Role names in docs but code crosses boundaries freely | **Pattern drift** — document actual vs intended |
| Two patterns in one feature with no documented split | **Hybrid without boundaries** |
| Pattern applied only in one folder tree | **Partial adoption** |
| Framework default pattern overridden ad hoc | **Accidental architecture** |

Record pattern-related findings with: intended pattern, actual flow, locations, and whether the fix is alignment or explicit pattern change (decision for `TARGET_TREE.md`, not diagnosis).
