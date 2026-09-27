---
description: Hunt the whole repo for over-engineering, then cut the ones you approve
agent: build
---

Make the code smaller, not different. Same behavior, fewer lines.

## Scope

Default: the whole repo, skipping `node_modules`, `.git` and committed build output.

$ARGUMENTS, when given, narrows it:

- a path: that file or directory
- a class (delete, stdlib, native, yagni, shrink): only that kind of cut
- `apply <n>`: make the cut list's nth cut, from the list this command already printed
- anything else: read it as the instruction, keep the default

## Phase 1, hunt. Read only.

Per candidate, stop at the first rung that holds: needs to exist, already in this codebase, stdlib, platform, installed dep, one line. That rung is the cut's label.

Behavior must not change. A candidate that only gets shorter by changing behavior is a fix, not a simplification: leave it out, report it under `not a cut`.

## Phase 2, apply. Only after the user says yes.

Cut the approved items, in the order listed. Do not cut anything that was not approved, and do not cut the same thing twice.

## Never

- delete a test to make the code smaller
- touch trust-boundary validation, data-loss handling, security, accessibility
- rename, move, or reformat a line you are not cutting
- rewrite a whole file when a few lines go
- explain in a comment what the smaller code already says

## Prove it

Find the check first: README, then package.json scripts, Makefile, justfile, pyproject.toml, Cargo.toml, pom.xml, build.gradle. Never invent one.

Run it after each applied cut, or at least after each file when the check is slow, and once at the end. No check, or cannot run: name the missing check. Never report a check as passing unless it ran.

Cut broke the check: fix the cut or revert it. Do not bend the check to fit the cut unless the check was wrong about behavior, and then say why.

## Output

Phase 1, one line per cut, numbered, biggest cut first: `<n>. <file>:<line>, <rung>: <what goes>. <what replaces it>. ~<X> lines.`

Then `<N> cuts, ~<X> lines. Apply which?` and stop there.

Phase 2, the same one line per cut prefixed done or reverted, then `<M> applied, ~<X> lines, check: <command> <result>.` Nothing to cut: 'Lean already. Ship.'

Boundaries: phase 1 changes nothing, phase 2 needs the user's yes. /lazy-audit inventories debt and never cuts, /lazy-review judges the result and changes nothing. A candidate that needs a new capability to get shorter is scope creep: report it, never add the capability.
