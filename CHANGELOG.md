# Changelog

All notable changes to this plugin will be documented in this file.

## [0.2.1] — 2026-05-20

### Added
- Intent rows for cortex v4.10.0's `/relink-memory` command — the retroactive wikilink + graduation backfill. Routed phrases: "relink my memory", "fix my wikilinks", "my graph is disconnected", "why isn't my obsidian graph showing edges", "back-fill the wikilinks", "graduate the people in my memory".

## [0.2.0] — Chief of Staff evolution (2026-05-20)

Major rewrite. The router was a translator (utterance → command). It becomes a Chief of Staff orchestrator (verb → routed work, with parallel fan-out and narrated execution).

### Changed — primary surface is now 15 verbs + role-addressable fallback

- `skills/route/SKILL.md` rewritten. Primary structure: **verb table** (15 verbs covering ~all common operator intent). Each verb has natural-language patterns, context-aware routing rules, and parallel-yes/no annotation.
- The 15 verbs: **start day, catch me up on X, research X, draft X to Y, capture/remember X, plan X, track X, review X, status update for X, bill/invoice, what's on my plate, what's missing in my memory, clean up X, close day, close week**.
- **Role-addressable fallback** — patterns like "ask my Chief Financial Agent to bill this month" or "have my VP of Relationships prep the week" route to the named plugin directly. Each plugin's AI staff role title is a real handle.
- **Voice change** — narrates work instead of asking permission. "On it — pulling your calendar and inbox" instead of "Sounds like you want to run /brief. Run it?". Confirmation gates still preserved for `confirm`-mode commands per autonomy slider.

### Added — parallel tool use guidance

The router skill now invites parallel tool use for independent operations:
- `/listen` mining fan-out (transcript-reviewer + conversation-miner + activity-miner read disjoint surfaces)
- `/recall` cross-node reads (independent file reads)
- "research X" multi-source (memory + web)
- `/morning` pre-fetching during user-gate pauses
- `/end-day` Steps 5.5/5.6/5.7 (reindex + hot cache + log are independent)

Uses Anthropic API's native parallel tool use — no custom orchestration layer. Prompts explicitly note independence so the model issues calls in parallel.

### Added — disambiguation procedure

When a verb is invoked with ambiguous context (e.g., "research X" — person? topic? company?), the Chief asks ONE clarifying question. Never silently routes ambiguous input. If still ambiguous after one clarification, runs the most conservative interpretation and surfaces the result with a "let me know if you wanted X instead" note.

### Removed — single-command intent table as primary surface

The pre-v0.2 intent table (~60 rows mapping utterances to individual commands) is no longer the primary surface. The 60+ commands still work as direct invocations for power users typing `/X` literally.

### Why this matters

The user's stated goal: "a couple core verbs I use throughout the day deliberately; others called through context." This release implements that. The 60+ commands become plumbing. The verb is the interface.

Coordinated with cortex v4.9.0 (workstream node type + DECISION knowledge type) — the router can now route to workstream creation ("start a workstream X") and surface DECISION context in recall queries.

## [0.1.4] — 2026-05-16

### Added
- Intent rows for cortex v4.8.0+'s `/start-nucleus` foundational onboarding walker. Routed phrases include "start nucleus", "let's get started", "set me up", "onboard me", "first time setup", "let's begin", "configure everything", "I just installed Nucleus, now what", "walk me through setup".

### Removed
- Intent row for `/observe` (cortex v4.8.0 pruned the slash command). Passive observation remains always-on via the cortex `observe` skill — no command needed. Phrases like "passively observe X" / "watch for X" no longer route to a command.

## [0.1.3] — 2026-05-16

### Added
- Autonomy-mode consultation per `claude-cortex/references/autonomy.md` (v4.7.1+). Before suggesting confirmation, the router looks up the routed command's autonomy mode (user override in `<config-root>/plugins/cortex.user-context.md` → cortex defaults). For `auto` mode, the router invokes the command directly with a one-line note (no "ok to run?" prompt). For `confirm` mode, the prompt adds emphasis. For `suggest` (default), behavior unchanged.

## [0.1.2] — 2026-05-16

### Added
- Intent rows for cortex v4.7+ commands: `/listen` (nightly autonomous ingest) and `/morning` (interactive merge of overnight proposals). Routed phrases include "good morning," "start my day," "what happened overnight," "morning routine," "ingest yesterday," "mine yesterday's calendar," "process overnight," "run nightly ingest."

## [0.1.1] — 2026-05-16

### Added
- Intent rows for cortex v4.5+ commands: `/reindex`, `/research-gaps`, `/merge-research-draft`, `/setup-obsidian`. Routed phrases include "what's missing from my memory," "find gaps," "set up Obsidian," "enable graph view," "make this work on mobile," "regenerate the memory catalog."

## [0.1.0] — 2026-05-16

### Added
- Initial release. Always-on `route` skill that translates natural-language utterances into Nucleus slash commands, with suggest-and-confirm flow (never auto-dispatches).
- Explicit `/route` command — prints the cheat-sheet of every Nucleus capability grouped by domain, and accepts an inline argument for one-shot routing.
- Intent table covering 57 commands across the 13 plugins shipped in the Nucleus catalog (claude-cortex, daily-brief, lead-engine, bizdev-outreach, weekly-outreach, referral-engine, news-curator, client-status, project-setup, time-tracking, weekly-alignment, writing-style, core-ops).
- Installed-only filtering: rows for plugins not present in the user's environment are skipped during matching.
- Designed to coexist with cortex's auto-firing `recall` skill: router loads the capability table, cortex loads memory content.
