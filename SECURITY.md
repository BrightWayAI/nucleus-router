# Security Policy

## What this plugin does with your data

Nucleus-router is a thin routing layer. It does not store, transmit, or transform any user data on its own. Its only job is to read a static intent table at conversation start, match the user's natural-language utterance to a Nucleus slash command, and suggest that command for the user to confirm.

**Reads:**
- Its own bundled `skills/route/SKILL.md` (the intent table). Read-only.
- The list of installed Nucleus plugins, if the host (Cowork or Claude Code) exposes it, to filter the intent table. Read-only.

**Writes:**
- Nothing. The router never writes files, never modifies memory, never touches user data.

**Invokes:**
- Other Nucleus plugins' slash commands, but only after the user confirms. The router itself does not execute the routed command — it surfaces the suggestion; the user accepts; Claude runs the command. All side effects come from the routed plugin, governed by that plugin's own security policy.

## Where data lives

- Nothing persists from this plugin. There is no router-owned config file, no router-owned memory, no logs.

## Privacy considerations

- The router operates entirely in the LLM conversation context. The intent table is shipped in the plugin itself and contains no user-specific information.
- Routed commands run under their own plugin's security model. If you accept a route to `/lead-draft`, lead-engine's privacy rules apply. The router adds no additional data flow.

## What gets sent off your machine

- Nothing beyond what's already sent by the conversation itself (LLM inference). The router does not call external services.

## Supported versions

| Version | Supported |
|---------|-----------|
| 0.1.x   | Yes       |

## Reporting a vulnerability

Report privately via GitHub Security Advisories on the [nucleus-router repo](https://github.com/BrightWayAI/nucleus-router/security/advisories).
