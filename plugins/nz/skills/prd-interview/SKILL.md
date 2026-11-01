---
name: prd-interview
description: Interview the user about a feature idea, then write PRD.md as an Atlassian-style Agile PRD with an Amazon-style customer problem statement, measurable acceptance criteria and domain-specific non-functional requirements. Use whenever the user wants a PRD, product requirements, a feature brief or user stories, or needs to define what to build and why before development starts, even if they never say "PRD". For how to build something, use spec-interview instead.
---

# PRD Interview

Turn a feature idea into a product requirements document by interviewing the user until every section can be filled in without guessing, then write it to `PRD.md`.

Three ideas shape the document:

- **Agile PRD (company standard).** Follow Atlassian's Confluence product requirements template, keeping its section names and order. The goal is a shared understanding across product, design, engineering and QA, so write just enough for them to agree on what is being built. It is not an exhaustive contract.
- **Customer first.** Follow Amazon's Working Backwards habit: define the customer and their problem before discussing the solution. A feature that can't name its customer or problem isn't ready for requirements.
- **Measurable.** Anything the team will be held to needs a number or a pass/fail check, because "fast" and "easy to use" mean different things to each reader and can't be tested.

## What stays out of a PRD

A PRD covers **what** and **why**. **How** belongs in the technical spec, which `/nz:spec-interview` produces next.

When the user offers an implementation detail, ask what need sits behind it and record the need: "use Redis caching" becomes "reports load in under 2 seconds". A real constraint, such as "must use our existing billing provider", goes under **Assumptions**.

## Input

The user supplies a **feature idea**, either as the skill argument (`/nz:prd-interview scheduled report exports`) or in their message.

- If no idea was given, ask one question: "What feature do you want to define?" and wait.
- Restate the idea in one line so the user can correct it before the interview starts.

## Step 1 — Ground yourself first (silently)

Before asking anything, learn what you can on your own so you never ask something the repo already answers.

- Read what exists: README, docs, earlier PRDs or specs, and any ticket or research the user pointed to.
- If there is a codebase, find who uses the product today, what related behaviour exists, and the terms the product uses for its users and concepts.
- Work out the product domain (for example public API, mobile app, payments), because it decides which non-functional requirements matter in Phase 4.
- Turn findings into informed questions: "The app already has weekly email digests. Should exports use the same schedule options?" beats "How should scheduling work?"

## Step 2 — Interview

### How to ask

- Ask **one to three questions per turn**, never a wall of questions. Use the AskUserQuestion tool when available.
- Prefer multiple choice with a **recommended option first**, labelled "(Recommended)", plus a one-line reason. The user can always type their own answer.
- When an answer is vague ("most users", "it should be quick"), push for something concrete: a number, an example, or a decision.
- When an answer creates a contradiction or a risk, say so briefly and ask how to resolve it.
- If the user says "you decide" or "don't know", propose a sensible default, state it, and record it under **Assumptions**.
- Match the depth to the feature. A small change may need one short round per phase; skip what doesn't apply.

### Phase 1 — Customer problem

Settle this before anything else, because every later section depends on it.

- **Who exactly is the customer?** If the answer is "everyone", narrow it to the group that feels the problem most.
- **What is the problem from their point of view?** Get it in the words they would use. If there are several problems, which matters most to them?
- **How do they deal with it today, and where does that fall short?** Workarounds, competitors or doing nothing all count.
- **What is the evidence?** Support tickets, interviews, usage data, lost deals. How many customers are affected, and how often?
- **What is the single most important benefit?** Push for one, since a list of benefits usually means the problem isn't clear yet.
- **Would customers need to change their behaviour to get it?**

If there is no evidence, keep going, but record the claim under **Assumptions** as unvalidated and add an open question for how to validate it. If the interview shows there's no real customer problem, say so plainly. That is a useful result.

### Phase 2 — Objective and success metrics

- **Objective:** How does this support the organization's larger goals, and why now?
- **Success metrics:** How will you know it worked? List each goal with its metric, a current baseline, a target and a timeframe. If the baseline is unknown, add an open question to measure it.
- **Basics:** What is the target release, and who is on the core team (owner, design, engineering, QA, stakeholders)?

### Phase 3 — Assumptions, options and acceptance criteria

- **Assumptions:** What are you assuming about users, technical constraints and business goals?
- **Options:** Map out the product requirements under consideration as user stories: "As a `<customer>`, I want `<capability>`, so that `<benefit>`".
- **Importance:** Rate each one **Must**, **Should** or **Could**. If everything is a Must, ask what they would cut to ship a month earlier.
- **Acceptance criteria:** Write them for each Must and Should story using Given / When / Then.

