---
name: implement-spec
description: Build a feature from a reviewed SPEC.md (and PRD.md if there is one), one task at a time, tracking progress in IMPL-PLAN.md so any later session can resume at the next task. Writes a task list and a spec coverage table, waits for approval, implements each task with tests, stays within the spec's scope and reports what is done. Use whenever the user wants to implement, build, continue or resume work from a spec or an IMPL-PLAN.md, even if they only say "build it", "next task" or "carry on". Not for bugs, which belong in fix-bug, or for deciding what to build, which belongs in prd-interview or spec-interview.
---

# Implement Spec

Build what an approved spec describes, one task at a time, and keep a written record of progress so the work can stop and resume at any point, in any session, by anyone.

Three rules shape the work:

- **The spec is the agreement.** Build what it says, and only that. When the code shows the spec is wrong or unclear, stop and ask. Don't quietly build something different.
- **Done means tested.** A task or spec item is marked done only when a test for it passes.
- **The plan file is the memory.** `IMPL-PLAN.md` is updated after every task, so a session that ends suddenly loses at most the task in progress.

## Input

The user may give, as the skill argument or in their message:

- The spec path, if it isn't `SPEC.md` (`/nz:implement-spec SPEC-rate-limiting.md`).
- A limit on this run, such as one task or one rollout phase (`/nz:implement-spec only T4`, `/nz:implement-spec phase 1`).

With no argument, use `SPEC.md` in the project root and continue from wherever the plan says.

## Step 1 — Find where the work stands

Find the plan file that belongs to the spec. `SPEC.md` pairs with `IMPL-PLAN.md`, and `SPEC-<slug>.md` pairs with `IMPL-PLAN-<slug>.md`.

- **Plan exists:** this is a resume. Go to Step 5.
- **No plan, spec exists:** this is a new build. Go to Step 2.
- **No spec:** tell the user there's nothing to build from yet, and recommend `/nz:spec-interview`. If there are several specs and none was named, ask which one.

## Step 2 — Check the spec is ready

Read the whole spec, and `PRD.md` if it exists. The PRD's acceptance criteria count as requirements too.

- **Status.** The spec should say `Approved`. If it still says `Draft`, ask the user whether it has been reviewed. If they confirm, set it to `Approved` and continue. Otherwise stop, because building from an unreviewed spec wastes work when it changes.
- **Open questions.** For each item under Open Questions, decide whether it blocks any part of the build. List the blocking ones and ask the user to resolve them now, or agree that the affected tasks wait. Record each answer under Assumptions in the spec, with the user's agreement.
- **Fit with the code.** Read the code the spec touches: its structure, conventions, test setup, and how to run the tests, lint and type checks. If anything in the spec can't be built as written, raise it now, not halfway through.

## Step 3 — Write the plan

Break the spec into tasks:

- Each task is small enough to build and test in one sitting, and leaves the code working when it's done.
- Order tasks by dependency, with the core behaviour first, then edge cases, then observability and rollout. If the spec has rollout phases, group tasks by phase.
- Each task names the spec items it covers, the files it's likely to touch, and the tests that will prove it.

Then build the **coverage table**. Every requirement in the spec gets a row: each FR and NFR, each edge case and failure mode, security, observability and rollout item, and each PRD acceptance criterion. Every row links to a task, except:

- Items listed as out of scope, which are marked `Not built` so a reviewer can see they were left out on purpose.
- Items that can't be done in code, such as telling customers or a load test in staging, which are marked `Manual` with who does them.

Write the plan to `IMPL-PLAN.md` (or `IMPL-PLAN-<slug>.md`) next to the spec:

```markdown
# <Feature name> — Implementation Plan

**Spec:** SPEC.md (commit <short hash of the last commit touching it>)
**PRD:** PRD.md, or None
**Status:** Not started | In progress | Done
**Next:** T1
**Progress:** 0 of N tasks done
**Updated:** <today>

## Tasks
- [ ] T1: <what it does> (FR-1, FR-2)
  - Files: <likely files>
  - Tests: <tests that prove it>
- [ ] T2: ...

## Spec coverage
| Spec item | Spec section | Task | Status | Proof |
| --- | --- | --- | --- | --- |
| FR-1 <short name> | Requirements | T1 | Todo | |
| <out-of-scope item> | Out of scope | — | Not built | |
| <manual item> | Rollout Plan | — | Manual | <owner> |

## Baseline
Test, lint and type check results before any change.

## Decisions & deviations
None

## Blocked
None
```

