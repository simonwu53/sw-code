---
name: diagnosing-bugs
description: Diagnosis loop for hard bugs and performance regressions. Use when the user says "diagnose"/"debug this", or reports something broken/throwing/failing/slow.
license: MIT
metadata:
  source: https://github.com/svyatov/supermatt/tree/main/skills/diagnosing-bugs
---

# Diagnosing Bugs

A discipline for hard bugs. Skip phases only when explicitly justified.

When exploring the codebase, read the project's glossary and decision records (`glossary.md` and `dev/decisions/` in its docs folder) if they exist, to get a clear mental model of the relevant modules.

**Start from prior attempts.** If the user says they have been trying, ask what they tried before you start, so you skip their dead ends. If they point to an earlier task or analysis note, read it first: it often carries newer repro steps or a different suspect. Check what changed recently in the files you read (`git log --oneline -10 -- <file>`), unless the project's CLAUDE.md rules out git commands.

## Redact

This skill has you show commands, outputs and captured artifacts. **Redact every secret first**: write `<REDACTED>` in its place. Build loops against env vars, so the credential stays in the environment rather than in what you show. Captured artifacts carry auth headers: quote only the lines that carry the signal.

If the redacted output is not enough to diagnose the bug, say so and ask the user.

## Phase 1: Build a feedback loop

**This is the skill.** Everything else is mechanical. If you have a **tight** pass/fail signal for the bug (one that goes red on _this_ bug), you will find the cause; bisection, hypothesis-testing, and instrumentation all just consume it. If you don't have one, no amount of staring at code will save you.

Spend disproportionate effort here. **Be aggressive. Be creative. Refuse to give up.**

First write down the **trigger case**: the exact input, data, config, and steps under which the bug shows, and the symptom it produces. Then turn it into a **reproduction**: one command that runs the trigger case and checks the symptom. A trigger case describes the bug; a reproduction tests every hypothesis against it and later proves the fix.

Example: the loss goes NaN at epoch 30 of a 10-hour run. Rerunning 10 hours is not a reproduction. Loading the epoch-29 checkpoint and running 50 steps with the same seed, asserting a NaN appears, is one, if it shows the NaN in two minutes.

Building and running the reproduction is part of the diagnosis the user asked for. A run longer than a few minutes follows the CLAUDE.md rule for long jobs. A throwaway reproduction lives in the task folder's `scripts/` (or the scratch directory when there is no task); one that becomes a test goes where the project keeps tests.

### Ways to construct one, in roughly this order

1. **Failing test** at whatever level reaches the bug: unit, integration, end-to-end.
2. **Script with a fixture input** (`uv run ...`), checking the output against a known-good value or snapshot.
3. **Resume from a saved state**: a checkpoint, a cached intermediate file, a saved batch, so the run starts just before the failure.
4. **Replay a captured input.** Save the real input that failed (a data row, a request, an OD pair, an event log) to disk and replay it through the code path in isolation.
5. **Throwaway harness.** Call the failing function directly with a minimal subset of the system (one module, stubbed dependencies).
6. **Property / fuzz loop.** If the bug is "sometimes wrong output", run 1000 random inputs and look for the failure mode.
7. **Bisection.** If the bug appeared between two known states (a commit, a dataset version, a dependency version, a config), automate "set up state X, check, repeat" and halve the range each time.
8. **Differential loop.** Run the same input through old vs new version (or two configs) and diff outputs.
9. **Human in the loop.** Last resort, when only the user can run it (another machine, a GUI). Write the exact steps for them to run and the exact output to paste back, so the loop stays structured.

Build the right feedback loop, and the bug is 90% fixed.

### Tighten the loop

Treat the loop as a product. Once you have _a_ loop, **tighten** it:

- Can I make it faster? (Cache setup, skip unrelated init, narrow the data, fewer steps.)
- Can I make the signal sharper? (Assert on the specific symptom, not "didn't crash".)
- Can I make it more deterministic? (Pin seeds, time, and input files; isolate the output directory.)

A 30-second flaky loop is barely better than no loop; a 2-second deterministic one is tight, a debugging superpower.

### Non-deterministic bugs

