# Changelog

## [1.2.2] - 2026-11-24

### Fixed
- `plugin.json` has no `$schema` key, which Claude flagged as unknown.
- The `tasks` description has no angle brackets, which claude.ai stripped.

## [1.2.1] - 2026-11-23

### Changed
- `compliance-review`: the CLAUDE.md section is renamed from `Compliance` to `Global Rule Exceptions` and lists only exceptions to the global rules; long ones get a subsection, and a project with none records `None.`
- `compliance-review`: where code, tests and outputs go is recorded in its own `## Layout` section.

## [1.2.0] - 2026-11-23

### Added
- `compliance-review` skill: checks a project against the rules and records what it keeps in its CLAUDE.md.
- `project-docs`: a `CHANGELOG.md` at the project root, with its format.
- `tasks`: closing a task adds its changelog entry.

### Changed
- `tasks` review flags new inline comments other than a one-line public-issue link.
- `project-docs`: known traps go on `dev/` pages.

## [1.1.0] - 2026-11-23

### Added
- `project-docs` skill: docs grouped by reader, glossary, decision records.
- `tasks`: `tasks/findings-index.md`, one line per finding worth keeping.

### Changed
- `tasks`: the task folder's `docs/` is now `materials/`; closing a task follows a fixed order.
- `grill-with-docs`, `wait-what` and `diagnosing-bugs` use the `project-docs` layout.

### Removed
- `domain-modeling` skill, merged into `project-docs`.

## [1.0.1] - 2026-11-06

### Changed
- `tasks` review searches added lines for task references.

## [1.0.0] - 2026-11-03

### Added
- `tasks` skill: task folders with a fresh-context plan check and review.
- `grill-me`, `grill-with-docs`, `domain-modeling`, `diagnosing-bugs`, `what-would-you-do`, `wait-what` and `writing-for-agents` skills.
- `implementer` agent for delegating agreed tasks.
