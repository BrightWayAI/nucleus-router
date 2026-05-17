# Changelog

All notable changes to this plugin will be documented in this file.

## [0.1.0] — 2026-05-16

### Added
- Initial release. Always-on `route` skill that translates natural-language utterances into Nucleus slash commands, with suggest-and-confirm flow (never auto-dispatches).
- Explicit `/route` command — prints the cheat-sheet of every Nucleus capability grouped by domain, and accepts an inline argument for one-shot routing.
- Intent table covering 57 commands across the 13 plugins shipped in the Nucleus catalog (claude-cortex, daily-brief, lead-engine, bizdev-outreach, weekly-outreach, referral-engine, news-curator, client-status, project-setup, time-tracking, weekly-alignment, writing-style, core-ops).
- Installed-only filtering: rows for plugins not present in the user's environment are skipped during matching.
- Designed to coexist with cortex's auto-firing `recall` skill: router loads the capability table, cortex loads memory content.
