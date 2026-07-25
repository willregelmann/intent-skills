# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

A collection of Claude Code skills for working with project intent — recovering it, assessing a project against it, and testing which of a project's design choices its intent actually forces. Each skill is a standalone SKILL.md file in its own directory, installable as a Claude Code plugin.

The collection's own intent is recorded in [INTENT.md](INTENT.md), produced by running `discover-intent` against this repository. Read it before adding or reshaping a skill — in particular its Non-Goals, which are what make "this is out of scope" a thing you can say here. Note that nothing currently keeps that file current on its own; it is revised only when an assessment run escalates a change.

## Repository Structure

```
.claude-plugin/plugin.json       — plugin manifest
.claude-plugin/marketplace.json  — single-plugin marketplace; the repo installs itself
skills/<skill-name>/SKILL.md     — one directory per skill, one file per skill
```

This is a Claude Code plugin. Skills live under `skills/` and are auto-discovered — a new skill needs no manifest entry, only its own directory with a `SKILL.md`. Bump `version` in **both** manifests together when releasing; they drift silently otherwise.

There is no build system, no tests, no dependencies. The artifacts are markdown files with YAML frontmatter.

## Skill Format

Every SKILL.md must have:
- YAML frontmatter with `name` (lowercase, hyphens) and `description` (single line explaining what it does and when to use it)
- A structured process section with numbered steps
- A quick reference table summarizing the steps
- Common mistakes section (what goes wrong when the skill is misapplied)
- Key principles section (the non-obvious insights that make it work)

## Writing Conventions

- Skills are instructions for an AI agent, not documentation for humans. Write imperatively.
- Each skill should produce a specific deliverable (INTENT.md, verdict table, etc.) — name it explicitly.
- Processes should have clear phase boundaries where one step's output feeds the next.
- "Common Mistakes" should describe failure modes specific to this skill, not generic advice.
- Claims, criteria, and findings must be falsifiable/specific — never vague.

## The Intent Layer

These skills share a definition of intent: the *what for*, one layer above requirements and two above implementation. Purpose, users, success criteria, and non-goals are in scope. Architecture, design rationale, and technical constraints are not.

The exception: a mechanism belongs in intent when the project would be a *different project* without it. Swap the mechanism out — if what remains is the same project built differently, it is design; if what remains is something else, it is intent. A catalog whose source of truth is a reviewable commit history is not a catalog that happens to use git.

`INTENT.md` holds **targets, never measurements** — no counts, no percentages, no "currently," no "today it cannot." A statement written as a target stays true until the intent changes. Current state belongs in `ALIGNMENT.md`, which is explicitly a dated snapshot and is the only artifact here that should carry numbers.

`INTENT.md` is the shared artifact. It carries claims and nothing else — no origin tags, no provenance table, no citations. Every claim reads as equally load-bearing, so a skill that revisits the document re-derives its claims from the current docs and source rather than trusting what it finds there, and escalates anything that no longer holds instead of rewriting it silently.

## Current Skills

- **discover-intent** — Reconciles stated intent (docs) against enacted intent (source), escalates the disagreements, and produces INTENT.md
- **intent-alignment** — Assesses a project against its INTENT.md, producing per-statement verdicts and a prioritized remediation plan
- **reimagine-from-intent** — Commissions three clean-room designs from INTENT.md alone and compares them against the current design, separating forced choices from historical ones
