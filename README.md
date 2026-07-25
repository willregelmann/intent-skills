# Intent Skills

Agent skills for working with project intent — what a project is *for*, as distinct from what it does or how it's built.

## Skills

| Skill | What it does |
|-------|-------------|
| [discover-intent](skills/discover-intent/SKILL.md) | Reconciles what the docs claim against what the code does, escalates the disagreements, and produces an `INTENT.md` |
| [intent-alignment](skills/intent-alignment/SKILL.md) | Assesses a project against its `INTENT.md`, producing per-statement verdicts and a prioritized remediation plan |
| [reimagine-from-intent](skills/reimagine-from-intent/SKILL.md) | Commissions three clean-room designs from the intent alone and compares them against the current design |

## Install

This repository is a Claude Code plugin and its own single-plugin marketplace:

```
/plugin marketplace add willregelmann/intent-skills
/plugin install intent-skills@intent-skills
```

The skills activate automatically when a task matches their description, or you can invoke one directly by name.

Cross-agent support isn't a goal — `reimagine-from-intent` requires spawning independent subagents, which not every host can do.

## The Intent Layer

Intent is the *what for* — one layer above requirements, two above implementation:

| Layer | Example |
|-------|---------|
| Implementation | Uses Postgres with a normalized schema |
| Requirements | Item history is retained for 10 years |
| **Intent** | **Item history must survive a decade of ownership changes** |

`INTENT.md` covers purpose, users, success criteria, and non-goals. It deliberately excludes architecture and design rationale — with one exception: a mechanism belongs in intent when the project would be a *different project* without it. A catalog whose source of truth is a reviewable commit history is not a catalog that happens to use git.

The document is claims and nothing else — no citations, no provenance table, no title or preamble. It opens on the purpose and ends with what's still unresolved.

Crucially, it holds **targets, never measurements**: no counts, no percentages, no "currently." A criterion written as a target stays true until the intent changes; one with a number in it is wrong by the next commit. Measuring how far along a project actually is belongs to `intent-alignment`, whose output is explicitly a dated snapshot.

## Structure

Each skill is a standalone `SKILL.md` with YAML frontmatter in its own directory:

```
.claude-plugin/
  plugin.json        # plugin manifest
  marketplace.json   # single-plugin marketplace, so the repo installs itself
skills/
  discover-intent/SKILL.md
  intent-alignment/SKILL.md
  reimagine-from-intent/SKILL.md
```

No build system, no dependencies. The artifacts are markdown files.
