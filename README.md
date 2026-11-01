# nolowiz-dev-skills

Claude Code skills and plugins for the product development team.

## Install

Run these inside Claude Code:

```
/plugin marketplace add rupeshs/nolowiz-dev-skills
/plugin install dev-workflow@nolowiz-dev-skills
```

## Usage example

```
/spec-interview add rate limiting to the public API
```

## Updating

```
/plugin marketplace update nolowiz-dev-skills
```

Or turn on auto-update under `/plugin` → Marketplaces.

## Team auto-setup

Add this to a product repo's `.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "nolowiz-dev-skills": {
      "source": { "source": "github", "repo": "rupeshs/nolowiz-dev-skills" },
      "autoUpdate": true
    }
  },
  "enabledPlugins": { "dev-workflow@nolowiz-dev-skills": true }
}
```

## Available plugins

| Plugin | Skills |
| --- | --- |
| `dev-workflow` | `spec-interview` |

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md).
