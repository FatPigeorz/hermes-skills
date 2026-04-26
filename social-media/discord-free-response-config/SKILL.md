---
name: discord-free-response-config
description: Configure Hermes Discord gateway so free-response is limited to specific channels while other channels require @mention. Covers the common env-overrides-config pitfall and when not to use allowed_channels.
version: 1.0.0
author: Hermes Agent
license: MIT
metadata:
  hermes:
    tags: [discord, hermes, gateway, configuration, free-response, mentions]
---

# Discord Free-Response Configuration

Use this skill when the user wants Hermes/Jerry to:
- free-respond only in specific Discord channels
- still answer in DMs
- still answer in other channels when explicitly @mentioned
- avoid using Discord permission hacks for what is really gateway behavior

## Core lesson

This is usually a **Hermes gateway configuration** issue, not a Discord server permission issue.

The most important pitfall:
- `~/.hermes/.env` can override `~/.hermes/config.yaml`
- if `.env` contains `DISCORD_REQUIRE_MENTION=false`, the bot may free-respond in all server channels even when `config.yaml` looks restrictive

Always inspect both files.

## Desired policy pattern

Typical desired behavior:
- free-response only in one or a few channels
- DMs always allowed
- other server channels require `@mention`
- explicit `/steer` or tool-driven sends can still happen when requested

## Correct config pattern

In `~/.hermes/config.yaml`:

```yaml
discord:
  require_mention: true
  free_response_channels:
    - 'CHANNEL_ID_1'
    - 'CHANNEL_ID_2'
  allowed_channels: ''
  auto_thread: false
```

Key meaning:
- `discord.require_mention: true` → default server-channel behavior is mention-gated
- `discord.free_response_channels` → allowlist for channels that remain mention-free
- DMs still respond regardless
- `allowed_channels` should stay empty unless the user wants a **hard channel whitelist**

## Important distinction: free_response_channels vs allowed_channels

Use `free_response_channels` when the user means:
- “Jerry should stay quiet by default in other channels, but still respond if explicitly mentioned.”

Use `allowed_channels` only when the user means:
- “Jerry should only respond in these channels at all.”

`allowed_channels` is stronger than most users intend. It can block normal mention-based use in non-whitelisted channels.

## Step-by-step workflow

### 1. Inspect current config
Check both:
- `~/.hermes/config.yaml`
- `~/.hermes/.env`

Look specifically for:
- `discord.require_mention`
- `discord.free_response_channels`
- `discord.allowed_channels`
- `DISCORD_REQUIRE_MENTION`
- `DISCORD_FREE_RESPONSE_CHANNELS`
- `DISCORD_ALLOWED_CHANNELS`

## 2. Inspect docs/code if behavior seems inconsistent
Useful confirmed behavior from Hermes:
- env vars take precedence over `config.yaml`
- `require_mention: true` makes server channels mention-gated by default
- `free_response_channels` exempts listed channels from mention requirement
- DMs still respond regardless
- auto-threaded or already-participated threads may not require mention once the bot is already in the thread

## 3. Fix the real root cause
Most common fix:
- remove `DISCORD_REQUIRE_MENTION=false` from `~/.hermes/.env`
- or set it to `true`
- set `discord.require_mention: true` in `config.yaml`
- keep only intended channel IDs under `discord.free_response_channels`

Preferred approach:
- remove the env override entirely and keep `config.yaml` as source of truth

## 4. Restart gateway
Config changes do not reliably apply until restart.

Use:
```bash
hermes gateway restart
```

Then verify with:
```bash
hermes gateway status
```

## 5. Validation checklist
Test all four cases:

1. In a free-response channel:
   - normal message without `@Jerry`
   - expected: bot responds

2. In a non-free-response server channel:
   - normal message without `@Jerry`
   - expected: bot does not respond

3. In that same non-free-response channel:
   - message with `@Jerry`
   - expected: bot responds

4. In DM:
   - normal message
   - expected: bot responds

## Troubleshooting

### Bot still free-responds everywhere
Likely causes:
- `.env` still contains `DISCORD_REQUIRE_MENTION=false`
- gateway was not restarted
- another launcher/service injects `DISCORD_REQUIRE_MENTION=false`

### Bot stops responding outside free-response channels even when mentioned
Likely cause:
- `DISCORD_ALLOWED_CHANNELS` or `discord.allowed_channels` is set
- remove it unless the user explicitly wants a hard whitelist

### Bot still responds inside some thread without mention
Possible cause:
- Hermes treats existing bot-participated threads as active conversation contexts
- validate using a fresh top-level channel message, not just an old thread

## What not to do
- Do not use Discord channel permission overwrites to model free-response behavior unless the user truly wants hard posting restrictions
- Do not confuse “cannot post” with “should not free-respond by default”
- Do not jump to `allowed_channels` when the user wants mention-gated behavior outside one channel

## Reusable diagnosis summary
If the user says:
- “Jerry should only free-respond in my channel and DM”

Then first suspect:
1. `DISCORD_REQUIRE_MENTION=false` in `.env`
2. `discord.require_mention: false` in config
3. misunderstanding between `free_response_channels` and `allowed_channels`
