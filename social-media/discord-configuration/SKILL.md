---
name: discord-configuration
description: Central Hermes Discord gateway/profile configuration reference for reply policy, channel gating, sessions, bot permissions, env overrides, and verification.
version: 1.0.0
author: Hermes Agent
license: MIT
metadata:
  hermes:
    tags: [discord, hermes, gateway, configuration, sessions, free-response, mentions, profiles]
    related_skills: [discord-free-response-config, discord-agent-onboarding, discord-agent-identity, discord-mention-routing, discord-admin-agent-create]
---

# Discord Configuration

Use this skill whenever a task involves Hermes Discord gateway/profile configuration, especially:
- free-response vs mention-gated behavior
- channel allowlists / hard whitelists
- Discord session grouping
- bot-to-bot message handling
- profile identity/config inspection
- troubleshooting behavior that differs from `config.yaml`

This is the central configuration reference. Other Discord skills should link here instead of duplicating config rules.

## Canonical files to inspect

Always inspect both, because environment variables can override YAML:

- `~/.hermes/config.yaml`
- `~/.hermes/.env`

For profile-specific deployments, inspect the active profile/runtime equivalents rather than assuming the default home.

Useful searches:

```bash
# Use search_files/read_file tools rather than shell grep when available.
# Look for:
discord:
require_mention
free_response_channels
allowed_channels
auto_thread
allow_bots
allow_mentions
group_sessions_per_user
DISCORD_REQUIRE_MENTION
DISCORD_FREE_RESPONSE_CHANNELS
DISCORD_ALLOWED_CHANNELS
DISCORD_ALLOW_BOTS
DISCORD_ALLOWED_USERS
DISCORD_GROUP_SESSIONS_PER_USER
```

## Recommended default policy for this workspace

- DMs respond normally.
- Server channels are mention-gated by default.
- Only explicitly designated channel IDs are free-response channels.
- Do not use `allowed_channels` unless the user explicitly wants a hard channel whitelist.
- Do not create or reply in threads unless explicitly requested.
- Post as normal channel messages, not Discord replies/threaded replies.
- Use explicit Discord mention IDs for peer coordination; never rely only on display names.
- Keep `group_sessions_per_user: false` for shared channel sessions when multiple users/agents should contribute to the same channel conversation context.

## Canonical `config.yaml` pattern

```yaml
discord:
  require_mention: true
  free_response_channels:
    - 'CHANNEL_ID_1'
  allowed_channels: ''
  auto_thread: false
  allow_bots: mentions

group_sessions_per_user: false
```

Meaning:
- `discord.require_mention: true` — default server-channel behavior requires direct mention.
- `discord.free_response_channels` — channels where mention is not required.
- `discord.allowed_channels: ''` — no hard whitelist; mentioned use remains possible elsewhere.
- `discord.auto_thread: false` — avoid automatic threaded responses.
- `discord.allow_bots: mentions` — peer bots can be handled when explicitly mentioned, without encouraging bot pile-ons.
- `group_sessions_per_user: false` — a channel/group conversation shares context across users rather than isolating per-user sessions.

## Environment override pitfall

If behavior does not match YAML, suspect `.env` first.

Common problematic overrides:
- `DISCORD_REQUIRE_MENTION=false` can make the bot free-respond broadly even if `config.yaml` looks restrictive.
- `DISCORD_ALLOWED_CHANNELS` can block mention-based use outside the whitelist.
- `DISCORD_ALLOW_BOTS` can prevent expected bot-to-bot coordination.
- `DISCORD_GROUP_SESSIONS_PER_USER` or equivalent session env settings can override expected group-session behavior if present.

Preferred approach:
- keep `config.yaml` as the source of truth
- remove stale Discord env overrides unless they are intentionally profile-specific
- if an env override is required, document why

Current Hermes adapter pitfall:
- Some installed Hermes versions read `DISCORD_ALLOW_BOTS` directly in the Discord adapter and do not bridge `discord.allow_bots` from YAML into that env var. If peer-bot coordination does not work after setting `discord.allow_bots: mentions`, set `DISCORD_ALLOW_BOTS=mentions` in the active profile env file or service environment and document that override.

## `free_response_channels` vs `allowed_channels`

Use `free_response_channels` when the user means:
- “the bot may answer without mention only in these channels, but can still answer mentions elsewhere.”

Use `allowed_channels` only when the user means:
- “the bot must not respond at all outside these channels.”

`allowed_channels` is stronger than most users intend and can break normal mentioned use in other channels.

## Session grouping

`group_sessions_per_user` controls whether group/channel sessions are split per user.

- `false`: multiple users/agents in the same group/channel contribute to the same conversation session. This is usually preferred for Discord team channels.
- `true`: each user gets an isolated session in the group/channel. Useful for privacy/isolation, but it can fragment multi-agent collaboration.

When asked whether it is set, verify live config and check `.env` for overrides.

## Verification after changes

Config changes generally require a gateway restart:

```bash
hermes gateway restart
hermes gateway status
```

Then test:
1. In a free-response channel, send a normal message without mention; expected response.
2. In a non-free-response server channel, send a normal message without mention; expected silence.
3. In that same channel, direct-mention the bot; expected response.
4. In DM, send a normal message; expected response.
5. If peer-bot coordination matters, verify explicit mentions work and non-addressed bots stay quiet.

## Troubleshooting map

### Bot free-responds everywhere
Likely causes:
- `.env` sets `DISCORD_REQUIRE_MENTION=false`
- YAML has `discord.require_mention: false`
- gateway was not restarted
- another launcher/service injects Discord env vars

### Bot ignores mentions outside one channel
Likely causes:
- `discord.allowed_channels` or `DISCORD_ALLOWED_CHANNELS` is set
- bot lacks Discord permissions in that channel
- mention token was not a real `<@id>` token

### Bot responds inside old threads without mention
Likely causes:
- bot is already participating in that thread/session
- thread/session routing differs from fresh top-level channel routing

### Group conversation context is fragmented by user
Likely causes:
- `group_sessions_per_user: true`
- an env/profile override changes session grouping
- different profiles/runtimes are being compared

## Output checklist for configuration reports

```text
Config files inspected:
Env overrides found:
require_mention:
free_response_channels:
allowed_channels:
auto_thread:
allow_bots / allowed users:
group_sessions_per_user:
Gateway restarted: yes/no/not needed
Verification performed:
Open risks:
```
