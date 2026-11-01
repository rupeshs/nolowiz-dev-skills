# nolowiz-dev-skills

Claude Code skills and plugins for the product development team.

## Install

Run these inside Claude Code:

```
/plugin marketplace add rupeshs/nolowiz-dev-skills
/plugin install nz@nolowiz-dev-skills
```

## Usage example

```
/nz:spec-interview add rate limiting to the public API
```

## Updating

```
/plugin marketplace update nolowiz-dev-skills
```

Or turn on auto-update under `/plugin` → Marketplaces.

## Uninstall

Remove the plugin:

```
/plugin uninstall nz@nolowiz-dev-skills
```

To remove the marketplace too:

```
/plugin marketplace remove nolowiz-dev-skills
```

Removing the marketplace also uninstalls any plugins you installed from it.

If you used team auto-setup, also delete the `extraKnownMarketplaces` and `enabledPlugins` entries from the repo's `.claude/settings.json`. Otherwise the plugin is offered again the next time someone opens the repo.

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
  "enabledPlugins": { "nz@nolowiz-dev-skills": true }
}
```

## Available plugins

| Plugin | Skills |
| --- | --- |
| `nz` | `spec-interview` |

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md).
