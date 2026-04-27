---
name: discord-free-response-config
description: Configure Hermes Discord free-response vs mention-gated behavior using the central Discord configuration skill.
version: 1.1.0
author: Hermes Agent
license: MIT
metadata:
  hermes:
    tags: [discord, hermes, gateway, configuration, free-response, mentions]
    related_skills: [discord-configuration]
---

# Discord Free-Response Configuration

Use this skill when the user specifically wants Hermes/Jerry/another Discord agent to:
- free-respond only in specific Discord channels
- answer DMs normally
- require `@mention` in other server channels
- avoid Discord permission hacks for what is really gateway behavior

For canonical config keys, env override pitfalls, `group_sessions_per_user`, restart steps, and verification, load and follow `discord-configuration`.

## Decision rule

Use `free_response_channels` when the user means:
- “the bot may answer without mention in these channels, but can still answer mentions elsewhere.”

Use `allowed_channels` only when the user explicitly means:
- “the bot must not respond at all outside these channels.”

Most workspace requests want `free_response_channels`, not `allowed_channels`.

## Desired policy pattern

Typical target behavior:
- `discord.require_mention: true`
- designated channel IDs under `discord.free_response_channels`
- `discord.allowed_channels: ''` unless a hard whitelist is requested
- `discord.auto_thread: false`
- DMs preserved
- normal channel messages, not Discord replies/threaded replies

## Minimal workflow

1. Load `discord-configuration`.
2. Inspect active `config.yaml` and `.env`.
3. Remove or correct env overrides that defeat the desired policy.
4. Update the active profile config.
5. Restart the gateway if config changed.
6. Verify free-response, mention-gated, and DM behavior.

## Reusable diagnosis summary

If the user says:
- “Only free-respond in my channel and DM.”

First suspect:
1. `DISCORD_REQUIRE_MENTION=false` in `.env`
2. `discord.require_mention: false` in config
3. confusion between `free_response_channels` and `allowed_channels`
4. gateway not restarted after config change
