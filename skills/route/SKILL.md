---
name: route
description: >
  Natural-language router for the Nucleus marketplace. Auto-fires at conversation
  start to load the capability map. When the user speaks naturally about something
  Nucleus can do, this skill suggests the right slash command and asks the user
  to confirm before running.

  Auto-fires in THREE scenarios:

  1. CONVERSATION START: Load the intent table into context so any later utterance
     can be routed without re-firing. Do this silently — do not print the table.

  2. EXPLICIT: User says /route, or types "what can you do", "help", "what
     commands are there", "show me capabilities", "give me a cheat sheet". In
     these cases, print the relevant section of the intent table grouped by
     domain. If the user passed an argument after /route, treat it as an utterance
     to match.

  3. IMPLICIT: User says anything that maps to a Nucleus capability — e.g.,
     "what's on my plate today", "I just met Sarah at the AI summit", "draft
     outreach to X", "wrap up the day", "block out tomorrow". Match it to the
     intent table, suggest the command, and ask the user to confirm before
     running. Never auto-dispatch.
---

# Nucleus router — JARVIS front door

This skill is always loaded. Its job is to make Nucleus feel like a conversational assistant instead of a 57-command CLI. The user speaks how they think; the router translates to the catalog.

## How to route — the 4-step procedure

1. **Match.** Walk the intent table top-to-bottom. Pick the first row whose pattern best matches the user's utterance. Semantic match, not regex.
2. **Filter by installed plugins.** If you can detect which Nucleus plugins are installed in the current environment, skip rows whose plugin is missing. If you cannot detect, proceed without filtering — the user will see a "command not found" error if the underlying plugin isn't installed, which is acceptable.
3. **Suggest + confirm.** Respond in the form: "Sounds like you want to run `/<command>` (one-line purpose). Run it?" Wait for the user to say yes / "go ahead" / "do it" / similar. Never invoke the command without explicit confirmation.
4. **Dispatch on confirm.** When confirmed, invoke the slash command exactly as written. If the command needs arguments and the user hasn't given them, ask for what's needed (e.g., "What contact?" before `/lead-brief`).

## Ambiguity rules

- **Two strong matches.** Ask one clarifying question: "I can route this to `/recall` (surface what we already know about a topic) or `/search` (full-text search the wiki). Which one?"
- **Weak match.** If nothing matches well, say: "I don't have a perfect command for that. The closest I have is `/X` (does Y). Want to try that, or rephrase what you're trying to do?"
- **Setup needed.** If the routed command's plugin appears not to be set up (e.g., `/brief` but `<config-root>/plugins/daily-brief.user-context.md` doesn't exist), suggest the setup command first: "Looks like daily-brief isn't set up yet. Run `/setup-brief` first?"
- **Multi-step intent.** If the user says "do X and then Y," route to each in order, confirming each one. Do not chain silently.

## Co-existence with cortex/recall

Cortex's `recall` skill also auto-fires at conversation start. The two skills are complementary:

- `recall` loads **memory content** (user profile, active project nodes, person pages).
- `route` loads **the capability map** (which commands exist, how to invoke them).

Both contribute to context. Order is irrelevant.

## The intent table

Each section lists rows of the form `pattern → command`. Match semantically, not literally.

### Memory and knowledge — plugin: claude-cortex

| Utterance | Command |
|---|---|
| "what do we know about X" / "remind me about X" / "catch me up on X" | `/recall` |
| "I just learned X" / "remember that…" / "make a note that…" | `/remember` |
| "save this thought" / "scratch note" / "jot this down" | `/note` |
| "I just met X" / "X is a new contact" / "add a person page for X" | `/remember` (person-page flow) |
| "search my memory for…" / "find the entry where…" / "full-text search" | `/search` |
| "forget that…" / "that's wrong, X is actually Y" / "supersede X" | `/forget` |
| "what happened on/around DATE" / "show me my timeline" | `/timeline` |
| "review my open threads" / "what's stale" / "any active loops" | `/review` |
| "drill me on what I should remember" / "quiz me on…" / "review for retention" | `/rehearse` |
| "I learned X from working on Y" / "lesson learned from the project" | `/learn` |
| "passively observe X" / "watch for X" | `/observe` |
| "wrap up the day" / "end of day" / "checkpoint today" | `/end-day` |
| "wrap up the week" / "weekly retro" / "end of week" | `/end-week` |
| "clean up my memory" / "prune stale entries" / "audit memory" | `/cleanup` |
| "regenerate the memory catalog" / "refresh the index" / "rebuild memory/index" (v4.5+) | `/reindex` |
| "what's missing from my memory" / "find gaps" / "research what we don't know" / "audit my knowledge for holes" (v4.5+) | `/research-gaps` |
| "merge the research findings" / "apply the gap-fill draft" / "walk the research draft" (v4.5+) | `/merge-research-draft` |
| "set up Obsidian" / "make this work on mobile" / "enable graph view" / "I want to use Obsidian" (v4.5+) | `/setup-obsidian` |
| "good morning" / "start my day" / "what happened overnight" / "morning routine" / "merge last night's proposals" (v4.7+) | `/morning` |
| "ingest yesterday" / "mine yesterday's calendar" / "process overnight" / "run nightly ingest" / "what happened yesterday I haven't captured" (v4.7+) | `/listen` |
| "set up cortex" / "first-time setup" / "configure identity" | `/setup-identity` |
| "set up my voice" / "teach Claude how I write" | `/setup-voice` |
| "connect note sources" / "set up Granola/Gemini/Fireflies/Otter" | `/setup-sources` |

