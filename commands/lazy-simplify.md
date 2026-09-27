---
description: Refactor the whole repo for clarity, then apply only the moves you approve
agent: build
---

Make the code easier to read, change, and grow. Same behavior, almost the same amount of code.

## Scope

Default: the whole repo, skipping `node_modules`, `.git` and committed build output.

$ARGUMENTS, when given, narrows it:

- a path: that file or directory
- a class (name, flow, shape, boundary, scale, fit): only that kind of move
- `apply <n>`: make the move list's nth move, from the list this command already printed
- anything else: read it as the instruction, keep the default

## Phase 1, hunt. Read only.

Rank by blast radius first: how many places import it, how deep the nesting, how long the function. A rename in a hot file costs more readers than a tidy leaf. Then the class:

- name: a name that says what it is typed, not what it does: `data`, `result`, `temp`, `manager`, `helper`, `util`, `handle`
- flow: nesting that could be a guard clause, `else` after `return`, a side effect inside a condition, early exits hiding the one real case
- shape: one function doing two things, three functions that are one thing, a block that wants to be a named function, an invariant carried in a comment instead of a type
- boundary: parsing or validating inside the core, `any` or a bare `object` where the shape is known, an optional standing in for "not decided yet"
- scale: work inside a loop that belongs outside it, N+1 calls in a loop, a whole file read to answer one question, a cache with no invalidation story, a lock held across I/O
- fit: an idiom, placement, or convention that fights the file around it, or that the platform already answers better

A `lazy:` marker is a decision with a ceiling, not a move: hand it to /lazy-debt.

A move that changes behavior is a feature or a fix, not a refactor: leave it out, report it under `not a refactor`.

## Phase 2, apply. Only after the user says yes.

Make the approved moves in the order listed, one at a time. Never mix a rename with a behavior change in the same edit, so /lazy-review can read it.

## Never

- delete a feature, a dependency, dead code, or a duplicate: that is /lazy-audit's job
- delete anything else, except what a move just made dead, and name the move that did it
- rename across a public API, a wire format, a config key, a migration, or a stored field without flagging it as a breaking change
- touch trust-boundary validation, data-loss handling, security, or accessibility
- delete a test to make the change smaller
- rewrite a whole file when a few lines go
- explain in a comment what the code already says

## Prove it

Find the check first: README, then package.json scripts, Makefile, justfile, pyproject.toml, Cargo.toml, pom.xml, build.gradle. Never invent one.

A refactor with no check to prove behavior is unchanged is not applied: no check, no refactor. Name the check that is missing and stop.

Before a rename, grep the whole repo for the old name, including strings, config keys, migrations, and docs. A rename with a live reference is a bug.

Run the check after each move, or at least after each file when it is slow, and once at the end. Never report a check as passing unless it ran.

Move broke the check: revert the move. Do not bend the check to fit the move unless the move was wrong about behavior, and then say why.

## Output

Phase 1, one line per move, numbered, biggest blast radius first: `<n>. <file>:<line>, <class>: <what is wrong with it now>. <the shape it should have>. ~<X> lines touched.`

Then `<N> moves, ~<X> lines touched. Apply which?` and stop there.

Phase 2, the same one line per move prefixed done or reverted, then `<M> applied, check: <command> <result>.` Nothing to move: 'Nothing to move. Clean.'

Boundaries: phase 1 changes nothing, phase 2 needs the user's yes. It refactors code that stays; /lazy-audit finds what can go and never edits, /lazy-review judges the diff and never edits.
