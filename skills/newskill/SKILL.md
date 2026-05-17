---
name: newskill
description: >-
  Create or refactor Codex skills into a concise guideline style: short
  imperative bullets, clear trigger frontmatter, minimal body prose, preserved
  exact snippets where needed, and progressive disclosure through references.
  Use when writing a new skill, converting an existing skill to guideline style,
  reviewing SKILL.md clarity, or reducing verbose skill instructions.
---

# Newskill

Write skills as compact operating guidelines for another capable agent.

## Frontmatter

- Keep only required YAML fields unless the repo explicitly uses more.
- Set `name` to the skill folder name.
- Write `description` as the trigger surface.
- Include what the skill does and when to use it in `description`.
- Put trigger phrases and user contexts in `description`, not in a body "when to use" section.
- Keep the description broad enough to trigger reliably and specific enough to avoid unrelated tasks.

## Body Style

- Prefer short imperative bullets.
- Avoid long explanatory paragraphs.
- Assume the agent is competent; include only rules, constraints, decisions, and non-obvious workflow.
- Use headings as scan targets.
- Put the most commonly needed rules first.
- Preserve exact commands, config snippets, schemas, layouts, and constants when precision matters.
- Remove examples that only restate the rule.
- Keep examples that prevent likely mistakes.
- Avoid motivational prose, background essays, and implementation history.

## Structure

- Start with a one-line purpose statement.
- Organize by task phase, decision area, tool, or platform.
- Use flat lists over nested bullets.
- Use tables only when they compress precise mappings.
- Move long variant-specific material into referenced files.
- Link references directly from `SKILL.md`.
- Keep reference files one level deep.
- Do not add README, changelog, installation guide, or auxiliary docs unless they are actual skill resources.

## Content Rules

- Preserve all behavioral requirements from the original skill.
- Convert rationale into direct instructions.
- Convert warnings into explicit "do" or "do not" bullets.
- Keep exact file paths, env vars, package names, and command names.
- Keep safety constraints visible.
- Keep validation steps visible.
- Remove duplicated guidance.
- Do not invent new policy while refactoring style.

## Snippets

- Keep snippets minimal and copy-pasteable.
- Show only the fields needed for the rule.
- Prefer placeholders like `{name}` only when the user or task must substitute values.
- Do not wrap snippets in extra prose that repeats the snippet.
- Keep code fences labeled with the correct language.

## References

- Use bundled references for long docs, variant catalogs, schemas, and examples.
- Mention when to read each reference.
- Do not duplicate the same information in both `SKILL.md` and references.
- Add table of contents to reference files over 100 lines.

## Validation

- Validate the skill with `quick_validate.py` after edits.
- Use `uv run --with pyyaml` if Python or PyYAML is not otherwise available.
- Check `git diff` for accidental trigger changes or lost requirements.
- Re-read the final `SKILL.md` for scanability.

```powershell
uv run --with pyyaml C:\Users\ctyja\.codex\skills\.system\skill-creator\scripts\quick_validate.py .\skills\{skill-name}
```

## Review Checklist

- Frontmatter trigger is clear.
- Body is mostly imperative bullets.
- Rules are grouped under short headings.
- Exact snippets and constants survived.
- Long explanations are removed or moved to references.
- No unrelated files were added.
- Validation passes.
