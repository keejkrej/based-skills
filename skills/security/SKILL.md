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

# Security: Minimum Release Age

Apply a seven-day registry package maturity window by default.

## Constants

- Use seven days.
- Convert as needed: `7` days = `10080` minutes = `604800` seconds.
- Add only the package managers the repo actually uses.
- Keep auth tokens and secrets out of committed config.

## Settings

| Tool | File | Key | Seven-day value |
|------|------|-----|-----------------|
| Bun | `bunfig.toml` `[install]` | `minimumReleaseAge` | `604800` |
| npm | `.npmrc` | `min-release-age` | `7` |
| pnpm | `pnpm-workspace.yaml` | `minimumReleaseAge` | `10080` |
| uv | `pyproject.toml` `[tool.uv]` or `uv.toml` | `exclude-newer` | `"7 days"` |

## Bun

- Add root `bunfig.toml`, or update the existing file.
- Use `minimumReleaseAgeExcludes` only for narrow exceptions.

```toml
[install]
minimumReleaseAge = 604800
```

Docs: https://bun.sh/docs/runtime/bunfig#installminimumreleaseage

## npm

- Add project-level `.npmrc` for team policy.
- Do not combine `min-release-age` with `before`.
- Use exclusions only for narrowly justified packages.

```ini
min-release-age=7
```

Docs: https://docs.npmjs.com/cli/using-npm/config#min-release-age

## pnpm

- For pnpm 11.x, put non-auth settings in `pnpm-workspace.yaml`.
- If no workspace file exists, add a minimal one and include the current package.
- Use `minimumReleaseAgeExclude` only for narrow exceptions such as internal scopes.
- For older pnpm repos, migrate old `.npmrc` age settings when upgrading.

```yaml
minimumReleaseAge: 10080
```

Docs: https://pnpm.io/settings#minimumreleaseage

## uv

- Set `exclude-newer` for PyPI resolution.
- Prefer `"7 days"` for readability.
- Use `exclude-newer-package` only for narrow per-package overrides.

```toml
[tool.uv]
exclude-newer = "7 days"
```

```toml
exclude-newer = "7 days"
```

Docs: https://docs.astral.sh/uv/reference/settings/#exclude-newer

## Workflow

- Detect managers from lockfiles and config: `bun.lock`, `bunfig.toml`, `package.json`, other lockfiles, `pnpm-workspace.yaml`, `pyproject.toml`, `uv.toml`.
- Update only relevant files.
- Preserve existing formatting and unrelated settings.
- Run the normal install, sync, or lock command after changing policy.
- If resolution fails, tighten version ranges, wait for maturity, or add a narrow exclusion.
- Mention the seven-day policy in PR notes or contributor docs when appropriate.

## Limits

- Use age gating as one layer, not a replacement for lockfile review, script restrictions, private registry controls, or secret scanning.
- Check upstream behavior when using Git, tarball, path, or private registry dependencies.
