# Authoring skills

This folder holds the skills published by this repository. Each skill lives in its own folder:

```
skills/
└── my-skill/
    ├── SKILL.md
    └── optional-supporting-file.md
```

## SKILL.md

Start every skill with YAML frontmatter:

```markdown
---
name: my-skill
description: Use this skill when...
---

# My skill

Instructions for the agent.
```

- `name` must match the folder name and use kebab-case.
- `description` is how the agent decides whether to load the skill. State the situations that should trigger it, including relevant keywords.
- Keep the body focused on actionable instructions. Prefer short sections and examples over long prose.
- Reference supporting files by relative path so the whole folder works when copied.

## Guidelines

- One skill per folder, with a single responsibility.
- Write for a general audience: avoid assumptions about a specific editor, shell or operating system unless the skill requires one.
- Do not include secrets, credentials or personal data.
- Check that the skill works when copied into a fresh project before opening a pull request.
- Update the skills table in the root [README](../README.md) when adding a skill.

## Pull requests

Follow the [`pr-and-commit-title`](pr-and-commit-title/SKILL.md) skill for commit messages and pull request titles.
