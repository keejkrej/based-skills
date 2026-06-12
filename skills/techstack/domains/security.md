# Supply chain

Seven-day minimum registry release age on greenfield repos — compromised releases are often pulled quickly.

## Defaults

- Apply on every greenfield scaffold and repo bootstrap — do not wait for a separate security pass
- Use seven days: `7` days = `10080` minutes = `604800` seconds
- Add only package managers the repo actually uses
- Keep auth tokens and secrets out of committed config
- Age gating is one layer — not a replacement for lockfile review, script restrictions, or secret scanning

## Settings

| Tool | File | Key | Seven-day value |
|------|------|-----|-----------------|
| Bun | `bunfig.toml` `[install]` | `minimumReleaseAge` | `604800` |
| npm | `.npmrc` | `min-release-age` | `7` |
| pnpm | `pnpm-workspace.yaml` | `minimumReleaseAge` | `10080` |
| uv | `pyproject.toml` `[tool.uv]` or `uv.toml` | `exclude-newer` | `"7 days"` |

## Bun

- Root `bunfig.toml` on greenfield Bun repos
- `minimumReleaseAgeExcludes` only for narrow exceptions

```toml
[install]
minimumReleaseAge = 604800
```

Docs: https://bun.sh/docs/runtime/bunfig#installminimumreleaseage

## npm

- Project-level `.npmrc` for team policy
- Do not combine `min-release-age` with `before`
- Exclusions only for narrowly justified packages

```ini
min-release-age=7
```

Docs: https://docs.npmjs.com/cli/using-npm/config#min-release-age

## pnpm

- pnpm 11.x: non-auth settings in `pnpm-workspace.yaml`
- No workspace file → minimal one including the current package
- `minimumReleaseAgeExclude` only for narrow exceptions (e.g. internal scopes)

```yaml
minimumReleaseAge: 10080
```

Docs: https://pnpm.io/settings#minimumreleaseage

## uv

- `exclude-newer` for PyPI resolution — prefer `"7 days"`
- `exclude-newer-package` only for narrow per-package overrides

```toml
[tool.uv]
exclude-newer = "7 days"
```

Docs: https://docs.astral.sh/uv/reference/settings/#exclude-newer

## Workflow

- Detect managers from lockfiles and config: `bun.lock`, `bunfig.toml`, `package.json`, `pnpm-workspace.yaml`, `pyproject.toml`, `uv.toml`
- Update only relevant files; preserve existing formatting and unrelated settings
- Run normal install/sync/lock after changing policy
- Resolution fails → tighten version ranges, wait for maturity, or add a narrow exclusion
- Mention the seven-day policy in PR notes when bootstrapping a repo

## Limits

- Check upstream behavior for Git, tarball, path, or private registry dependencies
