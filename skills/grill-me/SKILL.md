---
name: grill-me
description: Grill the user about a plan, decision, or idea, one round of questions at a time, until every decision that changes what gets built is settled. Use when the user says "grill me", wants to stress-test their thinking, or asks to poke holes in, challenge, or be interviewed about a plan or design.
license: MIT
metadata:
  source: https://github.com/svyatov/supermatt/tree/main/skills/grilling
---

Interview the user relentlessly until you reach a shared understanding. Map this as a **design tree**: every decision branches into the decisions that hang off it.

Work the tree in **rounds**. The **frontier** is every decision whose prerequisites are already settled: the questions you can ask _now_ without guessing at answers you haven't heard yet. Ask at most four frontier questions in one round, starting with the ones whose answers remove the most other questions. A few questions discussed in depth converge faster than many at once; the rest of the frontier waits for the next round. Before sending, test each pair: when any option of one question would answer or reshape another, the second moves to the next round. Then wait for the user's answers.

Every choice question includes the smallest option: do nothing, reuse what exists, or defer. Recommend it unless you can name a concrete case the user will hit where it fails. When you recommend more, name what it adds (a field, a state, a command, a file, a concept) and what breaks without it.

## Asking questions

Ask in the text of your reply, in this format, so the user can answer each question at length. A question dialog or the next-step box cuts the answer short, so questions never go there.

```
❓ **Q1** - **<question title>**: <question body, might be multiple paragraphs, including multiple choices>

➡️ <your recommended answer and the deciding reason>

---

❓ **Q2** - **<question title>**: <question body>

➡️ <your recommended answer and the deciding reason>
```

Number questions continuously across rounds (a second round starts at Q5 after Q1–Q4), so an answer like "Q2: yes" never refers to two questions.

Each round the user answers reshapes the tree: settled decisions push the frontier outward and unblock questions that depended on them. An answer that raises a doubt or a counter-question settles nothing yet: discuss it, and keep that decision on the frontier. Recompute the frontier and ask the next round.

## Facts versus decisions

Finding _facts_ is your job, never the user's. When a frontier question needs a fact from the environment (files, code, data, docs, tools), look it up, and delegate a broad search to a subagent as CLAUDE.md describes. Anything you could look up yourself is never a question for the user. An unfinished lookup blocks only the questions downstream of it: ask the rest of the frontier now. A question's premise is a fact too: when sources disagree on it or the user disputes it, settle it with the most direct evidence you can get, such as running the tool in a scratch directory, before the decision that rests on it goes back to the user.

The _decisions_ are the user's: put each to them and wait.

## Done

Ask only questions whose answer changes what gets built or what the user sees. A detail the implementer can settle alone is not a question, so it never joins the tree. The session is done when the frontier is empty: every decision that changes what gets built is settled. Then list the settled decisions in one place and ask the user to confirm that this is the shared understanding. Do not act on it until they confirm.
