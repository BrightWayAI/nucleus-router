# Changelog

All notable changes to this plugin will be documented in this file.

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
