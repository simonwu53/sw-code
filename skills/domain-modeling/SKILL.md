---
name: domain-modeling
description: Build and sharpen a project's domain model, its glossary and its project-level decisions (ADRs). Use when discussing project terminology, resolving a vague or overloaded word, stress-testing how domain concepts relate, writing or editing a GLOSSARY.md, or recording or editing an ADR.
license: MIT
metadata:
  source: https://github.com/svyatov/supermatt/tree/main/skills/domain-modeling
---

# Domain Modeling

Actively build and sharpen the project's domain model as you design. This is the *active* discipline: challenging terms, inventing edge-case scenarios, and writing the glossary and decisions down the moment they crystallise. (Merely *reading* `GLOSSARY.md` for vocabulary is not this skill: that's a one-line habit any skill can do. This skill is for when you're changing the model, not just consuming it.)

## Where each record goes

| Record | What belongs there | Where |
|---|---|---|
| Glossary entry | A term the project uses beyond a single task | `GLOSSARY.md` in the project's docs folder |
| ADR | A project-wide decision that passes the three tests under _Offer ADRs sparingly_ | `adr/` in the project's docs folder |
| Task decision | A decision that only governs the current task | The task folder, as the `sw-code:tasks` skill describes |

The docs folder is the one the project already uses (`docs/`, `doc/`, ...). A location named in the project's CLAUDE.md wins. In a folder that holds several projects, each with its own CLAUDE.md, every project keeps its own glossary and ADRs.

When a record could go in more than one place (a term or decision that may outlive the task, or a project with no docs folder), ask the user where it goes before writing it.

Create files lazily: the glossary only when the first term is resolved, and the ADR folder only when the first ADR is needed. Preserve existing file names; do not rename or migrate files.

## During the session

### Challenge against the glossary

When the user uses a term that conflicts with the existing language in `GLOSSARY.md`, call it out immediately. "Your glossary defines 'cancellation' as X, but you seem to mean Y. Which is it?"

### Sharpen fuzzy language

When the user uses vague or overloaded terms, propose a precise canonical term. "You're saying 'account': do you mean the Customer or the User? Those are different things."

### Discuss concrete scenarios

When domain relationships are being discussed, stress-test them with specific scenarios. Invent scenarios that probe edge cases and force the user to be precise about the boundaries between concepts.

### Cross-reference with code

When the user states how something works, check whether the code agrees. If you find a contradiction, surface it: "Your code cancels entire Orders, but you just said partial cancellation is possible. Which is right?"

### Update GLOSSARY.md inline

When a term is resolved, write it down right then, in the format in [GLOSSARY-FORMAT.md](GLOSSARY-FORMAT.md). Don't batch entries up. Write directly when the user started this through `grill-with-docs` or asked for glossary work; otherwise show the entry and write it once the user agrees.

`GLOSSARY.md` should be totally devoid of implementation details. Do not treat `GLOSSARY.md` as a spec, a scratch pad, or a repository for implementation decisions. It is a glossary and nothing else.

### Offer ADRs sparingly

Only offer to create an ADR when all three are true:

1. **Hard to reverse**: the cost of changing your mind later is meaningful
2. **Surprising without context**: a future reader will wonder "why did they do it this way?"
3. **The result of a real trade-off**: there were genuine alternatives and you picked one for specific reasons

If any of the three is missing, skip the ADR; a decision that only governs the current task goes in the task folder instead. Use the format in [ADR-FORMAT.md](ADR-FORMAT.md).
