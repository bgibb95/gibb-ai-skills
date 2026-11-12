<img src="assets/icon.svg" width="64" height="64" alt="">

# gibb-ai-skills

A public collection of reusable AI skills and shareable AI instruction files. The contents are plain Markdown, so they work with any agent that supports skill folders, and they are easy to read, copy, adapt and improve.

## Skills

| Skill | Description |
| --- | --- |
| [`pr-and-commit-title`](skills/pr-and-commit-title/SKILL.md) | Format pull request titles and commit messages as `[EMOJI] [KEYWORD]: [Description]` |

## Using a skill

Each skill is a folder containing a `SKILL.md` file. To use one, copy its folder into the skills directory your AI tool reads, for example `.claude/skills/` in a project or `~/.claude/skills/` for all projects with Claude Code. Check your tool's documentation for the exact location.

A skill's `description` frontmatter tells the agent when to load it, so keep the whole folder intact when copying.

## What is a skill?

A skill is a folder of instructions an AI agent can load on demand. At minimum it contains a `SKILL.md` file with YAML frontmatter:

```markdown
---
name: my-skill
description: Use this skill when...
---

Instructions for the agent go here.
```

Supporting files, such as references or templates, can live beside `SKILL.md` in the same folder.

## Contributing

1. Create a folder under `skills/` using a short kebab-case name, for example `skills/my-skill/`.
2. Add a `SKILL.md` with `name` and `description` frontmatter. The description should say **when** to use the skill, not just what it does.
3. Keep instructions tool-agnostic where possible, and never include secrets or credentials.
4. Add the skill to the table in this README.
5. Open a pull request. Titles and commit messages follow the [`pr-and-commit-title`](skills/pr-and-commit-title/SKILL.md) format.

See [`skills/README.md`](skills/README.md) for more authoring guidance.

## License

Released under the [MIT License](LICENSE).
