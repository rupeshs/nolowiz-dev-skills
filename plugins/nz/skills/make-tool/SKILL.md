---
name: make-tool
description: Before building a tool, script, app, automation or integration, interview the user to understand their real use case and constraints, present a clear plan, and implement only after they explicitly approve it. Use whenever the user asks to create, build, make or automate something ("I want to create a tool that...", "build me a script to...", "can you make an app for...", "is there an API or solution for..."), especially when the request is short or vague, or when the right solution depends on their access, accounts, environment, data or preferences. Skip it for small, fully specified edits and pure questions with no build involved. Not for product features that need a PRD or spec, which belong in prd-interview or spec-interview, building from an existing SPEC.md, which belongs in implement-spec, or bugs, which belong in fix-bug.
---

# Make Tool

Understand the user's situation, agree a plan with them, and build only once they say yes.

A short request like "make a tool to download my files" hides the facts that decide the whole design: what access the user really has, what runs where, how much data, what failure they're fighting. Guessing those facts and building straight away often produces something that can't work for them, such as a downloader built around a VPN the user turns out not to have. A few minutes of questions and one plan they can read are much cheaper than a wrong build, and the user stays in control of what gets installed and changed on their machine.

## Input

The user supplies a **build request**, either as the skill argument (`/nz:make-tool a script that backs up my photos to a NAS every night`) or in their message.

- If nothing was given, ask one question: "What do you want to build?" and wait.
- Restate the request in one line: what they want, and the problem it solves.
- If it is really a product feature the team needs to agree on, a build from an existing `SPEC.md`, or a bug, say so and suggest `prd-interview`, `spec-interview`, `implement-spec` or `fix-bug` instead. Continue here only if the user still wants to.

## Step 1 — Start from what you already know

Read the conversation, the project folder and any files the user pointed to before asking anything. Note what is already answered and what is still unknown. Never ask something the user has already told you or that the code shows: it wastes their time and signals you weren't listening.

## Step 2 — Find the deciding unknowns

List the unknowns, then rank them by how much the answer would change the design. Ask about the top ones first. Typical deciding questions, by category (pick what applies; this is a menu, not a form):

- **Access and permissions:** What accounts, credentials, keys or network access do they actually have? Do they own the system or rent/borrow it? Is there an official API, or only an app or website?
- **Environment:** OS, where the tool will run (PC, phone, server, NAS, cloud), what is already installed, and comfort with the command line.
- **Inputs and outputs:** What data goes in, what should come out, in which format, and where it should end up.
- **Scale and conditions:** How much data, how often, and the network or hardware conditions (slow or distant links, flaky Wi-Fi, limited storage).
- **The real problem:** What do they do today, and what exactly goes wrong? Ask for the exact error, when it happens, and what they already tried.
- **Success:** How will they know it works? What would "good enough" look like?
- **Preferences and limits:** One-off command, double-click script, GUI or scheduled automation? Anything it must never do (delete data, cost money, touch production)? Any budget or time limits?

## Step 3 — Interview in short rounds

- Ask at most 4 questions per round, usually 1–3 rounds in total, and use the AskUserQuestion tool when available. Put the most decisive questions in round 1, because their answers often make later questions unnecessary.
- Offer concrete options with a recommended default where one exists, so the user can answer with a click. Add a one-line description explaining what each option means.
- Use the user's own words and plain language. Briefly explain any technical term you have to use.
- When an answer depends on outside facts (does this service have an API, what does this provider allow, which library supports this), research it yourself, with web search or docs, instead of asking the user. Verify, don't guess.
- Stop interviewing once you can pick an approach with confidence. Write the remaining minor unknowns into the plan as assumptions.

## Step 4 — Check feasibility honestly

Decide whether what they asked for can actually work with the access and constraints they have. If it can't, say so plainly and give the best real alternatives (a manual workflow, a different tool, asking the provider for access). Don't propose workarounds that break a service's terms, defeat security, or reverse-engineer private protocols. A short honest "a script can't do this for you, but here's what will help" is worth more than a tool that won't work.

## Step 5 — Present the plan

Use this structure so the user can check it in a minute:

```
## What I understood
<3-6 bullets: their situation, access, environment, data, the real problem, the success criteria>

## Recommended approach
<one or two sentences: the approach and why it fits them; mention the main alternative only if it's a close call>

## How it will work
<numbered steps in plain language>

## What I will build
<files, locations, dependencies to install, anything it will change on their system>

## Not included
<things deliberately left out, so scope is clear>

## Assumptions, risks and unknowns
<what is unverified and what could go wrong>

## How I'll test it
<how you'll show it works before handing it over, and what will remain untested>

Shall I go ahead? You can say yes, or change anything above first.
```

Then stop and wait for their reply.

## Step 6 — The approval gate

Until the user explicitly approves (for example "yes", "go ahead", "approved", or picking a "proceed" option), don't write implementation code, install packages, create environments, change config, or connect to their systems. Reading files and researching to improve the plan are fine.

- "How does it work?", "tell me more" or a follow-up question is **not** approval. Answer it, update the plan if needed, and ask again.
- If they approve with changes, show only the changed parts of the plan and continue.
- If, while building, you find the plan needs to change in a way the user would care about (new dependency, different approach, bigger scope, anything destructive), pause, explain briefly and get an OK. Small internal details don't need re-approval.

## Step 7 — Build, test, report

- Build exactly what was approved. Reuse existing code in the project where it fits.
- Test it the way the plan said, ideally including the failure the user cares about (for example, simulate a dropped connection when the tool is meant to resume).
- Don't commit or push. Leave that to the user unless they ask.
- Report honestly: what was built (with file paths), what was tested and how, what is still untested, the exact commands to run on their OS, and what to do if it fails.
