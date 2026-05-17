---
description: Translate natural language to the right Nucleus command, or print the cheat sheet of every capability grouped by domain.
---

# /route — explicit Nucleus router

## What this does

The `route` skill is already loaded at conversation start and matches natural-language utterances to Nucleus commands automatically. This explicit `/route` command exists for two cases:

1. **Cheat sheet.** Running `/route` with no argument prints the full table of Nucleus capabilities, grouped by domain, filtered to whatever plugins are installed. Useful when the user wants to see what's available rather than ask.
2. **Explicit routing.** Running `/route <utterance>` (e.g., `/route what does my day look like`) forces the router to evaluate that utterance even if Claude wouldn't have routed it on its own. Useful for testing, for ambiguous phrasings, or when the user wants the router's opinion before phrasing their real request.

## Procedure

### If the user passed no argument

1. Detect which Nucleus plugins are installed. If you can read the host's installed-plugin list, use it. Otherwise, assume all 13 are installed.
2. Print the cheat sheet, organized by domain, showing only rows from installed plugins. Use the same domain groupings as `skills/route/SKILL.md`:
   - Memory and knowledge (claude-cortex)
   - Daily flow (daily-brief)
   - Business development (lead-engine, bizdev-outreach, weekly-outreach, referral-engine)
   - Client engagements (client-status, project-setup, time-tracking)
   - Content and roundups (news-curator)
   - Cross-team alignment (weekly-alignment)
   - Voice and writing (writing-style)
   - Ops, health, and reviews (core-ops)
3. After the table, print: "Say what you want in plain English — I'll route it. Or run any of these directly."

### If the user passed an argument

1. Treat the argument as a natural-language utterance.
2. Apply the matching procedure from `skills/route/SKILL.md` (steps 1-4: match, filter, suggest, dispatch on confirm).
3. Do not silently invoke the command. Always confirm.

## Examples

User: `/route`
→ Print the cheat sheet.

User: `/route what's on my plate today`
→ "Sounds like you want to run `/brief` (today's working surface). Run it?"

User: `/route I just met someone new at a conference`
→ "Sounds like you want to run `/remember` and create a person page. What's their name and the one or two things worth capturing?"

User: `/route help me think about Q3 strategy`
→ "I don't have a Nucleus command for open-ended strategy work — that's a conversation, not a workflow. The closest tools I have are `/recall` (surface what we already know about a project/topic) and `/review-deliverable` (QA pass on a doc you've drafted). Want to start with `/recall`?"

## What this command does NOT do

- Does not run the routed command without confirmation.
- Does not learn from past `/route` invocations.
- Does not modify any files. The cheat-sheet is printed inline.
