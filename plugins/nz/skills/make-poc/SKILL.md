---
name: make-poc
description: Build a quick, throwaway proof of concept (POC) or prototype of a user requirement inside an isolated environment (uv virtual environment, Docker or Docker Compose, project-local node_modules), so nothing is installed into the system Python, global packages or host services. Writes POC-PLAN.md with the question the POC must answer, success criteria, stack, isolation and cleanup, waits for approval, then builds the smallest thing that answers the question, runs it and records the result. Use whenever the user wants a POC, prototype, spike, demo, quick experiment or "see if this works" build of an idea or requirement, even if they never say "POC". Not for a tool they will keep using, which belongs in make-tool, a product feature, which belongs in prd-interview or spec-interview, building from an existing SPEC.md, which belongs in implement-spec, or bugs, which belong in fix-bug.
---

# Make POC

Prove or disprove one idea quickly, in a sealed box the user can delete with one command.

A POC exists to answer a question, such as "can we parse these PDFs reliably?", "is this API fast enough?" or "does this library do what we need?". It is not the product. So the work stays small, shortcuts are allowed as long as they're written down, and the result that matters is the answer, not the code. Because a POC is thrown away or rewritten, it must never leave anything behind on the user's machine: no global packages, no changed system Python, no services installed on the host, no edited shell profiles.

## Rules

- **Isolated by default.** Every dependency lives inside the POC folder or a container. See "Isolation rules" below. If isolation isn't possible with the tools the user has, stop and ask. Don't fall back to a global install.
- **Plan first.** Write `POC-PLAN.md` and build nothing until the user approves it.
- **Answer the question.** Build the smallest thing that meets the success criteria, then stop. Ideas that come up while building go under "Next steps", not into the POC.
- **Honest result.** Report what was proven, what wasn't, and every shortcut that would have to change for production.

## Input

The user supplies a **requirement or idea**, either as the skill argument (`/nz:make-poc extract invoice totals from scanned PDFs`) or in their message.

- If nothing was given, ask one question: "What do you want to prototype?" and wait.
- Restate it in one line as **the question the POC answers**, for example "Can we extract invoice totals from scanned PDFs with at least 95% accuracy using open-source OCR?"
- If it is really a tool they'll keep using, a product feature, a build from an existing `SPEC.md`, or a bug, say so and suggest `make-tool`, `prd-interview`, `spec-interview`, `implement-spec` or `fix-bug`. Continue here only if the user still wants a POC.

## Step 1 — Start from what you already know

Read the conversation, any `PRD.md` or `SPEC.md`, and files the user pointed to. Don't ask what is already answered.

## Step 2 — Ask only what decides the POC

One round, at most 4 questions, using the AskUserQuestion tool when available, with a recommended default for each. Skip any question the request already answers. Pick from:

- **Success criteria:** What result would convince them, such as a number, a working demo flow, or an output file that looks right?
- **Inputs:** Sample data, files, API keys or test accounts they can provide. If none, should the POC use made-up sample data?
- **Stack:** Any language, framework or library it must use or must avoid? If they don't mind, recommend the fastest stack for this question.
- **Form:** Script with console output, small web page, API endpoint, or notebook?
- **Time box:** Roughly how long the POC should take, which caps its scope.

Research outside facts yourself, such as whether a library supports a format or what an API allows. Write remaining minor unknowns into the plan as assumptions.

## Step 3 — Check the environment (read-only)

Find out what isolation tools exist, without installing or changing anything. Use commands that work on the user's OS, for example:

- `uv --version`, for Python projects.
- `docker --version`, `docker compose version` and `docker info`, which shows whether the Docker daemon is running.
- `node --version` and `npm --version`, only if the POC needs Node.
- The OS, and whether the ports the POC needs (for example 8000, 5432) are free.

Then pick the isolation approach (see "Isolation rules"). If the tool needed is missing, don't install it. Put the choice in the plan as an open question with options, such as "install uv (a single user-level binary, removable later)", "run everything in Docker instead", or "you install it yourself". Installing uv or Docker is a change to the user's machine and needs their explicit OK.

## Step 4 — Write the plan

Pick the POC folder: `poc/<slug>/` in the project root, or the location the user named. If that folder already exists, ask whether to reuse it or pick a new name. The POC never mixes its files or dependencies into the main project's code, lock files or environment.

Write the plan to `<poc folder>/POC-PLAN.md`:

```markdown
# <POC name> — POC Plan

**Status:** Draft
**Date:** <today>
**Folder:** poc/<slug>/

## Question to answer
<one sentence>

## Success criteria
- <measurable or clearly observable result, for example "extracts the total from at least 19 of the 20 sample invoices">

## Scope
**In:** <the minimum needed to answer the question>
**Out:** <deliberately left out, such as auth, UI polish, error handling beyond the happy path, deployment>

## Approach
<2-5 sentences: how the POC will answer the question and why this is the quickest credible way>

## Stack and isolation
| Part | Choice | Isolated by |
| --- | --- | --- |
| Language/runtime | Python 3.12 | uv-managed Python, `.venv` in POC folder |
| Libraries | <list> | `pyproject.toml` + `uv.lock` in POC folder |
| Services | <for example Postgres 16> | Docker Compose project `poc-<slug>`, named volume, port bound to 127.0.0.1 |

**Nothing is installed globally.** <Name anything that does touch the machine outside the POC folder, such as uv's download cache or pulled Docker images, and how to remove it.>

## Files
<tree of the files that will be created>

## Steps
1. <step>
2. <step>

## How to run
<exact commands for the user's OS>

## How it will be verified
<how each success criterion will be checked, and with what data>

## Shortcuts taken
<things acceptable in a POC but not in production: hard-coded config, no retries, sample data, no tests beyond the check above>

## Cleanup
<exact commands that remove everything, for example `docker compose -p poc-<slug> down -v --rmi local`, then delete the folder>

## Assumptions, risks and open questions
- <item>

## Result
_Filled in after the build._
```