### Daily flow — plugin: daily-brief

| Utterance | Command |
|---|---|
| "what's on my plate" / "what's today" / "build my brief" / "show me today" | `/brief` |
| "I left annotations in the brief" / "act on my brief notes" / "process the brief" | `/process-brief` |
| "block out tomorrow" / "plan tomorrow" / "what does tomorrow look like" | `/plan-tomorrow` |
| "set up the daily brief" / "configure /brief sections" | `/setup-brief` |
| "set up plan-tomorrow" / "configure tomorrow planning" | `/setup-plan` |

### Business development — plugins: lead-engine, bizdev-outreach, weekly-outreach, referral-engine

| Utterance | Command |
|---|---|
| "pre-call brief on X" / "research X before the call" / "prep me for the X meeting" | `/lead-brief` |
| "capture this lead" / "add X to the LinkedIn pipeline" | `/lead-capture` |
| "draft a connection request to X" / "warm-comment on X's post" | `/lead-connect` |
| "draft a DM to X" / "draft outreach to X" (LinkedIn / warm lead) | `/lead-draft` |
| "log that I messaged X" / "record that touchpoint" | `/lead-log` |
| "show me the LinkedIn pipeline" / "who's hot right now" / "pipeline review" | `/lead-pipeline` |
| "pull new leads from LinkedIn" / "scan for intent signals" | `/lead-pull` |
| "warm sequence to X" / "3-touch cadence to X" | `/lead-warm` |
| "set up lead-engine" / "configure my buyer persona / signals" | `/lead-setup` |
| "research X and draft outreach" (cold contact, not on LinkedIn) | `/setup` (bizdev-outreach setup, then it drives the draft) |
| "plan this week's outreach" / "weekly BD prep" / "who should I reach out to this week" | `/weekly-outreach` |
| "set up weekly outreach" | `/setup-outreach` |
| "ask X for a referral to Y" / "draft a referral ask to X" | `/referral-ask` |
| "who are my best connectors" / "show me my referral network" | `/referrals` |
| "set up referrals" / "configure referral network" | `/setup-referrals` |

### Client engagements — plugins: client-status, project-setup, time-tracking

| Utterance | Command |
|---|---|
| "weekly status update for X" / "draft client status for X" | `/client-status` |
| "set up client status" | `/setup-status` |
| "set up a new engagement" / "kick off project X" / "new client" / "new project" | `/project-setup` |
| "set up project templates" | `/setup-projects` |
| "log my time" / "what did I work on today" / "process calendar into time entries" | `/track-time` |
| "generate invoices" / "bill out this month" / "create invoices" | `/generate-invoices` |
| "set up time tracking" | `/setup-time` |

### Content and roundups — plugin: news-curator

| Utterance | Command |
|---|---|
| "draft this week's AI roundup" / "weekly LinkedIn news post" / "news roundup" | `/ai-roundup` |
| "set up news curation" | `/setup-news` |

### Cross-team alignment — plugin: weekly-alignment

| Utterance | Command |
|---|---|
| "scan Slack for alignment issues" / "what's the team talking about" / "Monday alignment brief" | (auto-fires; skill-only plugin — no slash command needed) |

### Voice and writing — plugin: writing-style

| Utterance | Command |
|---|---|
| "rewrite this in my voice" / "polish this to sound like me" / "tighten this" | `/style` |
| "I just rewrote this — learn from the edit" / "capture this voice pattern" | `/style-learn` |
| "audit my voice guide" / "review the style guide" | `/style-review` |
| "set up my writing style" | `/setup-style` |

### Ops, health, and reviews — plugin: core-ops

| Utterance | Command |
|---|---|
| "show me Nucleus status" / "what's running" / "system health" | `/nucleus-status` |
| "open the dashboard" / "full Nucleus view" | `/nucleus-dashboard` |
| "something's broken" / "diagnose X" | `/diagnose` |
| "review this deck / doc / spreadsheet" / "QA pass on this deliverable" | `/review-deliverable` |
| "log an agent run" | `/log-agent-run` |
| "agent metrics" / "how often does X fire" / "agent activity" | `/agent-metrics` |
| "register scheduled tasks" / "set up Cowork schedules" | `/register-schedules` |
| "set up core-ops" | `/setup-core` |

## Failure modes — what to do when matching fails

- **No match at all.** Respond: "I don't have a Nucleus command for that. Closest two: `/X` (does Y), `/Z` (does W). Want to try one, or describe what you're trying to do?"
- **The matched plugin isn't installed.** Respond: "That capability lives in the `<plugin>` plugin, which doesn't appear to be installed. Install it from the [Nucleus marketplace](https://github.com/BrightWayAI/nucleus) and re-run."
- **The matched plugin is installed but not set up.** Suggest the setup command first.
- **User confirms but command errors out.** Surface the error verbatim; do not try to retry silently or invent fallback paths.

## What this skill does NOT do

- Does not execute commands without explicit user confirmation.
- Does not modify any files or memory.
- Does not log routing decisions anywhere.
- Does not learn from past routings (yet — possible v0.2 feature).
- Does not replace cortex's auto-recall. Both run side by side.
- Does not handle multi-command chains in v0.1. Routes one command per turn, confirms each.
