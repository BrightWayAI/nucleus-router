# nucleus-router

> The JARVIS front door for Nucleus. Speak how you think — the router translates to the right slash command and asks before running.

Nucleus ships 57 slash commands across 13 plugins. Remembering which command does what is the difference between using a CLI and talking to an assistant. This plugin closes that gap.

It does one thing: an always-loaded skill matches natural-language utterances to the right Nucleus command, suggests it, and waits for you to confirm. No auto-dispatch. No surprises.

---

## What it looks like

You:
> What's on my plate today?

Router:
> Sounds like you want to run **`/brief`** (today's working surface). Run it?

You:
> Yes

Claude runs `/brief`. Done. You didn't need to remember the command name.

---

## Install

This plugin is part of the [Nucleus marketplace](https://github.com/BrightWayAI/nucleus). Install via Cowork or Claude Code's plugin marketplace mechanism, pointed at that marketplace.

Standalone install: `gh repo clone BrightWayAI/nucleus-router` into your plugins directory.

No setup command. The router loads automatically at conversation start.

---

## Commands

| Command | Purpose |
|---|---|
| `/route` | Print the full cheat sheet of every Nucleus capability grouped by domain. |
| `/route <utterance>` | Force-route a specific utterance — useful for testing or when you want the router's opinion before phrasing your real request. |

The router also fires implicitly on any natural-language utterance that matches a Nucleus capability. You usually don't need to type `/route` at all.

---

## How it works

A single `SKILL.md` contains the intent table — one row per Nucleus command, with the natural-language patterns that should match it. At conversation start, the skill's auto-trigger loads the table into Claude's context. When you speak naturally, Claude walks the table, picks the best match, and surfaces a confirmation prompt before invoking the command.

The router never:
- Auto-dispatches commands without confirmation.
- Writes files or modifies memory.
- Calls external services.
- Logs your routing decisions.

It is a pure routing layer. All side effects come from the commands you confirm.

---

## Coexistence with cortex

Cortex's `recall` skill also auto-fires at conversation start. The two skills are complementary, not redundant:

- **`recall`** loads what Nucleus *knows* about you and your projects (memory content).
- **`route`** loads what Nucleus can *do* (the capability map).

Both run side by side.

---

## Coverage

Initial intent table covers all 57 commands across the 13 plugins in the Nucleus catalog:

| Domain | Plugins |
|---|---|
| Memory and knowledge | claude-cortex |
| Daily flow | daily-brief |
| Business development | lead-engine, bizdev-outreach, weekly-outreach, referral-engine |
| Client engagements | client-status, project-setup, time-tracking |
| Content and roundups | news-curator |
| Cross-team alignment | weekly-alignment |
| Voice and writing | writing-style |
| Ops, health, and reviews | core-ops |

Run `/route` (no argument) to see the full table.

---

## Versioning

See [CHANGELOG.md](./CHANGELOG.md). Current: **v0.1.0** — initial release.

## License

MIT — see [LICENSE](./LICENSE).

## Security

See [SECURITY.md](./SECURITY.md). The router is a thin routing layer with no data plane of its own.
