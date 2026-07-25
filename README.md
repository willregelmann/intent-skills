# Intent Skills

Agent skills for working with project intent — what a project is *for*, as distinct from what it does or how it's built.

## Skills

| Skill | What it does |
|-------|-------------|
| [discover-intent](discover-intent/SKILL.md) | Reconciles what the docs claim against what the code does, escalates the disagreements, and produces an `INTENT.md` |
| [intent-alignment](intent-alignment/SKILL.md) | Assesses a project against its `INTENT.md`, producing per-statement verdicts and a prioritized remediation plan |
| [reimagine-from-intent](reimagine-from-intent/SKILL.md) | Commissions three clean-room designs from the intent alone and compares them against the current design |

## Install

```
npx skills add willregelmann/intent-skills -g -y
```

The `-g` flag installs globally (user-level) so the skills are available in all projects. Omit it to install project-level instead.

These are Claude Code skills. The `SKILL.md` format is portable to other agents, but cross-agent support isn't a goal — `reimagine-from-intent` requires spawning independent subagents, which not every host can do.

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
discover-intent/SKILL.md
intent-alignment/SKILL.md
reimagine-from-intent/SKILL.md
```

No build system, no dependencies. The artifacts are markdown files.
