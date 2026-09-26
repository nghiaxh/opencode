---
description: "Harvest lazy: shortcut comments into a debt ledger"
---

Collect deliberate `lazy:` comments into one ledger so a deferral can't quietly become permanent.

## Scan

`grep` tool, pattern `(#|//|<!--)\s*lazy:`. gitignore already drops `node_modules` and `.git`; if the repo commits build output, narrow with `include` to source extensions. Add the stack's other comment prefixes to the pattern.

Two filters, both required:
- Markdown: drop a match inside a code fence or inline backticks. That is the convention being documented, not debt.
- The tool reports a match count and caps at 100. If the count exceeds the lines you can see, the ledger is partial: say so and name the shortfall. Never report a truncated scan as complete.

## Output

One row per marker, grouped by file:

`<file>:<line>, <what was simplified>. ceiling: <the limit named>. upgrade: <the trigger to revisit>.`

Split the comment on the first comma after `lazy:`. No comma, or nothing after it, is `no-trigger`: the marker that silently rots. Want an owner per row too? add `git blame -L<line>,<line>`.

End with `<N> markers, <M> with no trigger.` Nothing found: 'No lazy: debt. Clean ledger.'

Boundaries: reads and reports only, changes nothing. To persist it, ask and it writes the ledger to a file (e.g. `LAZY-DEBT.md`).
