# nolowiz-dev-skills

Claude Code skills and plugins for the product development team.

## Install

Run these inside Claude Code:

```
/plugin marketplace add rupeshs/nolowiz-dev-skills
/plugin install nz@nolowiz-dev-skills
```

## Usage examples

A short description is enough to start. Run any skill without one and it asks what you want to work on.

### Define what to build: `prd-interview`

Use this when the team needs to agree on what to build and why. It writes `PRD.md` with the customer problem, success metrics, user stories, acceptance criteria and non-functional requirements.

```
/nz:prd-interview scheduled report exports
/nz:prd-interview let customers download invoices as PDF
/nz:prd-interview single sign-on for enterprise customers
```

### Define how to build it: `spec-interview`

Use this when you know what to build and need the technical design. It checks your codebase, then writes `SPEC.md` with the interfaces, data, failure modes, rollout and testing plan.

```
/nz:spec-interview add rate limiting to the public API
/nz:spec-interview migrate background jobs to a queue
/nz:spec-interview add audit logging to admin actions
```

### Use both for a new feature

1. Run `/nz:prd-interview scheduled report exports` and review `PRD.md` with the team.
2. Run `/nz:spec-interview scheduled report exports`. It reads `PRD.md` and doesn't ask those questions again.
3. Build from `PRD.md` and `SPEC.md`.

### Fix a bug: `fix-bug`

Use this when something is broken. It reproduces the bug, finds the root cause, writes a regression test, makes the smallest fix and verifies it. Include any error messages, logs or steps to reproduce the bug. It leaves committing to you.

```
/nz:fix-bug exports fail with a 500 for reports over 50,000 rows
/nz:fix-bug login redirects to a blank page on Safari since Friday's release
/nz:fix-bug the checkout test fails intermittently in CI
```

Before changing code, it checks what the fix could affect and records which tests pass. Afterwards it confirms that those tests still pass.

If the fix is large or risky, such as a design change, a data migration or a change to shared code, it tells you and writes `FIX-PLAN.md`. It waits for your approval before changing any code.

## Updating

Auto-update is off by default for this marketplace. To turn it on, run `/plugin`, open the **Marketplaces** tab, select `nolowiz-dev-skills` and choose **Enable auto-update**.

To update manually, run these in your terminal, then restart Claude Code:

```
claude plugin marketplace update nolowiz-dev-skills
claude plugin update nz@nolowiz-dev-skills
```

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
| `nz` | `prd-interview`, `spec-interview`, `fix-bug` |

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md).
