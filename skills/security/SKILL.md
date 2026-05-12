---
name: security
description: >-
  Enforces a rolling seven-day minimum release age for registry packages to
  reduce npm and PyPI supply-chain risk (compromised releases are often pulled
  quickly). Covers npm, pnpm, Bun, and uv with the correct file, key, and
  units for each tool. Use when hardening installs, onboarding package
  managers, reviewing CI, or when the user mentions supply chain, minimum
  release age, or release-age gating.
---

# Security: minimum release age (7 days)

Apply a **seven-day** maturity window so installs only resolve package versions that have been published long enough for ecosystem signals and takedowns to surface. **Each tool uses different units and files**—copy the snippets below literally for a 7-day policy.

| Tool | File | Setting | 7-day value |
|------|------|---------|-------------|
| [npm](https://docs.npmjs.com/cli/using-npm/config#min-release-age) | `.npmrc` (project or user) | `min-release-age` | **days** → `7` |
| [pnpm](https://pnpm.io/settings#minimumreleaseage) | `pnpm-workspace.yaml` | `minimumReleaseAge` | **minutes** → `10080` |
| [Bun](https://bun.sh/docs/runtime/bunfig#installminimumreleaseage) | `bunfig.toml` under `[install]` | `minimumReleaseAge` | **seconds** → `604800` |
| [uv](https://docs.astral.sh/uv/reference/settings/#exclude-newer) | `pyproject.toml` `[tool.uv]` or `uv.toml` | `exclude-newer` | duration → `"7 days"` (or `"1 week"`, `P7D`) |

**Constants:** 7 days = `10080` minutes = `604800` seconds.

## npm

Add to **`.npmrc`** (commit at repo root for team alignment, or set per-user for global behavior):

```ini
min-release-age=7
```

Do **not** combine `min-release-age` with `before`—[npm treats them as mutually exclusive](https://docs.npmjs.com/cli/using-npm/config#min-release-age).

Optional: investigate current npm docs for an exclude list if you must allow specific packages to bypass the policy (e.g. internal scopes).

## pnpm

As of **pnpm 11.x**, non-auth settings belong in **`pnpm-workspace.yaml`**, not `.npmrc` ([settings](https://pnpm.io/settings)).

```yaml
minimumReleaseAge: 10080
```

If the repo has **no** workspace file yet, add a minimal root `pnpm-workspace.yaml` that includes this package (e.g. `packages: ['.']` for a single-package repo) plus `minimumReleaseAge`, or merge `minimumReleaseAge` into an existing file.

Use **`minimumReleaseAgeExclude`** (and patterns like `'@org/*'`) only when a dependency must install fresher than seven days. Prefer fixing version ranges or waiting for maturity instead of broad exclusions.

On **pnpm before v11**, older docs allowed `minimum-release-age` in `.npmrc` (minutes); migrate to `pnpm-workspace.yaml` when upgrading.

## Bun

In **`bunfig.toml`** at the project root (or user-level [`.bunfig.toml`](https://bun.sh/docs/runtime/bunfig)):

```toml
[install]
minimumReleaseAge = 604800
```

Use **`minimumReleaseAgeExcludes`** for named exceptions when required.

## uv (PyPI)

Set **`exclude-newer`** so resolution only considers distributions **uploaded before** the implied cutoff; a friendly duration is interpreted relative to resolve time ([uv settings](https://docs.astral.sh/uv/reference/settings/#exclude-newer)).

In **`pyproject.toml`**:

```toml
[tool.uv]
exclude-newer = "7 days"
```

Or in **`uv.toml`** at the project root:

```toml
exclude-newer = "7 days"
```

Per-package overrides: **`exclude-newer-package`** (e.g. exempt an internal package with `false` or a fixed timestamp).

## Agent workflow

1. Detect which managers the repo uses (`package.json` + lockfiles, `bunfig.toml`, `pnpm-workspace.yaml`, `pyproject.toml` / `uv.toml`).
2. Add or update only the relevant blocks; keep auth tokens and secrets out of committed `.npmrc`—use env vars or ignored local files for `_authToken`.
3. After enabling strict gating, run a normal **`install` / `sync` / `lock`** in CI or locally and fix version ranges or exclusions if resolution fails—failures often mean every candidate in range is younger than seven days.
4. Document the policy in the PR or contributor notes so teammates expect slower uptake of brand-new releases.

## Limitations

- Age gating helps with **registry tarball** attacks; it does not replace lockfile review, `ignore-scripts` / trusted-deps policies, private registry controls, or secret scanning.
- **Git / tarball / path** dependencies may bypass or interact oddly with npm age rules—see upstream release notes when mixing sources.
