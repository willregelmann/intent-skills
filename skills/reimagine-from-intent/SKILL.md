---
name: reimagine-from-intent
description: Commissions three independent clean-room designs from a project's INTENT.md alone, then compares them against the current design to separate choices the intent forces from choices that are merely historical — use when the user says "how would we build this today", "reimagine this from scratch", "is our architecture still the right one", "what would a greenfield version look like", or before committing to a major refactor
tags:
  - intent
  - architecture
  - clean-room
  - design-review
---

# Reimagine From Intent

Hand a project's intent — and nothing else — to three designers who cannot see the project. Where all three land in the same place, the intent forces that choice. Where they scatter, the choice was always free. Then put the real design against that map and find out which of its decisions are implied by what the project is for, and which are just what happened.

The comparison is the deliverable. A greenfield design on its own is an opinion; three of them agreeing is a property of the intent.

## Quick Reference

| Step | Job | Output |
|------|-----|--------|
| 1 | Scrub the intent into a brief | Design-free brief + justified constraints |
| 2 | Commission three independent designs | Three full designs, each self-annotated |
| 3 | Build the convergence map | Every dimension marked Forced, Contested, or Arbitrary |
| 4 | Place the current design against it | Per-dimension findings |
| 5 | Steelman the current design | Verdict per divergence |
| 6 | Propose migration | Sequenced items, Greenfield-wins only |
| 7 | Report | Map + verdicts + proposal; `REIMAGINING.md` on request |

## Process

### 1. Scrub the Intent Into a Brief

Read `INTENT.md`. **Do not proceed without it** — commissioning designs against intent you inferred in the same session produces three elaborations of your own guess.

`INTENT.md` is mostly design-free, but mechanism appears in it — sometimes wrongly, sometimes because the mechanism *is* the point. Scan every statement for implementation: technology names, storage mechanisms, process descriptions, anything answering *how* rather than *what for*. Then sort what you find into three piles, because they are handled differently:

- **Constitutive** — the mechanism is part of what the project *is*. Removing it does not yield the same project built differently; it yields a different project. "There is no database to write to, so every change is a pull request" is constitutive for a git-driven catalog: the governance model is the product, not an implementation of it.
- **Genuine constraint** — externally imposed and non-negotiable. An upstream API contract, a regulatory requirement, a platform the users are already on.
- **Incidental leak** — a mechanism that got written into the statement of purpose because it is what the project happens to do today. "Users authenticate via OAuth" for a project whose purpose is team collaboration: swap it for SAML and nothing about the project's identity changes.

**The test:** if you could swap the mechanism out and the project would still be recognizably the same project serving the same purpose, it is design and it comes out. If swapping it out makes it a different project, it is intent and it stays.

Present your classification and ask the user to confirm it. Getting this wrong in either direction ruins the run — scrub something constitutive and the three designs will solve a different problem than the one you have; keep an incidental leak and they will reproduce your current architecture and call it convergence.

For anything you have classified as a genuine constraint, apply the standard a spec-driven rewrite uses: *"we do it this way" is not a justification.* "The upstream API requires it" or "the user has fixed it" is.

Produce the brief:

```
## What this is for
<purpose and users, with incidental mechanism removed and constitutive
 mechanism kept — a git-driven catalog's brief still says the source of truth
 is a reviewable commit history, because that is the project, not a way of
 building it>

## What would count as success
<the success criteria, verbatim — these are targets, they don't leak>

## What this deliberately does not do
<the non-goals>

## Hard constraints
<only the externally-imposed constraints confirmed as genuine, each with its
 justification>

## Explicitly unconstrained
<every mechanism removed in the scrub, named>
```

The last section is what gives the designers permission to diverge. Without it they will reconstruct what they infer you already have.

### 2. Commission Three Independent Designs

Launch three subagents. Each receives **only the brief**.

Never pass: the project path, repository access, the technology stack, file listings, your own reading notes, or any other design.

**Give all three the identical prompt.** Do not assign them different perspectives, lenses, or priorities — no "optimize for simplicity" for one and "optimize for scale" for another. Manufactured variation destroys the only measurement this skill has: three agents given different instructions will diverge for reasons that tell you nothing about the intent.

```
You are designing a system from scratch, from a specification of what it is for.
No implementation exists. Do not assume any particular technology, structure, or
prior art beyond what the brief states.

[Paste the brief verbatim]

Produce a complete design:
- Major components and their boundaries
- The data model — entities, relationships, identity, lifecycle
- Interfaces — APIs, schemas, command surfaces, data formats
- Technology choices, with the reasoning for each
- The primary workflows, end to end

For every significant decision, state which line of the brief drove it. Where a
decision is not determined by the brief and you are choosing on judgment alone,
mark it EXPLICITLY as arbitrary. Being honest about what the brief does not
constrain is more valuable than defending a preference.
```

That last instruction makes each design annotate its own forced-versus-free boundary, which Step 3 then cross-checks rather than having to infer.

### 3. Build the Convergence Map

**Before looking at the actual project.** Reading it first turns the map into a search for confirmation.

Enumerate the design dimensions the three addressed — data model, identity, storage, interface surface, workflow, distribution, technology, and so on. For each:

| Marking | Condition | Meaning |
|---------|-----------|---------|
| **Forced** | All three chose materially the same thing | The intent implies it |
| **Contested** | Two agree, one differs | Weak signal — read the dissent before weighting it |
| **Arbitrary** | All three differ, or all three flagged it arbitrary | The intent does not constrain this |

