---
name: project-docs
description: The layout and rules of a project's docs folder, its glossary and its decision records. Use when starting a project's docs, adding, moving or rewriting a doc page, writing a glossary entry, a decision record or a changelog entry, or deciding whether something belongs in the docs.
---

# Project docs

A project keeps its docs in one folder that is published with the repository, and its changelog in `CHANGELOG.md` at the project root. The docs are **reference**: they describe the project as it is now, for the person who runs it, the partners who use its results, and the developers who maintain it. Agents read them the same way. A reader finds a page by their question in the index, and then reads that page.

## Where the docs live

- Use the folder the project already has (`doc/`, `docs/`). A new project uses `docs/`. A location named in the project's CLAUDE.md wins.
- In a folder that holds several projects, each with its own CLAUDE.md, each project keeps its own docs.
- Create a folder or file when its first page is written. An empty folder is never created ahead of time.
- Existing docs keep their names and places. Reorganize them only when the user asks.

## Layout

```
docs/
  README.md        the index
  glossary.md      one entry per term
  guide/           for the person who runs or uses the project: installation, inputs, running, configuration, troubleshooting
  outputs/         for partners who use the results: one page per group of output files, or per API
  method/          for modellers and reviewers: what the system does and why (model, algorithm), and its validation
  dev/             for maintainers: architecture, internal schemas, conventions, known traps, release and publishing
    decisions/     decision records, one file per decision
```

The folders are sorted by **reader**. A page goes in the folder of the reader who needs it. A project that has no separate partners has no `outputs/`, and a project without a model or algorithm to explain has no `method/`. A folder with many pages may have its own `README.md` as its entry page.

Each file format is described once, in its reader's folder:

- input files go in `guide/`;
- output files go in `outputs/`;
- internal files and the contracts between modules go in `dev/`.

Other pages link to that description.

## What belongs in the docs

The docs hold what stays true until the project changes. The **task folder** (`sw-code:tasks`) holds the working record: plans, status, open questions, investigations and their findings. Validation of the current model is reference, so it goes in `method/`.

The docs are published, so every link points to another page in the repository or to a public URL. A decision record states its evidence in its own words.

## Writing a page

- The first paragraph names the reader and what the page covers.
- A term is explained where it first appears on a page, and linked to its glossary entry.
- A page that partners read makes sense on its own.
- A trap goes on the `dev/` page of the part it concerns.
- When you add, move or remove a page, update the index (and the folder's `README.md`, if it has one) in the same change.

Templates for the index, a glossary entry, a decision record and the changelog: [FORMATS.md](FORMATS.md).

## Glossary

`glossary.md` has one entry for each term the project uses beyond a single task, in alphabetical order. An entry gives the term's meaning in this project and links to the page that explains it in full.

Keep the project's language exact during a discussion:

- When a word conflicts with the glossary, say so: "The glossary defines a run as one execution for one config file. You seem to mean the whole batch. Which one?"
- When a word is vague or has several meanings, propose one precise term.
- When the user states how something works, check the code and report any difference.

## Decision records

A decision record answers "why is it built this way?" Write one only when all three tests hold:

1. **Hard to reverse**: changing course later costs real work.
2. **Surprising**: a newcomer would wonder why, without the context.
3. **A real trade-off**: there were alternatives, and one was picked for reasons.

A decision that governs only one task goes in that task's folder.

A record stays as written. When a decision changes, write a new record that names the one it replaces, and set the old record's status line to `superseded by NNNN`.

## Changelog

`CHANGELOG.md` sits at the project root, next to the project's CLAUDE.md, so each project in a shared repository has its own. Every project keeps one, in git or not. It is published: it lists the changes a reader can see (inputs, outputs, behaviour, fixed bugs), each as one short line in the reader's words.

An entry is written when a task closes (`sw-code:tasks`). For a change made outside a task, the report that ends the change offers the entry, and it is written on the user's yes.

## When to write

- At task close, a changelog entry is written without asking and shown in the report.
- When `grill-with-docs` runs, or when the user asked for doc, glossary or decision work, write each entry as it settles.
- Otherwise, show the entry or the page change, and write it once the user agrees.
- When you are not sure whether something belongs in the docs, or in which folder, ask.
