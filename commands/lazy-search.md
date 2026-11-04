---
description: Locate the code a topic touches, file:line only
agent: plan
---

$ARGUMENTS is the topic to locate. Non-empty: search for it. Empty: ask for it first, then search.

## Missing topic

No argument. Ask for the topic first: `question` call, one question, no options needed yet, just what they want located. That question does not count as a search round. Once the topic is heard, search below.

## Search

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
- Never ask a question once the topic is known. Stop after the list with one closing line: `want decisions on it? run /lazy-ask.`

## Never

- implement, or edit a file, during search
- ask about an already located hit
- guess a path that does not exist

## Output

The hit list only, then the closing line. Boundaries: search reads and stops, changes nothing and writes no code.
