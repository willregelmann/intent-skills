## Purpose

Intent Skills is a collection of Claude Code skills for working with a project's
intent — what it is *for*, as distinct from what it does or how it is built.
Projects accumulate documentation that describes their mechanics and says
nothing about their purpose, and purpose that lives only in the heads of the
people who set it. These skills recover it, write it down, and keep the written
version honest.

`INTENT.md` is the artifact at the center. Skills attach to it: one produces it,
others read it to assess a project against its purpose or to test which of its
design choices that purpose actually forces. More may attach later.

The skills never settle intent on their own. They gather evidence, reconcile
what a project says against what it does, and frame the disagreements as
questions — but the person who owns the project answers them. A version that
concluded what a project was for without being told would be a different tool.

## Users

- **A maintainer arriving at an unfamiliar or drifted project** — needs to
  recover what it is for without a person to ask
- **A maintainer deciding what to build next** — needs to know how far the
  project sits from its purpose, and which of the gaps matter
- **A maintainer weighing a significant change** — needs to know which design
  choices the purpose forces and which are only history
- **Later skills in this collection** — attach to `INTENT.md` as a stable spine,
  and need it to mean the same thing every time it is read

## Success Criteria

Falsifiable, and stated as targets rather than current measurements. If every
one of these held, the project has achieved its intent.

1. A maintainer can recover a project's intent from its documentation and source
   without consulting anyone who worked on it.
2. Every disagreement between what a project says and what it does reaches the
   person who can settle it, rather than being resolved by a skill.
3. `INTENT.md` stays true until the intent itself changes — nothing in it goes
   stale because the project moved.
4. A new skill can attach to `INTENT.md` without changing the artifact's shape or
   requiring the existing skills to be revised.
5. Anyone can install the collection from a published repository.
6. Every finding a skill reports is specific enough to act on without redoing the
   investigation that produced it.

## Non-Goals

- **Testing judgment** — these skills produce judgment calls about projects that
  are each unique; a fixture proving one repository works says nothing about the
  next. Correctness is established by running them and reading the output
- **Cross-agent portability** — these are Claude Code skills. The file format
  happens to be portable, but supporting other agents is not a goal and does not
  constrain what a skill may require of its environment
- **A durable document that reports status** — `INTENT.md` never carries counts,
  percentages, or progress; measuring how far along a project is belongs to a
  dated snapshot that is allowed to go stale
- **Replacing a project's own documentation** — `INTENT.md` answers what a
  project is for, not how to use it or how to build it, and does not supersede a
  README

## Open Questions

| # | Question | Why it matters | Status |
|---|----------|----------------|--------|
| 1 | Which parts of the intent layer remain uncovered, and what makes a candidate skill belong here? | The collection is explicitly open to more skills, but nothing defines the boundary, so there is no way to say a proposed skill is out of scope. | Open |
| 2 | Nothing keeps `INTENT.md` current on its own — it is revised only as a side effect of running an assessment. Should drift detection be a job the collection has? | An intent document that silently goes stale is worse than none, and Criterion 3 depends on someone noticing. | Open |

---

Discovered 2026-07-25.
