---
name: discover-intent
description: Extracts a project's high-level intent from its documentation and source, reconciling what the docs claim against what the code actually does, and produces an INTENT.md — use when onboarding to an unfamiliar codebase, before a significant change, or when the user says "what is this project for", "figure out the intent", "why does this exist", or "write an INTENT.md"
tags:
  - intent
  - onboarding
  - documentation
  - codebase-analysis
---

# Discover Intent

Recover what a project is *for* from two sources that rarely agree: what the project **says** (documentation) and what the project **does** (source). Reconcile them, escalate every disagreement to the user, and write the result to `INTENT.md`.

The gaps between stated and enacted intent are the most valuable thing this skill produces. Do not paper over them.

## Quick Reference

| Step | Job | Output |
|------|-----|--------|
| 1 | Scope the project, pick fresh vs. update mode | Evidence inventory |
| 2 | Read stated intent (docs) | Candidate intent statements + sources |
| 3 | Read enacted intent (source) | What the code actually optimizes for |
| 4 | Reconcile the two lists | Per-statement verdict table |
| 5 | Escalate everything not Corroborated | User answers, batched |
| 6 | Write the deliverable | `INTENT.md` at the project root |

## What Counts as Intent

Intent is the *what for*, one layer above requirements and two above implementation.

| Not intent | Intent |
|-----------|--------|
| "Exposes a REST API for collections" (feature) | "Lets collectors catalog physical items without manual data entry" |
| "Uses Postgres with a normalized schema" (design) | "Item history must survive a decade of ownership changes" |
| "Should be fast" (vague) | "A catalog scan returns in under 2s so users don't leave the page" |

In scope for `INTENT.md`: **purpose, users, success criteria, non-goals, open questions.**

Out of scope: architecture, design rationale, technical constraints, requirements detail. If you catch yourself writing "we use X because Y," that is a design commitment — leave it out.

**One exception, and it is real.** A mechanism belongs in intent when the project would be a different project without it. The test: swap the mechanism out — if what remains is recognizably the same project built differently, it is design and it stays out; if what remains is a different project, it is intent and it belongs in Purpose. A catalog whose source of truth is a git history, where curation *is* opening a pull request, is not a catalog that happens to use git; the governance model is the product. Do not scrub that out in the name of surviving a rewrite. Everything genuinely incidental still goes.

## Process

### 1. Scope and Mode

Confirm what the project *is* before reading it. In a monorepo, intent is per-deliverable — ask which package or root to analyze rather than producing one blurred document for all of them.

If `INTENT.md` already exists, run in **update mode**: read it first and treat every existing claim as a candidate, not as settled. The document records no origin for its claims, so a re-run cannot tell which ones a maintainer confirmed and which the last run inferred — re-derive all of them from the current docs and source, and put anything that no longer holds through Step 5 again rather than silently rewriting it. Report drift explicitly: what changed, what was dropped, and what is newly in question.

Build an evidence inventory before reading anything in depth:

- Docs: `README`, `docs/`, `CLAUDE.md` / `AGENTS.md`, ADRs, design notes
- Manifest: `package.json`, `pyproject.toml`, `Cargo.toml` — name, description, dependencies, scripts
- Code surface: entry points, public API, routes, CLI definitions, data model
- Tests: test names are executable statements of what matters
- History: recent commit subjects, open issue titles

Intent concentrates in a small fraction of a codebase. Read entry points, the public surface, the data model, and the tests. Skip vendored code, generated code, lockfiles, and leaf utilities.

### 2. Stated Intent

Sources, ranked by signal:

1. **Explicit purpose statements** in README or docs — highest signal, often the stalest
2. **`CLAUDE.md` / `AGENTS.md`** — written to be acted on, usually current
3. **ADRs and design docs** — capture *why*, which is exactly what you want
4. **Recent commit subjects and issue titles** — where the project is being pushed *now*
5. **Code comments** — narrow, but honest

For each candidate, record the claim and its `file:line`. Write claims as falsifiable statements about purpose, not summaries of features. When a doc gives you a feature list, convert each item into the purpose it serves — or mark it as not-yet-intent and keep looking.

### 3. Enacted Intent

Derive this list from the code **without consulting the Step 2 list**. Anchoring to the docs is the primary failure mode of this skill: it turns a reconciliation into a confirmation.

What to read for:

- **Where the care went** — which modules have tests, error handling, retries, and commit churn. Effort is a revealed preference; it names the real priority more reliably than any README.
- **What is load-bearing** — what most of the codebase depends on
- **The data model** — the entities and relationships the project commits to are a claim about what it thinks the world contains
- **The seams** — config, plugin points, and abstractions mark what the authors expected to vary; hardcoded values mark what they expected not to
- **Dependencies** — reveal the class of problem being solved and which externals are non-negotiable
- **The users** — CLI flags, API routes, UI screens, and auth roles enumerate who can do what
- **What is neglected** — a stated priority with no code behind it is a finding

Write these as intent statements in the same falsifiable form as Step 2, each with its `file:line` evidence.

### 4. Reconcile

Compare the two lists and assign every statement a verdict:

