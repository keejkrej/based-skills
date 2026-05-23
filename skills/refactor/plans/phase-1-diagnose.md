# Phase 1 Plan Template

Copy into `.refactor/plans/phase-1-diagnose.md` before executing Phase 1.

```markdown
# Phase 1 — Diagnose

## Meta

- **Date:** [YYYY-MM-DD]
- **Author:** [agent or human]

## Scope

- **In:** [paths, apps, packages, features]
- **Out:** [excluded paths or concerns]
- **Stack:** [languages, frameworks, build tools]

## Scan checklist

- [ ] Entry points (main, servers, CLI, jobs, plugins)
- [ ] Largest units and highest fan-in modules
- [ ] Cross-boundary dependencies
- [ ] Duplication and contract drift
- [ ] Test layout vs production layout
- [ ] Package / module public boundaries
- [ ] Intended architecture inference (see architecture-patterns.md)
- [ ] Anti-pattern catalog pass (see anti-patterns.md)

## Outputs

- **Deliverable:** `.refactor/DIAGNOSIS.md`
- **Template:** diagnosis-template.md

## Exit criteria

- [ ] Every in-scope anti-pattern has a finding with evidence
- [ ] Findings ranked by severity
- [ ] Deferred items documented
- [ ] User confirmed scope and priorities (unless diagnose-only)

## Revision log

| Date | Change |
|------|--------|
| [YYYY-MM-DD] | Initial plan |
```
