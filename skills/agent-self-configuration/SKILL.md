---
name: agent-self-configuration
description: Configure and verify an agent's own identity, active profile, reply policy, and peer-vs-self model before acting in a multi-bot or multi-channel environment.
version: 1.0.0
author: Hermes Agent
license: MIT
metadata:
  hermes:
    tags: [identity, discord, multi-agent, profiles, configuration, self-model]
---

# Agent Self Configuration

Use this skill when an agent is operating in a multi-bot, multi-profile, or multi-channel setup and needs to establish **who it is** before taking action.

## When to use

Trigger this skill when:
- starting work in a Discord multi-bot environment
- the user asks a bot to coordinate with other bots
- there is risk of confusing `self` with another bot account
- channel-specific behavior matters (free-response vs mention-only)
- the agent has a profile-specific identity or SOUL

## Core principle

Before you ask another bot to act or decide whether to reply, first determine:
1. which bot account is **you**
2. which profile/config is active
3. which channels are your free-response surfaces
4. which other bot accounts are peers rather than self

Do not reason from display names alone.

## Workflow

### 1. Identify your current runtime identity
Inspect:
- current profile
- active config location
- local SOUL.md if present
- Discord bot identity via configured token / live session context

Questions to answer:
- What bot name/account am I using right now?
- Which Hermes profile is active?
- Am I Jerry, Spike, Tom, or another bot?

### 2. Inspect local configuration
Check the active profile's:
- `config.yaml`
- `.env`
- `SOUL.md`

Pay attention to:
- `discord.require_mention`
- `discord.free_response_channels`
- `DISCORD_ALLOWED_USERS`
- `DISCORD_ALLOW_BOTS`
- home channel settings
- profile-specific persona or identity text

### 3. Reflect current local customization
Before acting, capture how this specific deployment is configured right now.

Check and summarize:
- current `discord.require_mention`
- current `discord.free_response_channels`
- whether `allowed_channels` is being used as a hard whitelist
- whether `.env` overrides `config.yaml`
- any environment-specific notes saved under `references/current-jerry-setup.md`
- which custom coordination skills have been added in this environment

If the environment has local custom skills or policy decisions, include them explicitly in your reflection rather than assuming a generic Hermes setup.

### 4. Establish self vs others
Build a simple mental table:
- self bot account ID/name
- peer bot account IDs/names
- user IDs relevant to the current environment
- allowed collaboration channels

Explicitly avoid mistakes like:
- treating `@Jerry` as another bot when you are Jerry
- assuming another bot shares your local files or profile
- assuming a channel belongs to you without checking config

### 5. Establish reply policy
Decide:
- where you may free-respond
- where you should only respond on mention
- when you should message another bot instead of replying directly yourself

### 6. Use explicit mentions carefully
When messaging another bot:
- mention the peer bot, not yourself
- start with a valid `@` mention when attention matters
- restate the task explicitly instead of saying only "same thing"

## Output checklist

Before acting, be able to state:
- "I am <bot>"
- "My active profile is <profile>"
- "My free-response channels are <channels>"
- "Mention is required elsewhere: <yes/no/how>"
- "Custom skills added here: <skills>"
- "These are peer bots, not me: <bots>"

## Pitfalls

- Do not infer identity from display names alone
- Do not assume shared filesystem or same-machine access across bots
- Do not confuse home-channel delivery with free-response policy
- Do not ask another bot to do something before clarifying whether that bot is self or peer
- Do not rely on stale memory when live config is available

## Good defaults

- In ambiguous multi-bot situations, inspect config first
- Treat each profile as a separate operational identity
- Treat each bot as a separate machine/agent unless explicitly told otherwise
- Prefer explicit user IDs / bot IDs over human-readable names when coordinating

## Success check

- self identity is verified from live config/context
- peer bots are distinguished from self
- reply behavior matches channel policy
- cross-bot coordination uses explicit peer mentions