**Compare by consequence, not vocabulary.** Two designs have made the same choice when they produce the same observable behavior and the same constraints, whatever they called it. Two designs that both say "event-driven" and mean different things have not converged.

Where a designer's self-marking disagrees with the convergence — three designs independently making the same choice while each calls it arbitrary — trust the convergence. Three people reaching for the same tool unprompted is evidence, whatever they believe about why.

### 4. Place the Current Design Against the Map

Now read the project. For each dimension:

- **Current matches a Forced choice** → **Validated.** Report it. Knowing which parts of your architecture are entailed by your purpose is as useful as knowing which parts aren't, and it is the only output of this skill that protects good decisions from being churned.
- **Current differs from a Forced choice** → **Divergence.** The highest-value finding, and the input to Step 5.
- **Current sits inside an Arbitrary dimension** → **No finding.** Say nothing beyond noting the dimension is unconstrained. Never recommend changing a choice the intent does not reach — this is the single easiest way for this skill to waste a team's quarter.
- **Current has a component no greenfield design produced** → **Candidate incidental complexity.** Something exists that the purpose does not call for. It may still be load-bearing; Step 5 decides.
- **Greenfield designs produced something the current lacks** → **Candidate gap.** Check it against `ALIGNMENT.md` first — if it is already a known unmet criterion, it belongs to that plan, not this one.

### 5. Steelman the Current Design

For every Divergence and every Candidate incidental complexity, actively try to defend what exists. A clean-room designer has never met production; the current design may be carrying knowledge the brief could not contain.

Go looking for it: git history around the decision, code comments explaining a non-obvious choice, ADRs, issue and PR discussion, incident fixes. A comment that reads *"filtering to main meant a stacked PR got no checks at all"* is precisely the kind of thing three greenfield designs will never produce.

Verdict per item:

| Verdict | Meaning | Consequence |
|---------|---------|-------------|
| **Greenfield wins** | No defense found. The current choice is drift, inertia, or a solution to a problem that no longer exists | Eligible for Step 6 |
| **Current wins** | It encodes knowledge the intent does not capture | The finding is that `INTENT.md` is incomplete — surface it, do not migrate |
| **Open** | A real tradeoff with no clear winner | Report as a decision the team should make deliberately |

**"Current wins" is a finding about the intent, not a null result.** If the real design is right for a reason the brief never mentioned, the brief is missing something — recommend a `discover-intent` pass over that area.

Never let cleanliness decide a verdict. A greenfield design is always tidier because nothing has happened to it yet.

### 6. Propose Migration

**Only for Greenfield-wins items.** Contested dimensions, Arbitrary dimensions, Current-wins, and Open all produce no migration work.

Sequence by what unblocks what, and for each item give: the divergence, the smallest step toward the greenfield choice, what it costs, and what it makes possible.

**Prefer incremental convergence to replacement.** A rewrite is almost never the right output — if the sequenced plan cannot be expressed as steps that each leave the project working, say so plainly rather than proposing a rebuild.

**Boundary with `intent-alignment`:** that skill's remediation closes gaps between the project and its success criteria. This one closes gaps between the project's design and the design its purpose implies. An item appearing in both belongs to the alignment plan — defer to its sequencing and cross-reference rather than duplicating it.

### 7. Report

Deliver in the session, always:

1. **The convergence map** — every dimension, marked, with a one-line note on what the three designs did
2. **Validated choices** — where the current design matches a Forced choice
3. **The verdict table** — every divergence with its steelman result
4. **The migration proposal**
5. **Any incompleteness in `INTENT.md`** the Current-wins verdicts exposed

Write `REIMAGINING.md` to the project root **only when the user asks**. Date it and mark it as a point-in-time comparison. Overwrite wholesale on a later run — it is a snapshot, not a log.

## Common Mistakes

- **Handing over the unscrubbed intent.** Every mechanism left in the brief is a design decision the "independent" designers will reproduce, and you will read that reproduction as convergence.
- **Assigning the three designers different perspectives.** It feels like thoroughness. It destroys the measurement — you can no longer tell intent-driven convergence from instruction-driven divergence.
- **Letting a designer see the project.** Even the stack, even "for context." A designer who knows the current answer will produce a defense of it.
- **Recommending changes in Arbitrary dimensions.** The intent does not constrain them, three designs proved it, and churning them costs real time for no gain.
- **Mistaking tidiness for superiority.** The greenfield designs have no users, no incidents, and no history. That is why they look better, and it is not an argument.
- **Skipping the steelman.** Without it this skill reliably concludes "rewrite everything," which is both wrong and the reason nobody runs it twice.
- **Comparing vocabulary instead of consequence.** Three designs using the same word have not necessarily converged; three using different words may have.
- **Duplicating the alignment plan.** If an item is already a known unmet criterion, it belongs there.

## Key Principles

- **Convergence is the measurement.** A single greenfield design is one agent's taste. Three agreeing, from identical briefs, independently, is a property of the intent itself.
- **Identical brief, independent execution.** Any variation you introduce between the designers is variation you cannot interpret afterward.
- **Most dimensions are Arbitrary, and that is the normal result.** Intent constrains far less of a design than it feels like it should. Naming what is genuinely unconstrained is what stops the rest of the findings from being noise.
- **Where the current design wins, the intent is incomplete.** Hard-won production knowledge that never made it into `INTENT.md` is a gap in the intent, not a victory over the exercise.
- **Validation is an output.** Learning that your architecture is entailed by your purpose is worth as much as learning that it isn't, and it is the finding that protects good design from restless churn.
- **Incremental convergence, never a rewrite.** If the gap cannot be crossed in steps that each leave the project working, that fact is the finding.