The goal is not a clean repro but a **higher reproduction rate**. Loop the trigger 100×, parallelise, add stress, narrow timing windows, inject sleeps. A 50%-flake bug is debuggable; 1% is not, so keep raising the rate until it's debuggable.

### When a reproduction is out of reach

Some bugs cannot be reproduced within minutes: they need another machine, hours of compute, or data you cannot access. Then shrink the trigger case until it fits (less data, fewer steps, a saved state), and if it still does not fit, say so explicitly: list what you tried, and use the evidence from the original failure (logs, saved outputs, stack traces) as the loop. Ask the user for (a) access to whatever reproduces it, (b) a redacted captured artifact (log, saved input, traceback), or (c) permission to add temporary instrumentation to their next run. Do **not** proceed to hypothesise on nothing but the trigger case.

### Completion criterion

Phase 1 is done when you have the trigger case written down and either:

- **a reproduction**: one command you have **already run at least once** (show the invocation and its output, redacted), that is
  - [ ] **Red-capable**: it drives the actual bug code path and asserts the **user's exact symptom**, so it can go red on this bug and green once fixed. Not "runs without erroring"; it must be able to _catch this specific bug_.
  - [ ] **Deterministic**: same verdict every run (flaky bugs: a pinned, high reproduction rate, per above).
  - [ ] **Fast**: seconds to a few minutes.
  - [ ] **Agent-runnable**: you can run it unattended; a human in the loop only as in method 9.
- **or the stated reason** no reproduction fits in minutes, plus the original-failure evidence you will test hypotheses against.

If you catch yourself reading code to build a theory before either exists, **stop: jumping straight to a hypothesis is the exact failure this skill prevents.**

## Phase 2: Reproduce + minimise

Run the loop. Watch it go red as the bug appears.

Confirm:

- [ ] The loop produces the failure mode the **user** described, not a different failure that happens to be nearby. Wrong bug = wrong fix.
- [ ] The failure is reproducible across multiple runs (or, for non-deterministic bugs, reproducible at a high enough rate to debug against).
- [ ] You have captured the exact symptom (error message, wrong output, slow timing) so later phases can verify the fix actually addresses it.

### Rule out the environment

Confirm the environment is the one you think it is: dependencies installed and current (`uv sync`), the expected Python and library versions active, required env vars set, the input files the ones you think (path, version, date in the name), no stale outputs or caches from an earlier run.

**Work in progress is a suspect.** When the user has uncommitted or half-finished changes that could reach the failing code, say so and ask whether to test without them. Do not stash, revert, or otherwise change their working tree or version control yourself.

### Minimise

Once it's red, shrink the repro to the **smallest scenario that still goes red**. Cut inputs, callers, config, data, and steps **one at a time**, re-running the loop after each cut, and keep only what's load-bearing for the failure.

Why bother: a minimal repro shrinks the hypothesis space in Phase 3 (fewer moving parts left to suspect) and becomes the clean regression test in Phase 5.

Done when **every remaining element is load-bearing**: removing any one of them makes the loop go green.

Do not proceed until you have reproduced **and** minimised.

## Phase 3: Hypothesise

First, **audit your assumptions**: list the beliefs your picture of the bug depends on (this function returns what its name says, the config loads before this runs, the caller passes a non-null value) and mark each _verified_ (you read it, ran it, or checked state) or _assumed_. Many wrong hypotheses are right ones tested against a wrong assumption.

Then match the symptom against the known **bug classes** in [BUG-CLASSES.md](BUG-CLASSES.md) (timezone, floating point, tensor shapes, coordinate order, cache staleness, and others). A match is a cheap first hypothesis.

Find a **working sibling**: similar code in the same codebase that works. List every difference between it and the broken path, however small. Each difference is a candidate hypothesis.

Generate **3-5 ranked hypotheses** before testing any of them. Single-hypothesis generation anchors on the first plausible idea.

Each hypothesis must be **falsifiable**: state the prediction it makes.

> Format: "If <X> is the cause, then <changing Y> will make the bug disappear / <changing Z> will make it worse."

If you cannot state the prediction, the hypothesis is a vibe: discard or sharpen it. A good prediction names something you have not looked at yet: another code path, another input, another observable. "`user` will be null when I log it" only restates "`user` is null", so it cannot catch a wrong hypothesis.