Statuses are `Todo`, `Done`, `Blocked`, `Manual` and `Not built`. **Proof** is the test that covers the item, or the manual check when a test isn't practical.

Show the user the task list and ask them to approve it. For a spec with three tasks or fewer, a one-line confirmation is enough. Once it's approved:

- Set the spec's status to `In progress`, and add `**Implementation:** IMPL-PLAN.md` under it. Make no other changes to the spec for tracking progress.
- Set the plan's status to `In progress`.

## Step 4 — Record the baseline

Before changing any code, run the test suite, lint and type checks, and write the results under **Baseline** in the plan. Without a baseline, you can't tell what the new code broke from what was already failing. Keep tests that were already failing separate in every later report.

Then go to Step 6.

## Step 5 — Resume safely

Read the plan, but check it before trusting it. Code may have changed by hand, or the last session may have stopped partway through a task.

- **Done tasks really are done.** Their files exist and their tests pass. If one doesn't hold up, set the task back to unchecked and tell the user.
- **Half-finished work.** If uncommitted changes belong to the task marked **Next**, finish that task from where it stands rather than starting it again.
- **Spec changes.** Compare the spec with the commit recorded in the plan header (`git diff <hash> -- SPEC.md`). If it has changed, show the user what changed and update the tasks and coverage table to match before continuing. Record the new commit in the header.
- **Blocked tasks.** If the reason for a block has been resolved, move the task back into the list.

Tell the user in one line where the work stands, for example "3 of 7 tasks done, continuing with T4: fail open when Redis is down", then continue.

## Step 6 — Build one task

Take the task marked **Next**.

1. **Test first.** Write the tests for the task's spec items, following the project's test conventions. Where practical, run them and confirm they fail for the right reason before writing the code.
2. **Build it.** Write only what this task needs, matching the surrounding code. Leave refactoring and unrelated fixes out, and list anything worth following up in the final report.
3. **Verify.** Run the task's tests, the tests around the code you changed, and lint and type checks. Everything that passed in the baseline must still pass. Never delete, skip or weaken a test to get a pass.
4. **Update the plan straight away:** tick the task, set its coverage rows to `Done` with the proof, and update **Next**, **Progress** and **Updated**.

**When the spec doesn't work as written,** because it's ambiguous, contradicts the code, or would break something, stop. Explain the problem briefly, offer options with a recommendation, and wait for the user. Record their decision under **Decisions & deviations**. If it changes what's being built, update `SPEC.md` with their agreement, so the spec still describes the code.

**When a task is blocked** on a person, a decision or something outside the repo, move it to **Blocked** with the reason and what would unblock it. Continue with tasks that don't depend on it.

**Stay out of production.** Run migrations, jobs and scripts against local or test environments only. Don't deploy, change shared infrastructure or touch production data unless the user explicitly asks.

## Step 7 — Keep going or stop

After each task, carry on with the next one unless:

- The user limited this run to a task or phase, and it's done.
- Every remaining task is blocked.
- A rollout phase has finished and the spec says to ship or check that phase before starting the next one.

Because the plan is up to date after every task, stopping at any of these points is safe. When stopping early, tell the user what was finished, what's next, and that running `/nz:implement-spec` again will continue from there.

## Step 8 — Finish

When no task is left unticked:

- Run the full test suite, lint and type checks, and compare the results with the baseline.
- Go through the coverage table. Every row must be `Done`, `Manual` or `Not built`. Any other row means the work isn't finished, so report it rather than claiming completion.
- Check each PRD acceptance criterion against the running code where you can, not just the tests.
- Set the plan's status to `Done`, and the spec's status to `Implemented`.

Finish with a short report, including only what applies:

```markdown
**Built:** What was built, with the main files.
**Spec coverage:** N of M items done; the rest marked Manual or Not built, and why.
**Verified:** What was run, and the results compared with the baseline.
**Deviations:** Decisions that changed the spec, or None.
**For the team:** Manual items and who owns them, blocked items, follow-ups.
```

Say clearly if something couldn't be verified.

Leave committing, pushing and deploying to the user unless they ask for them. Recommend committing `IMPL-PLAN.md` with the code, so a teammate can pick up the work and a reviewer can check the coverage table.
