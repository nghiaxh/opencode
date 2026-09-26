---
description: Harvest lazy: shortcut comments into a debt ledger
---

Every deliberate `lazy:` comment names a ceiling and an upgrade path. Collect them into one ledger so a deferral can't quietly become permanent.

## Scan

Search the repo for comment markers with the `grep` tool, not a shell `grep` (Windows has no `grep` binary). Pattern `(#|//|<!--)\s*lazy:` and skip `node_modules`, `.git`, and build output. If the stack uses other comment prefixes, add them to the pattern.

The comment prefix keeps prose that merely mentions the convention out of the ledger.

## Output

One row per marker, grouped by file:

`<file>:<line>, <what was simplified>. ceiling: <the limit named>. upgrade: <the trigger to revisit>.`

The convention is `lazy: <ceiling>, <upgrade path>`, so pull the ceiling and the trigger straight from the comment. Want an owner per row too? add `git blame -L<line>,<line>`.

Flag the rot risk: any `lazy:` comment that names no upgrade path or trigger gets a `no-trigger` tag, those are the ones that silently rot.

End with `<N> markers, <M> with no trigger.` Nothing found: 'No lazy: debt. Clean ledger.'

## Boundaries

Reads and reports only, changes nothing. To persist it, ask and it writes the ledger to a file (e.g. `LAZY-DEBT.md`).