---
description: Locate the code a topic touches, or ask what you actually want in rounds before any code is written
agent: plan
---

$ARGUMENTS decides the mode. Non-empty: locate mode. Empty: rounds mode.

## Locate mode

$ARGUMENTS is a topic to locate, not a want to settle. Search the repo for the code related to it and answer where it is. Nothing else.

- Dispatch one explore sub-agent, medium thoroughness. Brief: search the repo for the code serving the topic, skip `node_modules`, `.git` and build output, rank hits by how directly they serve the topic, return file:line anchors only. No fixes, no plans, no patches.
- Print one line per hit, biggest relevance first:

  ```
  <topic> lives at:
  1. <path>:<line>, <what lives there, one line>
  ...
  <N> hits.
  ```

  A hit spanning a block uses its start line. No commentary between hits, no padding.
- Nothing relevant found: say so plainly and stop. No guessing.
- Never ask a question in locate mode. Stop after the list with one closing line: `want decisions on it? run /lazy-ask with no argument.`

## Rounds mode

No argument. The want said in chat is a hint, not the answer: the first round tests it rather than confirming it.

## Round

The frontier is every decision whose prerequisites are settled: the questions askable now, without guessing at an answer not yet heard. Ask the whole frontier in one `question` call, each question with two to four options and the recommended one first, labelled `(Recommended)`. Then stop and wait. One round per turn.

Recompute after each answer: settled decisions push the frontier outward and unblock what depended on them. A question whose answer depends on one still open this round belongs to a later round, not this one.

## Facts are yours

A frontier question that needs a fact from the environment (does this endpoint exist, who calls this, what does this config key do) gets a sub-agent or a grep, never a question to the user. Do not block on it: a running exploration is an unsettled prerequisite, so only the questions downstream of it wait, ask the rest of the frontier now.

## Settle

The session ends when the frontier is empty: every branch visited, nothing silently assumed. Output the settled list, ask for the go, stop.

## Never

- implement, or edit a file, before the go
- ask what a sub-agent or the repo can answer
- re-ask what an earlier round settled
- collapse every question into one round
- invent a branch the user never raised

## Output

The final round only:

```
settled:
1. <decision>. <the answer, one line>. (<why it was open>)
n open: none.
```

Boundaries: locate mode reads and stops; rounds mode reads and asks. Both change nothing and write no code. The user already made the "does this need to exist" call by asking; do not argue it. Planning the implementation is ordinary work in the session after the go.
