---
name: fix-bug
description: Fix a bug by reproducing it, finding the root cause, writing a regression test that fails, making the smallest fix and verifying that existing functionality still works, with a written fix plan first for large or risky changes. Use whenever the user reports a bug, error, crash, failing test, regression, stack trace or something that stopped working, or pastes a bug ticket, even if they never say "bug". Not for new features or planned behaviour changes, which belong in prd-interview or spec-interview.
---

# Fix Bug

Fix a bug at its root cause, without breaking anything that already works, and leave a test behind that stops it coming back.

The order matters. A fix made before the bug is reproduced and explained is a guess: the symptom may disappear while the real problem stays, and nobody can tell whether the fix worked.

## Input

The user supplies a **bug report**, either as the skill argument (`/nz:fix-bug exports fail with a 500 for reports over 50,000 rows`), in their message, or as a ticket.

- If no bug was given, ask one question: "What's going wrong?" and wait.
- Restate the bug in one line: what happens, and what should happen instead.

## Step 1 — Understand the report

Collect what you need to reproduce the bug:

- What happened, and what was expected?
- What are the steps to reproduce it?
- What errors, logs or stack traces exist?
- When did it start, and did a release or change come before it?
- Who is affected: everyone, or only certain data, accounts, versions or environments?

Look in the repo, logs and ticket first. Ask only for what you can't find, one to three questions per turn, and use the AskUserQuestion tool when available.

## Step 2 — Reproduce it

Make the bug happen yourself, using the smallest setup that shows it: a failing test, a script, a command or the running app. Use local or test environments. Don't touch production systems or production data unless the user explicitly asks.

If you can't reproduce it, don't ship a speculative fix. Tell the user what you tried and what you ruled out, then ask for what would help, such as exact data, environment details or logs. If they want to go ahead anyway, make that decision visible in the final report, because the fix will be unproven.

## Step 3 — Find the root cause

- Follow the failure back from the symptom to where the wrong behaviour starts. A stack trace shows where it failed, which may not be where it went wrong.
- For regressions, use `git log`, `git blame` or `git bisect` to find the change that introduced it.
- Test one hypothesis at a time, and let evidence confirm or rule it out. Remove any temporary logging or debug code afterwards.
- State the cause in one sentence, in the form "X happens because Y". If you can't, keep investigating.
- Search for the same mistake elsewhere in the code, so one fix doesn't leave copies of the bug behind.

Be wary of fixes that only hide the symptom, such as catching the exception, adding a retry or adding a null check. These are correct only when you can explain why the failing case is expected.

## Step 4 — Check the impact

Before changing anything, work out what the fix could break.

- Find what depends on the code you'll change: callers, shared helpers, public APIs, background jobs, config and data formats.
- List the behaviour that must stay the same, and check which existing tests cover it.
- Run the related tests, or the full suite if practical, and keep the results as your **baseline**. Without one, you can't tell what the fix broke from what was already failing.

## Step 5 — Plan large fixes first

Treat a fix as large if it would:

- need a design change, such as a new locking or data model
- change behaviour customers rely on, or a public contract
- need a data migration or staged rollout
- touch shared code used across many features, or many files
- change important behaviour that has little test coverage
- need agreement across several teams or systems

For a small fix, go straight to Step 6.

For a large fix, leave the code unchanged for now, because a plan is cheap to correct and a risky fix is not. Tell the user what makes it large, then write the plan to `FIX-PLAN.md` in the project root (or the path the user named). If `FIX-PLAN.md` already exists, ask whether to overwrite it or write to `FIX-PLAN-<bug-slug>.md` instead.

```markdown
# <Bug title> — Fix Plan

**Status:** Draft
**Date:** <today>

## Bug
What happens, what should happen, and who is affected.

## Root Cause
X happens because Y, with the evidence.

## Impact
The code, features and data the fix touches.

## Options
Each realistic option with its trade-offs.

## Proposed Fix
The recommended option, why it was chosen, and the steps in order.

## Regression Risks
What could break, and how each risk is covered.

## Test Plan
New tests, existing tests to run, and manual checks.

## Rollout and Rollback
How it ships, and how to undo it if something goes wrong.

## Open Questions
Decisions the user or team still needs to make.
```

Ask the user to review the plan, update it with any changes, and wait for their approval before touching the code. If they prefer to skip the plan, follow their decision. Once it's approved, continue with Step 6 and follow the plan. If the fix turns out to need a wider redesign, recommend `/nz:spec-interview`.

## Step 6 — Write the tests

**Protect existing behaviour first.** If behaviour you listed in Step 4 has no test and the fix could affect it, add a test for it now. These tests should pass before the fix and still pass after it.

**Then write a regression test** that captures the bug, following the project's test conventions. Put it at the lowest level that still catches the bug.

Run it before changing the code and confirm it **fails for the right reason**, meaning it shows the reported problem rather than a setup or typo error. A test that was never seen failing doesn't prove the fix.

If an automated test really isn't practical, say why and keep clear manual steps for Step 8.

## Step 7 — Make the smallest fix

- Change only what's needed to correct the root cause, matching the surrounding code.
- Leave refactoring, cleanup and unrelated problems out, because they make the fix harder to review and riskier to ship. List anything worth following up in the final report.
- If the fix grows beyond what you expected, stop and go back to Step 5.

## Step 8 — Verify

**The bug is fixed:**

- Confirm the regression test now passes.
- Repeat the original reproduction from Step 2 and confirm the bug is gone.

**Nothing else broke:**

- Run the same tests as the baseline, and confirm everything that passed before still passes. Run the full suite if practical. If it's too slow, run everything covering the affected code and say what was skipped.
- Run lint and type checks if the project has them.
- Check the callers and flows from Step 4 that tests don't cover, using the running app or a script where you can.
- Never delete, skip or weaken a test to get a passing result. If an existing test fails, work out whether the fix broke something or the test relied on the buggy behaviour, and tell the user.
- Report tests that were already failing in the baseline separately.

If verification fails, go back to Step 3. The root cause is probably wrong or incomplete.

## Step 9 — Report

Finish with a short report, and include only what applies:

```markdown
**Cause:** X happens because Y.
**Fix:** What changed, with file references.
**Test:** The regression test, and confirmation that it failed before the fix and passes now.
**Verified:** What was run, and the results.
**Existing functionality:** What was checked against the baseline, and confirmation that nothing else broke.
**Still needs attention:** Existing bad data, other affected places, security impact, or unrelated problems found.
```

Say clearly if something couldn't be verified. A code fix doesn't repair data the bug has already damaged, so flag that if it applies.

Leave committing, pushing and deploying to the user unless they ask for them.
