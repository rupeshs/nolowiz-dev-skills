# Contributing

## Add a skill

Create `plugins/<plugin>/skills/<skill-name>/SKILL.md` with this frontmatter:

```markdown
---
name: <skill-name>
description: <when to use the skill>
---
```

- `name` is kebab-case.
- `description` says when to use the skill.

## Add a plugin

1. Create `plugins/<new-plugin>/.claude-plugin/plugin.json`.
2. Add skills under `plugins/<new-plugin>/skills/`.
3. Register the plugin in `.claude-plugin/marketplace.json`.

## Release

1. Bump `version` in both the plugin's `plugin.json` and its `marketplace.json` entry.
2. Run `claude plugin validate .`.
3. Open a pull request.

## Public repo warning

This repository is public. Never put secrets, internal hostnames, internal URLs, or customer data in a skill.
