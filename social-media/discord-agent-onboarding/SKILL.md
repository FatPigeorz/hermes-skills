---
name: discord-agent-onboarding
description: Onboard a new agent into a Discord-based team by configuring its identity, reply policy, free-response boundaries, and fallback chat-based coordination rules.
version: 1.0.0
author: Hermes Agent
license: MIT
metadata:
  hermes:
    tags: [discord, agent, onboarding, identity, configuration, free-response, mentions, multi-agent]
    related_skills: [agent-self-configuration, discord-free-response-config, discord-peer-coordination]
---

# Discord Agent Onboarding

Use this skill when a user wants a **new or newly joined agent** to integrate cleanly into an existing Discord team.

This is the canonical landing/onboarding skill for this workspace.

## What this skill is for

This skill focuses on the parts of onboarding that matter most here:
1. configure the agent's **own identity and profile correctly**
2. configure **free-response vs mention-gated behavior** correctly
3. define **cross-bot coordination rules** when direct agent-to-agent integration is not available

This skill is not primarily about teaching a large skill catalog or creating a new admin workflow. It is about making sure a Discord agent joins the team safely and behaves correctly.

## When to use

Trigger this skill when the user asks things like:
- "init this bot"
- "help this new agent join the server/team"
- "set up this agent's Discord behavior"
- "make this bot only talk in the right channels"
- "align this bot with our team conventions"
- "onboard this agent"

## Core principle

A new agent is not fully onboarded until all of these are clear:
- who the agent is
- which profile/config it is using
- where it may free-respond
- where mention is required
- how it should coordinate with peer bots and the user

In this workspace, **free-response configuration is the highest-priority landing concern**.

## Primary dependencies

Use these supporting skills when needed:
- `agent-self-configuration` — agent identity, profile, self-vs-peer distinction
- `discord-free-response-config` — gateway/config policy for free-response vs mention gating
- `discord-peer-coordination` — fallback coordination through Discord chat when there is no deeper agent-to-agent channel

## Workflow

### 1. Verify the agent's identity and profile
Confirm:
- which bot account this is
- which Hermes profile is active
- whether the profile is distinct from other agents
- whether the agent has its own Discord bot identity if it is meant to be a separate server member

Check:
- profile-specific config
- `.env`
- `config.yaml`
- persona / SOUL if relevant

Do not infer identity from display names alone.

### 2. Configure reply policy first
Before broader coordination, make sure the new agent's Discord reply behavior is correct.

Default target state:
- `discord.require_mention: true`
- `free_response_channels`: only explicitly designated channel(s)
- `allowed_channels`: empty unless the user explicitly wants a hard whitelist
- DM behavior preserved

Always inspect both:
- `config.yaml`
- `.env`

Reason:
- env overrides can silently defeat the intended reply policy
- most onboarding mistakes in this workspace are really gateway/config mistakes

### 3. Define self vs peers
Build a clear model of:
- self bot account
- peer bot accounts
- user(s) relevant to the workflow
- allowed collaboration surfaces

Default rule:
- treat peer bots as separate operational identities
- assume separate machines/filesystems unless explicitly stated otherwise

### 4. Define the landing surface
Choose the collaboration surface for the new agent:
- preferred shared channel
- whether it has any designated free-response channel(s)
- whether other channels are mention-only
- whether threads should be avoided

Default here:
- avoid unnecessary threads
- use mention-based coordination outside designated free-response channels

### 5. Align role and boundaries
The agent should explicitly align on:
- intended role / specialty
- configuration boundaries
- skill scope
- reply policy
- coordination expectations with the user and other bots

If another bot must be brought into the conversation via Discord chat, use an explicit mention and a concise actionable request.

### 6. Verify behavior in practice
Validate at least these cases:
1. free-response works in the designated channel
2. the agent stays quiet in non-free-response channels unless mentioned
3. explicit `@mention` works in non-free-response channels
4. DM behavior still works if relevant
5. peer-bot coordination behavior matches the intended policy

## New agent server configuration checklist

When a brand-new agent joins the server, configure it in this order:

1. **Dedicated Discord identity**
   - separate Discord Application and Bot
   - dedicated `DISCORD_BOT_TOKEN`
   - do not reuse another agent's token if this is meant to be a distinct Discord member

2. **Dedicated Hermes profile**
   - separate profile
   - isolated memory/session history
   - clear persona, specialty, and collaboration rules

3. **Reply policy**
   - mention-gated by default
   - free-response only in designated channels
   - no hard whitelist unless explicitly requested

4. **Cross-bot rules**
   - peers are not self
   - assume separate machine/filesystem unless explicitly stated otherwise
   - if allowlisting is enabled, include required peer bot IDs intentionally

5. **Landing message**
   - ask for role, boundaries, skill set, and reply policy
   - keep it operational, not just social

6. **Verification**
   - confirm the profile launches
   - confirm Discord identity is correct
   - confirm reply policy behaves as intended

## Pitfalls

- confusing self with a peer bot
- relying on display names alone
- forgetting that `.env` can override `config.yaml`
- using `allowed_channels` when the real intent is mention-gated behavior outside one channel
- assuming shared filesystem/machine across bots
- treating onboarding like a greeting instead of a behavior/config setup process

## Good defaults

- inspect config before coordinating
- make reply policy correct before refining collaboration behavior
- keep free-response narrowly scoped
- prefer mention-based coordination outside designated channels
- keep handoffs short and explicit

## Success checklist

- identity verified
- profile verified
- free-response configuration verified
- mention-gated behavior verified
- peer-bot boundaries clear
- collaboration channel chosen
- onboarding message/action completed
