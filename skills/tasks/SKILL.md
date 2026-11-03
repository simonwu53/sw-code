---
name: tasks
description: Run coding work through a task folder (tasks/<name>/) from agreement to close. Use when coding work starts in a project (a feature, fix, experiment, refactor, or investigation), and when opening, planning, delegating, reviewing, or closing a task.
---

# Tasks

Coding work runs through a task folder. The folder records what was agreed, and it is the brief for whoever implements it. Discuss the task with the user, question each of their decisions as a copilot would, and agree the plan before any code changes.

**Exceptions.** When the user says "go ahead" (or similar) for a change, do it directly, without a task. A small change that still deserves a record gets only a `description.md`, with the plan inline.

## Layout

```
tasks/
  <task-name>/            active task, kebab-case, no dates in the name
    description.md        required: context, goal, plan, status
    implementation.md     the concrete plan; required when the task is delegated or staged
    summary.md            required on completion
    stages/stageN.md      large tasks only: one file per stage
    analysis/<issue>.md   diagnosis of an incident or anomaly
    scripts/              scripts this task used, with a note on how to run them
    docs/                 references this task used (PDFs, lookups, notes)
  backlog/<task-name>/    agreed but not started; moved to tasks/<task-name>/ when work begins
  done/<task-name>/       finished tasks, moved as a whole folder
```

Where code, tests, and run outputs go is project-specific: the project's CLAUDE.md says. Task folders made before this layout keep their files as they are (`findings.md`, `MEMORY.md`, ...); new tasks follow it.

Read [FORMATS.md](FORMATS.md) before writing any file in a task folder.

## 1. Open

Create `tasks/<name>/description.md` and discuss until its Goal and Plan are agreed. When the plan still has open branches, grill it (`sw-code:grill-me`). Decisions the user still has to make stay under Open questions until they answer.

Shape the plan:

- **Stages are vertical slices.** Each stage delivers something that runs and can be checked on its own, and names the stages that block it. "Stage 1: the loss runs end to end on one batch" is a slice; "stage 1: write all the data code" is not.
- **Thresholds come first.** Fix the decision thresholds of an experiment before running it.
- **Tests name their break.** For each planned test, say where it goes and which wrong behavior turns it red.

Done when the user agrees the Goal and Plan and says to start.

## 2. Check the plan in a fresh context

For a task that will be delegated or staged, write `implementation.md`, then have it checked by a context that did not write it: the writer shares the plan's blind spots, and the implementer will see only the folder. Spawn one read-only subagent (`opus`) with this brief:

> Read `description.md` and `implementation.md` in `<task folder>`, and the code they touch. Report: (a) places where the plan contradicts itself or the goal; (b) steps the current code cannot support as written, citing `file:line`; (c) goals or acceptance checks that no step covers; (d) anything you would need to ask before implementing. Quote the plan line for each finding. Under 300 words.

Fix each finding that has one clear fix in the plan. Take the rest to the user. Done when no finding is open.

## 3. Work

- Code, tests, and outputs go where the project's CLAUDE.md says.
- Update the status line at every milestone, and record every decision in the task folder where it was made (see FORMATS.md).
- Delegate agreed implementation to `sw-code:implementer` (see _The folder is the brief_).
- **Tests.** Expected values come from an independent source (a known-good literal, a worked example, the spec), never recomputed the way the code computes them. Assert behavior, not a constant's value or a message's wording. Before the work counts as done, break the changed code a few times on purpose (a wrong constant or argument, the wrong branch, an empty return, a missing check for empty or bad input), run the tests, and undo each break. Each break must turn a test red; one that stays green marks a missing test or dead code.

Done when every step of the plan is implemented and its acceptance checks pass.

## 4. Review

Review the change in the main session, following [REVIEW.md](REVIEW.md). Done when every finding has an outcome line.

## 5. Close

Write `summary.md`, set the status to `done <date>`, update the project's docs where the change affects them, and move `tasks/<name>/` to `tasks/done/<name>/`. A follow-up is a new task whose **Trigger** points at this task's summary.

## The folder is the brief

A subagent starts with a fresh context. It sees CLAUDE.md, a git status snapshot, and the prompt, not this conversation.

Give the implementer the task folder path, and require it to read `description.md` and `implementation.md` before touching code. Anything it must know goes into the task folder first, not into the prompt: needing to explain the task in the prompt means the plan is incomplete, so fix the plan. Every delegation prompt states the files in scope, the files not to touch, and the acceptance check.

The implementer reports the files changed, what it did, what it had to decide, and what it could not finish. The main session reviews the work, then writes `summary.md` and the status line itself.

## Cross-references

- Link with relative paths, so links survive the move to `done/` (`../done/<task-name>/summary.md`).
- Name concrete artefacts: task folders, config constants, test scripts, doc pages.
- Reference material (PDF, CSV lookups) lives in the task's `docs/`, and one-off scripts in its `scripts/`.
