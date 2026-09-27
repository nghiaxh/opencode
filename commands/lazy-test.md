---
description: Run the check that exists, report what it proved and what it did not cover
---

Tests are proof, so hand over the proof or name its absence. Read only: this command runs checks and reads code, it writes nothing.

## Find the check

In order: README, package.json scripts, Makefile, justfile, pyproject.toml, Cargo.toml, pom.xml, build.gradle, then the stack's usual runner. Narrowest command that covers the change, not the whole suite, unless the suite is all that runs. Never invent a command.

## Run, then read

Run it, then read the real output. Report the actual assertion text, not a paraphrase.

- passed: command, count, duration
- failed: `<file>:<line>, <the assertion in your words>. <what in the change caused it>.`
- did not run: say so, and why

Cannot attribute a failure to the change or to HEAD: say `origin unverified`. Verify against HEAD only with the user's yes, never with a stash or reset you have not shown.

No runner anywhere: say `no runnable check` and name the one check that would cover this change, then stop.

## Judge the proof

Only once green, because a red suite says nothing about test quality. The proof can still be weak: asserts nothing (`expect(true)`, empty body, an unread snapshot); asserts implementation not behavior; skips the error, boundary, or permission path the diff just touched; no regression case for a fixed bug; one giant test where three cases would localize the failure.

## Output

One line per weak proof: `<file>:<line>, <the weak proof>. <the assertion that would hold>.`

End `<command> <pass|fail>, <N> run, <M> failed, <K> weak.` Then `Proven.` or `Not proven: <the missing check or the untested path>.`

Boundaries: reads and runs, changes nothing, and writes no test code. To close a gap, ask, and the write happens as ordinary work in the session. Builds no suite or framework (AGENTS.md caps it at one runnable self-check), judges no design (that is /lazy-review).