Every criterion needs a result a tester could mark pass or fail without asking anyone what was meant:

| Weak | Measurable |
| --- | --- |
| Exports are fast | Given a report of up to 100,000 rows, when the user requests a CSV export, then the file is ready within 60 seconds |
| Users get a clear error | Given a scheduled export fails, when the failure is detected, then the owner receives an email within 15 minutes naming the report and the reason |
| Scheduling is easy | Given a first-time user on the report page, when they schedule an export, then they finish in 4 steps or fewer |

Include the unhappy paths that customers will hit: empty states, invalid input, permissions, and what happens when something fails.

### Phase 4 — Domain-specific non-functional requirements

Read [references/nfr-domains.md](references/nfr-domains.md) and use the sections for this product's domain. Ask only about the qualities that matter to this feature, usually five to eight. A generic checklist copied into every PRD gets ignored, so tie each requirement to what would hurt this customer or business if it went wrong.

Each requirement needs a target and a way to verify it. Treat the numbers in the reference as starting points to discuss, and mark any target the user didn't explicitly agree to as `(assumed)`.

### Phase 5 — Supporting documentation, open questions and scope

- Are there mockups, diagrams or visual designs to link? If not, capture the key user journey in a few steps.
- What still has to be decided or researched? Each open question needs an owner. Record the answer and the date if it gets resolved during the interview.
- What is deliberately out of scope for this feature or release?

### When to stop

Stop when you could write every section of the template below **without inventing anything**. Every remaining unknown is either a recorded assumption or an open question the user agreed can stay open.

Then give a short summary of your understanding (5–10 bullets) and ask: "Anything wrong or missing before I write PRD.md?" Apply corrections, then write.

## Step 3 — Write PRD.md

- Write to `PRD.md` in the project root (or the path the user named).
- If `PRD.md` already exists, ask whether to overwrite it or write to `PRD-<feature-slug>.md` instead.
- Use only what the user said, what the repo showed, and the recorded assumptions.
- Write the **Customer Problem** section in plain language from the customer's point of view, with no internal jargon, feature names or solution details.
- Omit a section only if it truly doesn't apply; otherwise write "None" rather than deleting it.

Before finishing, check the draft:

- The customer is a specific group, and the problem makes sense without knowing the solution.
- Every success metric has a baseline (or an open question to find it), a target and a timeframe.
- Every Must and Should story has acceptance criteria, and none rely on words like fast, easy, simple, intuitive or reliable without a number.
- Every non-functional requirement has a target and a way to verify it.
- No technology or architecture choices have slipped in, apart from recorded constraints.

### Template

```markdown
# <Feature name> — PRD

| | |
| --- | --- |
| **Target release** | <release or date> |
| **Status** | Draft |
| **Date** | <today> |
| **Epic** | <Jira link, or "Not created yet"> |
| **Owner** | <name> |
| **Core team** | Design: <name> · Engineering: <name> · QA: <name> · Stakeholders: <names> |

## Customer Problem

**Customer:** The specific group this is for.

**Problem:** What goes wrong for them today, in their own words, and how often.

**Today's alternatives:** How they cope now and where that falls short.

**Evidence:** Tickets, research or data, with numbers. Label anything unproven as unvalidated.

**Most important benefit:** The one outcome that matters most to them.

## Objective

How this supports the organization's larger goals, and why now.

## Success Metrics

| Goal | Metric | Baseline | Target | Timeframe |
| --- | --- | --- | --- | --- |

## Assumptions

Assumptions about users, technical constraints and business goals, including defaults chosen during the interview and unvalidated claims.

## Options

| ID | User story | Importance | Jira issue | Notes |
| --- | --- | --- | --- | --- |
| US-1 | As a <customer>, I want <capability>, so that <benefit> | Must | | |

## Acceptance Criteria

### US-1: <short title>

- **AC-1.1:** Given <context>, when <action>, then <measurable result>.

## Non-Functional Requirements

Domain: <domain or domains>

| ID | Category | Requirement | Target | How verified |
| --- | --- | --- | --- | --- |
| NFR-1 | | | | |

## Supporting Documentation

Links to mockups, diagrams and visual designs, or the key user journey in a few steps.

## Open Questions

Things we need to decide or research.

| Question | Owner | Answer | Date answered |
| --- | --- | --- | --- |

## Out of Scope

What is out of scope for this feature or release, and why.
```

## Finish

Tell the user the PRD is written, where it is, and list any open questions in one or two lines. Suggest `/nz:spec-interview` as the next step for the technical design. Don't recap the interview.
