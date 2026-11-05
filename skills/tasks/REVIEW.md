# Review

Review the change in the main session after it is implemented, whoever wrote it. Collect the change first: the implementer's list of changed files, plus `git diff` where the project's CLAUDE.md allows git commands. Read the changed files in full, not only the hunks.

## Three questions

Ask each question of the whole change. Give each finding a code (S1, P1, A1, ...), its `file:line`, what is wrong, and the fix.

- **Standards (S)**: does the change follow the coding rules in CLAUDE.md (simplicity, surgical changes, the existing style) and the project's own conventions? Separate hard violations from judgement calls. Search the lines the change adds for `tasks/` and for the stage or decision IDs this task's docs use; each hit breaks the comment rule in CLAUDE.md.
- **Plan (P)**: does it do what `description.md` and `implementation.md` ask? Every goal and acceptance check is met, and nothing outside the scope was changed.
- **Adversarial (A)**: how does it fail in real use? Build a concrete scenario: "if this happens, then that happens, which breaks this."

Tests are part of the change: a test that recomputes its expected value the way the code does, asserts a constant, or leaves a planned behavior unchecked is a finding.

## Adversarial depth

Count the changed lines, leaving out tests and generated files.

- **Quick**: under 50 lines and no risk signal. Assumption violation only, at most 3 findings.
- **Standard**: 50 to 199 lines. Add composition failures and replay.
- **Deep**: 200 lines or more, or a change to data writes, training resume, a public file format, or a check other code relies on. Every technique.

Techniques:

1. **Assumption violation.** Name what the code assumes and break it: a key is always set, a list is never empty, a file exists when read, init runs before the first call, IDs are positive, inputs are in the expected units or coordinate order.
2. **Composition failures.** Two parts are each correct but fail together: a new return path reuses a sentinel (None, an empty list, a fallback value) so the caller reads "failed" as "no result"; an error is caught and replaced with a default; one of two parallel paths (CLI and API, train and eval) is updated and its twin is not.
3. **Replay and recovery.** Run the job again, or resume it after an interruption: work is duplicated, lost, or mixed with an earlier run's outputs.
4. **Silent pass.** A check goes green while the real thing is red: a mock hides the real behavior, a gate skips on error, an assertion cannot fail, a test runs in a different setup from the real run.

A finding with no concrete trigger is speculation: drop it.

## Outcomes

Fix findings within the task's agreed scope. Take a finding to the user as a question when its fix would change the plan, or when you judge it not worth fixing: give `file:line`, the options (O1, O2, ...) with what each costs, and your recommendation.

Report one line per finding: `<code>: fixed`, `<code>: partly fixed, <what is left and why>`, `<code>: pending, <the choice it waits on>`, or `<code>: skipped, <why>`. Say plainly when a finding was wrong.

---

The adversarial techniques are adapted from the `code-review` skill of [svyatov/supermatt](https://github.com/svyatov/supermatt) (MIT).
