# Skill mechanics

The skill-specific branch of [`writing-for-agents`](SKILL.md): what changes when the document is a skill (frontmatter, the invocation choice, and router skills). Everything else about writing it is the universal reference in `SKILL.md`.

## Invocation

Two choices trade the two loads:

- A **model-invoked** skill keeps a model-facing `description` with the trigger branches, so the model or another skill can discover it. Omit `disable-model-invocation`.
- A **user-invoked** skill keeps a one-line human-facing `description` without trigger lists. Set `disable-model-invocation: true`; the user starts it as `/name`.

Pick model invocation only when the agent must reach the skill by itself or another skill depends on it.

## Splitting by invocation

Split off a model-invoked skill when it has a distinct trigger or another skill must load and follow it. This spends permanent context on its description, so the independent reach must justify that cost.

A sequence split hides later steps only across a real independent context, such as a subagent.

## Router skills

When user-invoked skills multiply past what a person can remember, add one user-invoked router that names them and when to use each.
