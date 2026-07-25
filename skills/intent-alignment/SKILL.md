---
name: intent-alignment
description: Assesses how well a project meets the intent recorded in its INTENT.md, producing per-statement verdicts and a prioritized remediation plan — use before a planning cycle, a release, or a decision about what to work on next, or when the user says "how are we doing against our goals", "is this on track", "check alignment", or "are we still building what we set out to build"
tags:
  - intent
  - assessment
  - planning
  - codebase-analysis
---

# Intent Alignment

Measure a project against its own stated intent. `INTENT.md` says what the project is for and what would count as achieving it; this skill says how far along it actually is, and what to do about the gaps.

`INTENT.md` is deliberately timeless and carries no measurements. This assessment is the inverse: a dated snapshot, and the right place for every count, percentage, and "not yet" that the intent document refuses to hold.

## Quick Reference

| Step | Job | Output |
|------|-----|--------|
| 1 | Load the yardstick | Fixed list of assessable statements |
| 2 | Decide what would count as evidence | Per-statement evidence criteria, set before looking |
| 3 | Gather evidence | Findings per statement |
| 4 | Assign and freeze verdicts | Verdict table — final before any remediation thinking |
| 5 | Escalate intent-level defects | Confirmed edits to `INTENT.md` |
| 6 | Build the remediation plan | Prioritized, sequenced actions |
| 7 | Report | Characterization + table + plan; `ALIGNMENT.md` on request |

## Process

### 1. Load the Yardstick

Read `INTENT.md` at the project root. **Do not proceed without it.** If it is missing, say so and offer to run `discover-intent` first — assessing a project against intent you inferred five minutes ago is grading your own guess.

Check the discovery date in its footer against the project's recent history. If substantial work postdates it, say so up front: the assessment is only as good as the intent it measures against, and a stale yardstick produces confident nonsense.

Enumerate every assessable statement **before looking at the project**:

- The **Purpose** — treated as one statement
- Each **User** — is this person actually served?
- Each **Success Criterion**
- Each **Non-Goal**

**Open Questions are not assessed.** They are unresolved intent, not targets. They feed Step 5 — evidence may now settle one.

Fix this list before gathering evidence. A list assembled while reading code becomes a list of whatever was convenient to check.

### 2. Decide What Would Count as Evidence

For each statement, write down what would demonstrate it holds, and what would demonstrate it fails — *before* going to look. Two sentences each is enough.

This is not ceremony. Deciding the standard after seeing the evidence is how every criterion ends up "partially met."

### 3. Gather Evidence

Sources, by what they reveal:

- **Code and data** — what exists and what it can do
- **Tests** — what the project considers important enough to protect
- **CI and automation** — what it enforces continuously versus what it merely hopes for
- **Git history** — where effort is actually going. For each criterion, ask which recent changes serve it. A criterion with no work behind it is a different diagnosis from one being actively pursued and missed.
- **Issue tracker and open PRs** — what is acknowledged but unfinished
- **Runtime behavior** — where a criterion is about what the thing does, not what it contains

Record the specific evidence per statement — `file:line`, commit, count, measurement. Unlike `INTENT.md`, this artifact wants numbers.

### 4. Assign and Freeze Verdicts

For each **Purpose, User, and Success Criterion**:

| Verdict | Meaning |
|---------|---------|
| **Met** | Evidence supports it. Name the evidence. |
| **Partially met** | Holds in some areas, fails in others. Name the boundary precisely — which parts, not "mostly." |
| **Unmet — not attempted** | Nothing in the project works toward it. |
| **Unmet — attempted, not reached** | Real work exists; the gap is scale or execution. |
| **Unmet — structurally blocked** | The current approach cannot reach it. Name the specific blocker. |
| **Unassessable** | Cannot be tested as written. A defect in `INTENT.md`, not in the project → Step 5. |

For each **Non-Goal**:

| Verdict | Meaning |
|---------|---------|
| **Respected** | No evidence of the thing the project said it would not do. |
| **Eroding** | Pressure is visible — a field, a dependency, a feature request — but the line has not been crossed. |
| **Violated** | The project is doing it. Name exactly where. |

Give non-goals full attention. Scope creep is invisible from inside a project, and catching it is the thing an outside assessment is uniquely good at. A skipped non-goal check is a wasted run.

The three **Unmet** verdicts are not interchangeable. Not-attempted needs a decision about priority; attempted-not-reached usually needs nothing but time; structurally-blocked needs someone to change the approach, and no amount of effort will substitute.

**Do not produce a score, grade, or percentage-aligned figure.** A single number hides the distribution and invites gaming. One honest paragraph characterizing the project's alignment is worth more, and cannot be quoted out of context as a target.

**Write the complete verdict table down before continuing.** The verdicts are final at this point. Step 6 may not revise them — knowing that a gap is expensive to close is exactly the pressure that turns "unmet" into "partially met," and the whole value of the assessment depends on it not being applied.

