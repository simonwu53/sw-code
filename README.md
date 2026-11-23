# sw-code

A consent-first coding workflow for Claude Code: grill plans before building, run work through task folders, diagnose bugs from a reproduction, and keep agent docs lean.

## Skills

| Skill | Starts | What it does |
|---|---|---|
| `tasks` | automatically | Runs coding work through `tasks/<name>/`: agree the plan, check it in a fresh context, delegate, review, close. |
| `grill-me` | automatically, or `/sw-code:grill-me` | Interviews you about a plan, at most four questions a round, each with a recommended answer. |
| `grill-with-docs` | `/sw-code:grill-with-docs` | `grill-me` plus `project-docs`: records glossary terms and decision records as they settle. |
| `project-docs` | automatically | The layout of a project's docs folder: pages grouped by reader, the glossary, decision records, and the changelog. |
| `compliance-review` | automatically, or `/sw-code:compliance-review` | Checks a project against these rules, proposes migrations, and records what the project keeps in its CLAUDE.md. |
| `diagnosing-bugs` | automatically | Trigger case, reproduction, ranked hypotheses, causal chain, then a fix you approve. |
| `what-would-you-do` | `/sw-code:what-would-you-do` | Explains the last question asked and recommends an option. |
| `wait-what` | `/sw-code:wait-what` | Re-explains the last message in simple English with an example. |
| `writing-for-agents` | automatically | How to write CLAUDE.md files, skills, and other docs an agent reads. |

## Agent

`sw-code:implementer` executes an agreed, well-scoped task from its task folder and reports what changed.

## Not in the plugin

A plugin cannot add always-loaded instructions. These rules live in `~/.claude/CLAUDE.md`: coding goes through the task system; project docs follow `project-docs`; code carries minimal comments; changes get a changelog entry; and a project without a Global Rule Exceptions section in its CLAUDE.md gets a `compliance-review` first.

## Install

```
/plugin marketplace add simonwu53/sw-code
/plugin install sw-code@sw-code
```

Try a local checkout without installing: `claude --plugin-dir ./sw-code`.

## License

MIT, see [LICENSE](LICENSE).

## Credits

`grill-me`, `grill-with-docs`, `diagnosing-bugs`, `what-would-you-do`, `wait-what`, and `writing-for-agents` are adapted from [svyatov/supermatt](https://github.com/svyatov/supermatt) (MIT), itself forked from [mattpocock/skills](https://github.com/mattpocock/skills). Each keeps its license in its folder. `project-docs` is new; its three tests for a decision record come from supermatt's `domain-modeling` skill.
