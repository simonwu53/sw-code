---
name: compliance-review
description: Check a project against the sw-code rules and the global CLAUDE.md, and record what it keeps. Use when the user asks for a compliance review, or before the first task or code change in a project whose CLAUDE.md has no Compliance section.
---

# Compliance review

The rules grew over time, and older projects follow older ones. The review compares a project with the rules a new project follows. The user decides each difference: migrate it, or keep it. The decisions are recorded in one place, the `## Compliance` section of the project's CLAUDE.md, so later sessions read them there.

The skills let existing layouts stay as they are ("existing docs keep their names and places", "task folders made before this layout keep their files"). That means nothing moves without the user's decision. It does not make an old layout match.

The review reads only. Files change after the user's go-ahead (see _4. Record_).

## 1. Gather facts

Spawn one read-only subagent (`subagent_type: Explore`, `model: sonnet`) with this brief, filled in with the project path:

> In `<project path>`, report facts only, no judgement, under 400 words:
> (a) the top-level folders and files; (b) the docs folder (`doc/`, `docs/`), its subfolders and pages, and whether it has `README.md` and `glossary.md`; (c) the `tasks/` folder: its subfolders (`backlog/`, `done/`), `findings-index.md`, and per task folder the files and folders it holds; (d) whether `CHANGELOG.md` exists at the project root, and its heading style; (e) Python setup: `pyproject.toml`, `uv.lock`, `.venv/`, `requires-python`, other dependency files; (f) what the project's CLAUDE.md says about layout, tasks, docs, git and GitButler, quoted with line numbers; (g) the number of comment lines in code files (source, scripts, tests; leave out config files and Markdown), not counting file headers and docstrings, and up to 10 examples with `file:line`; (h) every comment, docstring or commit message in the last 50 commits that cites `tasks/`, a stage ID or a decision ID, with `file:line` or the commit hash.

Several projects in one folder, each with its own CLAUDE.md: one brief, one review and one Compliance section per project.

## 2. Compare

Compare the facts with each area's source rule. Read the rule from its source:

| Area | Source |
|---|---|
| Task layout, or the opt-out | `sw-code:tasks`; global CLAUDE.md "Task System" |
| Where code, tests and outputs go | the project CLAUDE.md states it (global "Task System") |
| Docs layout, glossary, decision records | `sw-code:project-docs` |
| Changelog | `sw-code:project-docs` "Changelog" |
| Code comments, task references | global "Comments and Docstrings" |
| Python setup | global "Choosing the Language" |
| Git and GitButler | global "Git and GitButler" |

Each exception line already in the project CLAUDE.md (for example "This project has no `adr/` folder") is a row of its own.

## 3. Report and decide

Show one table in chat:

| Rule | Status | What differs | Proposed migration | Effort |
|---|---|---|---|---|
| Docs layout | differs | pages sorted by topic, no `guide/` or `dev/` | move 6 pages into `guide/` and `dev/`, rewrite the index | medium |

Status is `matches`, `differs` or `does not apply`. Effort is small, medium or large.

The user answers each `differs` row: migrate or keep. Then list the migrations to do and the Compliance section text, and wait for the go-ahead. A small migration is done directly; a larger one becomes a task (`sw-code:tasks`).

A new project gives a short table. Its result is mostly the CLAUDE.md basics (where code, tests and outputs go) and the Compliance section.

## 4. Record

On the go-ahead, write the `## Compliance` section in the project CLAUDE.md, creating the CLAUDE.md if the project has none. Move the exception lines from elsewhere in that CLAUDE.md into the section.

```md
## Compliance

Checked against sw-code 1.2.0 on 2026-11-23. Everything not listed follows the rules.

Kept as it is:
- Docs: `doc/` keeps its own layout; decision records are not used.
- Tasks: task folders made before 2026-11-23 keep `findings.md` and `docs/`.
```

- The sw-code version is in `.claude-plugin/plugin.json`, two folders above this skill's folder.
- One line per kept exception, naming the area and what stays.
- When the user puts the decisions off, write the date and `Decisions pending.` in the section, so the review does not start again in the next session.
- After a migration or a recheck, update the section: the date and version change, and a migrated item leaves the list.
