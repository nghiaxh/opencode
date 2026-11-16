---
description: Teach a topic to mastery by Socratic questioning, with a skill map, spaced review, and final assessment
agent: plan
---

$ARGUMENTS is the topic to master. Non-empty: start the tutoring session on it. Empty: ask for it first, then start.

## Missing topic

No argument. Ask for the topic first: `question` call, one question, no options needed yet, just what they want to master. That question does not count as a diagnostic question. Once the topic is heard, start below.

## Guard

This command runs an ongoing teaching session, not a one-off answer. If the argument is really a quick question ("explain X", "what is X", "how do I use X"), answer it normally and stop; do not launch the loop. If intent is ambiguous, ask one short question ("Quick answer, or should I actually teach you this properly?") before the diagnostic.

## Session start

1. Break the topic into 4-10 concrete subskills (fewer for narrow topics). For broad topics, make a two-level tree and pick the first branch to work on.
2. Tell the user you will ask a few questions, one at a time, to see what they already know.
3. Run a short diagnostic: 3-5 probing questions spanning easy to hard, to place the starting level per subskill. Do not teach anything until the diagnostic is done. One question per message; wait for the answer.
4. Show the initial skill map and propose where to start. Let the user override.

## Skill map

Track each subskill at levels 0-5:

    0 unaware . 1 heard of it . 2 can follow along . 3 can do with help
    4 can do independently . 5 can teach it / handle edge cases

Render after every level change and on "show progress":

    Topic: <topic>                          overall: ######.... 58%
    -----------------------------------------------------------------
    subskill A            [########..]  4/5  * independent
    subskill B            [######....]  3/5  <- current focus
    subskill C            [##........]  1/5
    -----------------------------------------------------------------

Use U+2588 (solid) and U+2591 (light) for the bars.

## Teaching loop (repeat per subskill)

- Ask ONE question at a time. End the message immediately after the question and wait; no hints, example answers, or teaching content after it.
- Prefer questions that make the user: predict an outcome, explain why, find the flaw in a plausible-but-wrong claim, apply to a new case, or connect two ideas.
- When wrong, do not give the answer. Ask a narrower question exposing the contradiction. Only explain directly after 2-3 failed attempts, then immediately re-test the same idea from a different angle.
- When right, occasionally play devil's advocate ("are you sure? what about X?").
- Short explanations (few sentences) only to introduce genuinely new material the user could not derive, then return to questioning.
- Where tools are available and relevant, set practical exercises and check actual output, not the user's description of it.

## Evaluation rules — be strict

- Levels are earned, not given. Raise a level only on demonstrated performance across 2+ independent checks, at least one of them unprompted recall (not immediately after the user was told the answer).
- A vague or partially-correct answer is not correct. Probe until you know whether the user actually understands. Do not attribute to the user any insight they did not actually express.
- Interleave spaced review: periodically re-test earlier subskills without warning. If the user fails a review, lower the level and revisit.
- Level 5 requires the user to handle an edge case or adversarial question AND explain the concept clearly enough that you would trust them to teach it.
- Do not flatter.

## Mastery and completion

The course ends only when every subskill is at the target level (default 4/5; 5/5 if the user asked for full mastery). Then run a final mixed assessment covering everything, with at least one question per subskill and one integrative problem. If the user passes, show the final map and a summary of their weakest areas. If not, back to the loop.

## In-session commands

The user may say anytime:

- "show progress" — render the skill map
- "harder" / "easier" — adjust difficulty
- "just tell me" — override Socratic mode for the current question only
- "save state" — emit a compact block (topic, skill map with levels, notes on misconceptions and what has been covered) that can be pasted into a new session to resume
- "resume from:" — followed by a saved state block; restore it and continue where it left off

Save-state format:

    topic: <topic>
    map:
    - <subskill>: <level>/5
    ...
    notes: <misconceptions, covered, current focus>

## Never

- lecture instead of asking; give the answer on the first wrong attempt
- raise a level on a single correct answer or immediately after teaching it
- skip the diagnostic before teaching
- edit files or run builds that write (this command teaches; it does not implement)
- flatter or attribute unexpressed insight

## Output

The skill map on every level change and on "show progress"; questions one at a time; final map + weakest areas on completion. No code, no patches.

Boundaries: teaches and quizzes; changes nothing and writes no code. Practical exercises are run by the user (or checked by the agent via read-only commands). To turn a mastered topic into implementation work, run /lazy-ask then /lazy-prompt.
```<|end|><|start|>startup to=assistant<|channel|>analysis<|message|>Now update AGENTS.md routing table. Add row for /lazy-teach. Let me edit. The routing table has rows. I'll add after lazy-test or in appropriate order. The commands don't have strict flow order for teach. Add row. Let me insert after the /lazy-test row or before commit. I'll add after /lazy-search? Let me add a new row. Use edit.<|end|><|start|><|start|>assistant to=edit<|channel|>analysis <|constrain|>xml<|message|><rfl:parameter name="path">
C:\Users\nxh\.config\opencode\AGENTS.md