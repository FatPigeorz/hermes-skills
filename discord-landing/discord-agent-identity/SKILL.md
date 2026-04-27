---
name: discord-agent-identity
description: Configure a Hermes agent's own Discord identity, role, boundaries, and server integration when joining a multi-agent Discord server.
version: 0.1.0
author: FatPigeorz + Hermes
license: MIT
metadata:
  hermes:
    tags: [discord, gateway, agent-identity, multi-agent, onboarding]
    related_skills: [discord-mention-routing]
---

# Discord Agent Identity

Use this skill when a new Hermes agent joins a Discord server, or when an existing agent needs its own Discord-facing identity and operating boundaries clarified.

## Purpose

A Discord agent must know exactly who it is before it participates in server workflows. Display names are not reliable; Discord account IDs and mentions are the source of truth.

## Required setup facts

Record these durable facts for the agent:

1. **Bot identity**
   - Discord bot account ID
   - Mention form, e.g. `<@1234567890>`
   - Bot name / nickname only as a human-friendly label

2. **Server context**
   - Guild/server ID if available
   - Home channel(s)
   - Whether the agent may free-respond in any channel
   - Whether the agent is mention-first by default

3. **Role boundary**
   - What this agent owns
   - What this agent should avoid
   - Which peer agent or human owns coordination, review, or admin decisions

4. **Memory boundary**
   - Save stable identity and configuration facts to memory
   - Do not save temporary task progress as memory
   - Use skills for reusable procedures, not memory

## Default policy for this server

For FatPigeorz's Discord multi-agent setup:

- Use explicit Discord IDs/mentions; never infer identity from display names alone.
- During self-configuration, the channel where the user asks the agent to configure itself is the agent's free-response channel.
- New agents should be **mention-first by default** outside their configured free-response channel.
- The agent should allow mention handling and receive messages from all server members when the Discord/gateway configuration supports it.
- Existing known identities:
  - Toodles: `<@1497931025500733490>`
  - Jerry: `<@1496913087482167326>`
  - Tom / Project Manager: `<@1496985074896338964>`

## Agent role defaults

- **Tom / Project Manager**: task triage, priorities, and acceptance criteria.
- **Jerry**: coordination, review, task decomposition, and cross-checking.
- **Toodles**: local WSL execution, filesystem, terminal work, automation, code changes, and verification.

## Initialization checklist

When initializing a new Discord agent:

1. Identify the exact bot account ID and mention.
2. Identify the intended role/specialty.
3. Decide whether it can free-respond anywhere.
4. Set mention-first behavior unless free-response is explicitly configured.
5. Record stable identity/team facts in memory.
6. Avoid writing task-specific progress to memory.
7. If this agent will create/manage other agents, use the admin-agent workflow instead of this landing skill.

## Output format

When reporting setup back to the user, use:

```text
Agent:
Discord ID / mention:
Role:
Free-response channels:
Mention-first channels:
Peer agents:
Memory updates:
Open questions:
```
