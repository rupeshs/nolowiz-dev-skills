---
name: spec-interview
description: Interview the user about a development topic they haven't fully thought through (e.g. "add rate limiting to the public API"), then write a complete spec to SPEC.md.
---

# Spec Interview

Turn a half-formed development idea into a written spec by interviewing the user until every section of the spec can be filled in without guessing, then write it to `SPEC.md`.

## Input

The user supplies a **development topic**, either as the skill argument (`/nz:spec-interview add rate limiting to the public API`) or in their message.

- If no topic was given, ask one question: "What do you want to spec out?" and wait.
- Restate the topic in one line so the user can correct it before the interview starts.

## Step 1 — Ground yourself first (silently)

Before asking anything, learn what you can on your own so you never ask the user something the code already answers.

- If `PRD.md` exists (or the user named a PRD), read it first. Treat its customer problem, goals, user stories, acceptance criteria and non-functional requirements as decided, and carry them into the spec instead of asking again. Raise an item only if it conflicts with the code or can't be built as written.
- If a codebase is available, look for what the topic touches: frameworks, existing middleware, config, infrastructure (e.g. for rate limiting: API gateway, auth/API-key handling, Redis or other shared stores, existing 429 handling).
- Note conventions the spec should follow (naming, config style, error format, testing setup).
- Turn findings into informed questions: "You already use Redis for sessions — reuse it for counters?" beats "Where should state be stored?"

## Step 2 — Interview

### How to ask
- Ask **one to three questions per turn**, never a wall of questions. Use the AskUserQuestion tool when available.
- Prefer multiple choice with a **recommended option first**, labelled "(Recommended)", plus a one-line reason. The user can always type their own answer.
- Lead with the questions whose answers change the most downstream (scope and goals before implementation details).
- When an answer is vague ("reasonable limits", "it should be fast"), push for something concrete: a number, an example, or a decision.
- When an answer creates a contradiction or a risk, say so briefly and ask how to resolve it.
- If the user says "you decide" or "don't know", propose a sensible default, state it, and record it in the spec under **Assumptions**.
- Keep a running mental checklist of the coverage areas below; skip any that genuinely don't apply.

### Coverage areas
1. **Problem & goal** — why now, what goes wrong today, what success looks like (measurable if possible).
2. **Scope** — what's in, what's explicitly out, first version vs later.
3. **Actors** — who is affected (users, clients, internal services, admins), and do they differ in treatment?
4. **Functional behaviour** — the core rules, inputs, outputs, and states.
5. **Interface / contract** — APIs, responses, errors, headers, config, UI changes.
6. **Data & state** — what's stored, where, for how long, consistency needs.
7. **Edge cases & failure modes** — what happens when dependencies fail, at boundaries, under abuse.
8. **Non-functional** — performance budget, scale, availability, cost.
9. **Security & privacy** — abuse vectors, sensitive data, permissions.
10. **Observability** — metrics, logs, alerts, dashboards.
11. **Rollout** — feature flags, shadow/dry-run mode, migration, communication to users, rollback.
12. **Testing** — how it will be verified (unit, integration, load).

### Example: topic-specific questions (rate limiting a public API)
Use as inspiration for the kind of depth expected; generate equivalents for any topic.
- What problem triggers this: abuse, cost, fairness, protecting a fragile backend?
- What is the limit keyed on: API key, user, IP, org — or a combination?
- Are there tiers (free / paid / internal) with different limits? Concrete numbers per tier?
- Per endpoint, or global? Are expensive endpoints weighted more?
- Algorithm: fixed window, sliding window, or token bucket (allows bursts)?
- Where does counter state live (Redis, gateway, in-memory) and does it need to be exact across instances?
- If the limiter's store is down: fail open (allow traffic) or fail closed (reject)?
- Response when limited: 429 with `Retry-After` and `X-RateLimit-Limit/Remaining/Reset` headers? Error body format?
- Exemptions or allowlists? Can limits be overridden per customer?
- How will existing clients be warned before enforcement? Start in log-only mode?
- What metrics and alerts show it's working (and not blocking legitimate traffic)?

### When to stop
Stop interviewing when you could write every section of the spec template below **without inventing anything** — every remaining unknown is either a recorded assumption or a listed open question the user agreed can stay open.

Then give a short summary of your understanding (5–10 bullets) and ask: "Anything wrong or missing before I write SPEC.md?" Apply corrections, then write.

## Step 3 — Write SPEC.md

- Write to `SPEC.md` in the project root (or the path the user named).
- If `SPEC.md` already exists, ask whether to overwrite it or write to `SPEC-<topic-slug>.md` instead.
- Use only what the user said, what the code showed, and the recorded assumptions. Be specific: real numbers, real names, real endpoints.
- Omit a section only if it truly doesn't apply; otherwise write "None" rather than deleting it.

### Template

```markdown
# <Feature name> — Spec

**Status:** Draft
**Date:** <today>
**Owner:** <user, if known>

## Summary
Two or three sentences: what is being built and why.

## Problem & Goals
- Problem today
- Goals (measurable where possible)
- Non-goals

## Scope
- In scope (v1)
- Out of scope / later

## Actors
Who is affected and how they differ.

## Requirements
### Functional
Numbered, testable statements ("FR-1: The API returns 429 when…").
### Non-functional
Performance, scale, availability, cost targets.

## Design
### Interface / contract
Endpoints, request/response shapes, errors, headers, config.
### Data & state
What's stored, where, retention, consistency.
### Behaviour details
Algorithms, rules, state transitions.

## Edge Cases & Failure Modes
Each case and the expected behaviour.

## Security & Privacy

## Observability
Metrics, logs, alerts.

## Rollout Plan
Phases, flags, migration, communication, rollback.

## Testing Strategy

## Assumptions
Defaults chosen during the interview that the user didn't decide explicitly.

## Open Questions
Unresolved items, each with who/what can resolve it.
```

## Finish
Tell the user the spec is written, where it is, and list any open questions in one or two lines. Don't recap the interview.

End by telling them the next step: review and update the spec, change its status to `Approved`, then run `/nz:implement-spec` to build it.
