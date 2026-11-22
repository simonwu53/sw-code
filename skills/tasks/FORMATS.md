# Task file formats

## description.md

```
# <Short title: what the task delivers>

**Created:** YYYY-MM-DD · **Status:** <one line, dated>
**Trigger:** <why now: incident, request, predecessor task>        (optional)
**Related:** <code paths, doc pages, other task folders>            (optional)

## Context         what is known, with numbers and file paths (run ids, row counts, gaps)
## Goal            the deliverable, stated so that completion is checkable
## Plan            numbered stages; decision thresholds fixed before running experiments
## Out of scope    what this task deliberately leaves alone                (optional)
## Open questions  decisions the user must make; delete or answer them as they close
```

**Status line.** One line, always dated, always current. Update it in place at every milestone rather than appending a log: `Stage 2 done 2026-08-06 (see stages/stage2.md); Stage 3 open`. When the task closes it reads `done <date>` plus any lasting caveat.

**Decisions** are recorded where they were made (a `## Decision` section, or the stage file), with the options considered and why the others were rejected. Keep every rejected option: a later reader needs to see it was considered.

## implementation.md

```
# Implementation: <task title>

## Scope           files in scope; files not to touch
## Steps           numbered, each small enough to check on its own
## Tests           per test: where it goes, the behavior it checks, the break that turns it red
## Acceptance      the command(s) and expected result that prove the task is done
```

## stages/stageN.md

The goal of the stage, what blocks it (`Blocked by: stage1`), the method, and the decision thresholds. Results are appended under `## Findings`, or in a separate `stageN-findings.md` when they are long.

## analysis/<issue-name>.md

For incidents and anomalies: the error verbatim, the trigger case and reproduction command, the root cause as a causal chain, why earlier checks missed it, the options considered, and the chosen fix.

## summary.md

Written when the task is done. It is the handover document: assume the reader has not read `description.md`.

- **Change**: files touched, new constants, schema changes
- **Result**: a before/after table against a named run
- **Notes**: reruns required, thresholds, follow-ups

## tasks/findings-index.md

One file for the whole project, so a later task can find what earlier tasks learned without opening each folder. One line per finding worth keeping beyond its task:

```
- 2026-10-12 · OSRM car routing is 40× faster than OTP on the same OD set. → [router-benchmark/stages/stage2.md](router-benchmark/stages/stage2.md)
```

- The date the finding was made, one sentence with the fact or number, and a link relative to `tasks/` to the file that holds the details.
- Lines are added when a stage records the finding or at close, after the user agrees.
- When the task moves to `done/`, its lines are edited to point to `done/<task-name>/`. A line is never added twice.
