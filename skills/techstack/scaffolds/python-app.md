# Python App

- Use `src/{name}` layout.
- Use Hatchling.
- Keep package behavior out of `__init__.py`.
- Enforce Ruff + ty in `pyproject.toml` — see `[tool.ruff]` / `[tool.ty]` / `[tool.uv]` below.
- Seven-day PyPI release age via `[tool.uv] exclude-newer` — see [../domains/security.md](../domains/security.md).

```text
{project-root}/
├─ pyproject.toml
└─ src/
   └─ {name}/
      └─ __init__.py
```

```toml
[build-system]
requires = ["hatchling>=1.21.0"]
build-backend = "hatchling.build"

[project]
name = "{project-name}"
version = "0.1.0"
requires-python = ">=3.10"
dependencies = []

[dependency-groups]
dev = ["ruff>=0.9", "ty>=0.0.1"]

[tool.ruff]
target-version = "py310"
line-length = 88

[tool.ruff.lint]
select = ["E", "F", "I", "UP", "B", "TID252"]

[tool.ruff.lint.flake8-tidy-imports]
ban-relative-imports = "parents"

[tool.ty.environment]
python-version = "3.12"

[tool.ty.rules]
all = "error"

[tool.uv]
exclude-newer = "7 days"
```