| Verdict | Meaning | Action |
|---------|---------|--------|
| **Corroborated** | Stated and enacted agree | Goes into `INTENT.md` |
| **Unsupported** | Stated, but no code backs it | Ask: aspirational, abandoned, or built elsewhere? |
| **Undocumented** | Enacted, but stated nowhere | Ask to confirm — often the real intent, sometimes an accident |
| **Contradicted** | Stated and enacted directly conflict | Ask which one is authoritative |
| **Self-defeating** | Coherent as stated, but cannot hold | Ask, naming the specific conflict |

**Self-defeating** is a narrow category. It applies when two intent statements cannot both be maximized, when the stated intent cannot be achieved by *any* implementation, or when a success criterion does not measure the purpose it claims to. It does not mean "I would have built this differently." If you cannot name the specific structural conflict in one sentence, it is not self-defeating — drop it.

Present the verdict table before moving on.

### 5. Escalate

Ask about **every** non-Corroborated item, batched into as few rounds as possible — ideally one.

Each question must carry: what you found, the specific evidence on both sides, and your best-guess answer offered as the default. The user should be able to confirm rather than compose.

If a structured-question interface caps how many questions fit in one call, that cap is not a limit on the escalation — split across calls, or ask the remainder as a numbered list in the same message. Never drop a question, or demote one to Open Questions, because of an interface limit. Open Questions records what the user left unresolved, not what you failed to ask.

- **Never** resolve a Contradicted or Self-defeating item by picking one side silently. That discards the finding the user most needs.
- **Do** ask about what only the user knows: who the users actually are, what success means, whether an abandoned direction is dead or paused, whether a stated constraint is real or habit.
- **Do not** ask about anything you could settle by reading more code. Every avoidable question spends attention a real ambiguity needed.
- Unresolved is a legitimate outcome. It goes in Open Questions, not into a confident guess.

### 6. Write INTENT.md

Write to `<project-root>/INTENT.md`.

No title and no preamble — the filename says what the document is, and the first thing a reader should hit is the purpose.

```markdown
## Purpose

<2–4 sentences: the problem, who has it, and why this exists to solve it.>

## Users

- **<user type>** — <what they are trying to accomplish>

## Success Criteria

Falsifiable, and stated as targets rather than current measurements. If every
one of these held, the project has achieved its intent.

1. <criterion>

## Non-Goals

- <what this deliberately does not do> — <why>

## Open Questions

| # | Question | Why it matters | Status |
|---|----------|----------------|--------|
| 1 | <unresolved intent question> | <what it blocks or risks> | Open |

---

Discovered <YYYY-MM-DD>.
```

The dated footer is the only metadata the document carries, and it exists so a later run can tell how stale the claims are. Keep it at the bottom.

State targets, never measurements. `INTENT.md` says what the project is for and what would count as achieving it — not how far along it is. No counts, no percentages, no "currently," no "today it cannot." Those belong in the summary you report to the user, where they are useful and allowed to go stale. A criterion written as a target stays true until the intent changes; one written with a number in it is wrong by the next commit.

Every claim in the document stands on its own, unannotated. Do not tag claims by origin, and do not add a provenance table, a source column, or `file:line` citations — the evidence belongs in the discovery conversation, not in the artifact. `INTENT.md` states what the project is for; a reader who wants to know how a claim was reached runs the skill again.

Report a summary to the user: the purpose in one sentence, the number of claims by section, and any Open Questions that remain. Do not paste the whole file back.

## Common Mistakes

- **Summarizing the README.** If `INTENT.md` could have been written without opening a single source file, the skill did not run.
- **Listing features as intent.** "Supports OAuth" is a feature. "Lets teams onboard without sharing a password" is intent.
- **Baking the current state into the document.** "Today trading card games are 79% of items" measures progress; "coverage extends across many categories rather than concentrating in one" states the intent. The first is stale immediately and turns `INTENT.md` into a status report.
- **Unfalsifiable success criteria.** "Users are happy" cannot be assessed. "A new collector catalogs 20 items in under 5 minutes without typing a field name" can.
- **Anchoring on the docs.** Reading the README, then going to find code that agrees with it, produces a document that is confidently wrong wherever the project has drifted.
- **Resolving contradictions silently.** Quietly picking the doc or the code when they conflict destroys the highest-value finding.
- **Calling a design choice misguided.** The skill flags self-defeating *intent* — goals that conflict or cannot be met. Disliking the architecture is out of scope.
- **Smuggling in the design layer.** Constraints, architecture, and "we chose X because Y" do not belong in `INTENT.md`.
- **Over-scrubbing a constitutive mechanism.** The opposite error, and rarer but worse: stripping out the one design fact that defines what the project is, because a rule said mechanism does not belong in intent. Apply the swap test before removing anything.
- **Reading the whole codebase.** Intent lives in entry points, the data model, the public surface, and the tests.

## Key Principles

- **Docs state intent; code enacts it.** The reconciliation is the skill. Either source alone is a summary, not a discovery.
- **Effort is a revealed preference.** Where tests, error handling, and commit churn cluster is what the project is actually about, whatever the README says.
- **Non-goals are as load-bearing as goals.** A project with no stated non-goals has no defense against scope creep, and no way to say a change is out of bounds.
- **The document carries claims, not their pedigree.** Everything in it reads as equally load-bearing, which is why a re-run re-derives rather than trusts: nothing in the file marks which claims a maintainer stood behind.
- **Unresolved beats invented.** An honest Open Question is worth more than a confident guess that later gets cited as fact.