Then show the user a short summary (the question, success criteria, stack, isolation, anything that touches the machine outside the folder) and the path to the plan, and ask: "Shall I build it? You can say yes or change anything in the plan first." Stop and wait.

## Step 5 — The approval gate

Until the user explicitly approves ("yes", "go ahead", "approved", or picking a proceed option), don't create environments, install packages, pull images, start containers or write POC code. Reading, research and editing `POC-PLAN.md` are fine.

- A question or "tell me more" is not approval. Answer it, update the plan if needed, and ask again.
- On approval, set **Status** to `Approved`. If they approve with changes, update the plan and show only what changed.
- If the build reveals the plan must change in a way the user would care about (a new service, a different stack, anything installed outside the POC folder, bigger scope), pause, update the plan and get an OK.

## Step 6 — Build in isolation

Work only inside the POC folder, following the isolation rules. Keep it small:

- Use the fewest files and dependencies that answer the question. Pin versions (`uv.lock`, `package-lock.json`, image tags such as `postgres:16`, never `latest`).
- Put secrets in `.env` inside the POC folder, add a `.env.example` with placeholder values, and add a `.gitignore` covering `.env`, `.venv/`, `node_modules/` and output files.
- Add a short `README.md` in the POC folder: what it proves, how to run it, how to clean up. Point to `POC-PLAN.md` for the rest.
- Run it. Check every success criterion the way the plan said, with real or sample data, and keep the actual output (numbers, timings, sample results) for the report.
- If something fails, fix it within the time box. If it can't be made to work, that is a valid result. Record why and stop, rather than widening the scope.

## Step 7 — Record the result and report

Fill in the **Result** section of `POC-PLAN.md` and set **Status** to `Proven`, `Partly proven` or `Not proven`:

```markdown
## Result
**Answer:** <yes / no / partly, in one sentence>
**Evidence:** <what was run and what came out, with numbers>
**Success criteria:** <each criterion with Met / Not met>
**Limits:** <what this POC does not show, for example scale, real data, security>
**To take it to production:** <the shortcuts that must change and the main risks>
**Next steps:** <for example run /nz:prd-interview or /nz:spec-interview, test with real data, or drop the idea>
```

Then tell the user in a few lines: the answer, the evidence, the exact commands to run it again, the cleanup command, and anything that was not tested. Don't commit or push unless asked. Leave containers stopped unless the user wants to keep using the demo, and say which ones are still running if any.

## Isolation rules

Choose the lightest option that fully isolates the POC.

**Python: uv.**
- Create the project in the POC folder with `uv init --no-workspace` (so it doesn't join a parent uv workspace), pick the Python version with `uv python pin <version>`, add packages with `uv add <pkg>` and run with `uv run <command>`.
- The `.venv` lives in the POC folder. Python interpreters uv downloads go to uv's own managed directory, not the system Python.
- Never run `pip install` outside the POC's `.venv`, never use `pip install --user`, `sudo pip` or `--break-system-packages`, and never `uv pip install --system`.
- For a one-off command-line tool, use `uvx <tool>` instead of installing it.

**Node: project-local packages.**
- Install with `npm install` (or the package manager the project already uses) inside the POC folder only. Never `npm install -g`. Use `npx` for one-off tools.
- If the required Node version isn't installed, run the POC in a Docker container (for example `node:22`) rather than changing the user's Node.

**Services and everything else: Docker.**
- Databases, queues, caches, other languages, system libraries (for example Tesseract or ffmpeg) and anything needing `apt`, `brew` or `choco` run in containers, never on the host.
- Use a `compose.yaml` in the POC folder and always pass a project name (`docker compose -p poc-<slug> ...`) so the POC's containers, networks and volumes are grouped and easy to remove.
- Bind ports to localhost (`127.0.0.1:5432:5432`). Use named volumes or a folder inside the POC directory for data.
- If an app container needs the code, mount the POC folder only, never the user's home directory or the whole project.
- Clean up with `docker compose -p poc-<slug> down -v --rmi local`. Never run `docker system prune`, `docker volume prune` or anything else that affects containers, images or volumes the POC didn't create.

**Other languages.** Prefer Docker. Go modules and Cargo keep dependencies per project and only use their own user caches, so they're acceptable if the toolchain is already installed. Say so in the plan.

**Never, in any case:** edit shell profiles, `PATH`, the Windows registry, global git config, system services or files outside the POC folder; install system packages; or start anything that runs at login. If the POC truly needs one of these, explain why, and get explicit approval first.
