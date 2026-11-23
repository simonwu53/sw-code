# Project docs formats

## README.md (the index)

```md
# Documentation

{One paragraph: what the project is, and that the reader starts from their question.}

## You want to …

| You want to … | Read |
|---|---|
| run the pipeline, stop it or continue it | [guide/running.md](guide/running.md) |
| find the file for a purpose | [outputs/README.md](outputs/README.md) |
| change the code | [dev/architecture.md](dev/architecture.md) |
| look up a term | [glossary.md](glossary.md) |

## All pages

### Guide

{One line: who these pages are for.}

| Page | Content |
|---|---|
| [running.md](guide/running.md) | {What the page covers, as a list of its topics} |
```

One table and one line about the reader for each folder that exists.

## glossary.md entry

```md
### Run folder

The folder where one run writes all its files, for example `outputs/example_v2/`. See [guide/running.md](guide/running.md#run-folder).
```

The heading is the term as the project writes it. The text gives its meaning in this project, with no implementation detail. The link points to the page that explains it in full.

## dev/decisions/NNNN-slug.md

```md
# NNNN. {Short title of the decision}

**Status:** accepted YYYY-MM-DD

{One paragraph: the context, what was decided, and why. State the evidence in your own words.}
```

- Numbering: four digits, one higher than the highest existing number. The slug is kebab-case.
- Name the rejected alternatives only when a reader would suggest them again.
- Add a **Consequences** paragraph only when an effect elsewhere is not obvious.
- A replaced record's status line reads `superseded by [NNNN](NNNN-slug.md)`.

## CHANGELOG.md

```md
# Changelog

## 2026-11-23

### Added
- `--resume` continues a stopped run.

### Fixed
- Walk legs over 2 km are kept.
```

- Newest first. One heading per day, holding all that day's changes.
- Groups: Added, Changed, Fixed, Removed; only those with items.
- One line per item: one short sentence about what changed for the reader.
- A project that tags versions collects entries under `## Unreleased`, and renames it to `## [x.y.z] - YYYY-MM-DD` at release.