### 5. Escalate Intent-Level Defects

Some findings are about the intent, not the project. Batch them and ask:

- **Unassessable statements** — the criterion cannot be tested as written. Propose a falsifiable replacement.
- **Non-goals the project has deliberately outgrown** — a violation that looks intentional and reasoned is a candidate for the non-goal being wrong, not the code. Present it as a question, never assume.
- **Open Questions the evidence now settles** — say what the evidence shows and propose the resolution.
- **Criteria the Purpose no longer implies** — intent drift the last discovery run did not catch.

On confirmation, edit `INTENT.md` in place, respecting its format: targets not measurements, no origin tags, no title or preamble, dated footer at the bottom. Update the footer date when you change content.

**Never edit `INTENT.md` without confirmation, and never weaken a criterion to match what was built.** A failing criterion is trivially made to pass by lowering the bar, and that converts an honest assessment into a rubber stamp. A criterion is only rewritten because it was badly stated, never because it was missed.

### 6. Build the Remediation Plan

Now — with verdicts frozen — propose what to do. Priority order:

1. **Non-goal violations.** Something is happening that the project said should not. Stopping is usually cheap and the cost of continuing compounds.
2. **Structurally blocked criteria.** Nothing improves until the blocker is addressed, and these need a decision rather than effort. Surface them early so they are not mistaken for backlog.
3. **Unmet — not attempted**, where the criterion is load-bearing for the Purpose.
4. **Partially met.** Finishing something beats starting something.
5. **Unmet — attempted, not reached.** Already moving; often the right action is none.

Each item carries: the gap, the smallest change that would move the verdict, and which verdict it would move to. Note where one item unblocks another — sequence is often the most useful thing in the plan.

**The plan is bounded by `INTENT.md`.** Do not propose work the intent does not call for, however good the idea. If the most valuable thing you can see is out of scope, that is an intent question for Step 5's next run, not a remediation item.

### 7. Report

Deliver in the session, always:

1. **One paragraph** honestly characterizing where the project stands against its intent
2. **The verdict table** — every statement, its verdict, and its evidence
3. **The prioritized plan**
4. **Any `INTENT.md` edits made**, and any intent-level defect the user left unresolved

Write `ALIGNMENT.md` to the project root **only when the user asks**. It is the one artifact in this collection that should carry measurements — that is the whole reason it exists separately from `INTENT.md`.

Open with a one-line snapshot marker, then the same content you reported:

```markdown
*Point-in-time snapshot, <YYYY-MM-DD>. Measures the project against `INTENT.md`
as it stood on that date. Expected to go stale — re-run `intent-alignment`
rather than editing this file.*

## Assessment
<the characterization paragraph>

## Purpose / ## Users / ## Success Criteria / ## Non-Goals
<verdict tables, evidence column carrying the actual numbers>

## Remediation
<prioritized items, each naming the smallest change and the verdict it moves>

## Unresolved
<intent-level defects the user did not settle>

## Changes made to INTENT.md this run
<each edit and why>
```

Overwrite the file wholesale on a later run rather than appending — it is a snapshot, not a log. If the user wants history, that is what git gives them.

## Common Mistakes

- **Assessing against intent you inferred.** If `INTENT.md` is missing, run `discover-intent` or stop. Grading a project against a yardstick you invented in the same session measures nothing.
- **Letting remediation cost color the verdict.** The reason Step 4 freezes before Step 6 exists. A criterion is unmet whether the fix takes an hour or a quarter.
- **Rewriting a criterion so it passes.** The most damaging possible outcome of this skill. Criteria are rewritten for being badly stated, never for being missed.
- **Skipping the non-goals.** They are the findings the project cannot generate for itself, and they are the easiest section to skim past.
- **Treating every unmet criterion the same.** Not-attempted, attempted-and-short, and structurally-blocked call for a priority decision, patience, and a change of approach respectively.
- **Scoring it.** "68% aligned" is worse than useless — it will be quoted, tracked, and optimized, and it says nothing about which third is missing.
- **Assessing the Open Questions.** They are open. Evidence may settle one, which is a Step 5 escalation, not a verdict.
- **Grading effort.** A criterion with heroic work behind it and no result is unmet. Note the effort in the evidence; do not let it move the verdict.

## Key Principles

- **`INTENT.md` is the yardstick, not your opinion of what the project should be.** Every finding traces to a statement in it. A gap you cannot trace to a statement is either out of scope or an intent question.
- **The verdict comes before the plan.** Assessment contaminated by remediation cost is not assessment.
- **Three kinds of unmet, three responses.** Collapsing them is what turns an assessment into a backlog.
- **A criterion you cannot assess is a defect in the intent.** The project did not fail that one; the intent document did.
- **Never fix a failing grade by lowering the bar.** The temptation appears in every run, and yielding to it once makes every future run worthless.
- **This document is allowed to go stale.** It is a snapshot with a date on it. `INTENT.md` is the thing that must stay true.