**Show the ranked list to the user before testing.** They often have domain knowledge that re-ranks instantly ("we just changed the data loader, check #3"), or know hypotheses they've already ruled out. Cheap checkpoint, big time saver. Don't block on it; proceed with your ranking if the user is away.

## Phase 4: Instrument

Each probe must map to a specific prediction from Phase 3. **Change one variable at a time.**

Tool preference:

1. **Debugger / REPL inspection** if the env supports it. One breakpoint beats ten logs.
2. **Targeted logs** at the boundaries that distinguish hypotheses.
3. **Stack capture** when you do not know who passes the bad value: log a stack trace (`traceback.print_stack()`) just before the failing operation. In tests, write it to stderr; the test runner can swallow it.
4. Never "log everything and grep".

**Tag every debug log** with a unique prefix, e.g. `[DEBUG-a4f2]`. Cleanup at the end becomes a single grep. Untagged logs survive; tagged logs die.

A tagged probe that prints nothing was most likely never reached. Before you change how it prints, grep the tag to confirm it sits in the code the loop runs, above the call that fails.

**Perf branch.** For performance regressions, read [PERFORMANCE.md](PERFORMANCE.md) before comparing changes. Establish a measured baseline with a timing harness or profiler, then test one causal hypothesis at a time.

**Heisenbug.** If the bug goes away when you add a log or attach a debugger, the probe changed the timing or ordering: that is evidence, and the bug is still there. Suspect races, async ordering, and unflushed I/O. Observe from outside the path instead (in-memory buffer dumped after the failure, sampling profiler). A real fix still holds with the probe put back.

### Completion criterion: a causal chain with no gaps

Phase 4 is done when you can state the chain from trigger to symptom, each step with a `file:line` and an observed value that confirms it. "Somehow X leads to Y" is a gap: go back and probe it. No complete chain, no Phase 5.

## Phase 5: Fix + regression test

Show the user the causal chain and the fix you propose, and apply it once they agree.

**Fix at the source.** Walk the causal chain back from the symptom to where the bad value or state first appears, and fix it there. A guard where the error shows leaves every other path from the source still broken.

Write the regression test **before the fix**, but only where a test can exercise the **real bug pattern** as it occurs at the call site. If the only place for a test is too shallow (a single-caller test when the bug needs multiple callers, a unit test that can't replicate the chain that triggered the bug), a regression test there gives false confidence. **If no such place exists, that itself is a finding**: the code's structure prevents locking the bug down. Report it to the user in Phase 6.

Where such a place exists:

1. Turn the minimised repro into a failing test there, and watch it fail on the bug.
2. Apply the fix, and only the fix. A refactor bundled in hides which change turned the loop green.
3. Re-run the Phase 1 loop against the original (un-minimised) scenario.

### When the fix fails

Revert it. State which evidence ruled the hypothesis out, then return to Phase 3 for a new hypothesis with its own prediction. A variant of the same theory ("maybe the other branch") is the same hypothesis.

After **2-3 dead hypotheses or 3 failed fixes**, the diagnosis is what is wrong. Match the pattern and show it to the user before you continue:

| Pattern | Diagnosis | Next move |
| --- | --- | --- |
| Hypotheses point at different subsystems | Design problem, not a local bug | Stop and present the findings |
| Evidence contradicts itself | Wrong mental model of the code | Re-read the path from its entry point, assuming nothing |
| Works on one machine, fails on another | Environment difference | Compare config, dependencies, data, hardware, timing |
| Fix works but the prediction was wrong | Symptom fix; the cause is still active | Keep investigating |

## Phase 6: Cleanup and record

Required before declaring done:

- [ ] Original repro no longer reproduces (re-run the Phase 1 loop)
- [ ] Regression test passes (or the missing test place is reported to the user as a finding)
- [ ] All `[DEBUG-...]` instrumentation removed (`grep` the prefix)
- [ ] Throwaway harnesses deleted, or kept in the task's `scripts/` with a note on how to run them
- [ ] The project's relevant tests pass
- [ ] The diagnosis is recorded. With a task folder, write `analysis/<issue-name>.md`: the error verbatim, the trigger case and reproduction command, the causal chain, the hypothesis that turned out correct and the ones ruled out, why earlier checks missed it, the options considered, and the chosen fix. Without one, give the same content in your final report.
