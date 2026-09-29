---
description: Ask what you actually want, in rounds of options, until nothing is assumed
agent: plan
---

$ARGUMENTS is the want. It may be empty. It is a hint, not the answer: the first round tests it rather than confirming it.

## Round

The frontier is every decision whose prerequisites are settled: the questions askable now, without guessing at an answer not yet heard. Ask the whole frontier in one `question` call, each question with two to four options and the recommended one first, labelled `(Recommended)`. Then stop and wait. One round per turn.

Recompute after each answer: settled decisions push the frontier outward and unblock what depended on them. A question whose answer depends on one still open this round belongs to a later round, not this one.

## Facts are yours

A frontier question that needs a fact from the environment (does this endpoint exist, who calls this, what does this config key do) gets a sub-agent or a grep, never a question to the user. Do not block on it: a running exploration is an unsettled prerequisite, so only the questions downstream of it wait, ask the rest of the frontier now.

## Settle

The session ends when the frontier is empty: every branch visited, nothing silently assumed. Output the settled list, ask for the go, stop.

## Never

- implement, or edit a file, during a round
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

Boundaries: reads and asks, changes nothing, writes no code. The user already made the "does this need to exist" call by asking; do not argue it. Planning the implementation is ordinary work in the session after the go. Cutting what already exists is /lazy-audit, polishing what stays is /lazy-polish, proving it is /lazy-test.
