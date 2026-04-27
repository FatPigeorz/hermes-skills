---
name: discord-mention-routing
description: Coordinate Hermes agents on Discord when there is no direct agent-to-agent transport, using explicit mentions, safe response routing, and free-response boundaries.
version: 0.1.0
author: FatPigeorz + Hermes
license: MIT
metadata:
  hermes:
    tags: [discord, gateway, mentions, routing, free-response, multi-agent]
    related_skills: [discord-agent-identity]
---

# Discord Mention Routing

Use this skill when agents need to coordinate in Discord channels but do not have a reliable direct agent-to-agent communication channel.

## Core idea

Discord is the shared coordination surface. Agents should not assume they can silently communicate with each other. They should use explicit mentions and concise handoffs so the intended peer agent, user, and channel context are clear.

## Free-response is the most important landing decision

For Discord landing skills, the key configuration question is:

> Where may this agent respond without being explicitly mentioned?

Default policy:

- During self-configuration, the channel where the user asks the agent to configure itself is that agent's free-response channel.
- Outside that configured free-response channel, new agents are **mention-first** unless explicitly configured otherwise.
- The agent should allow mention handling and receive messages from all server members when the Discord/gateway configuration supports it.
- Peer-agent coordination should use explicit mentions even in free-response channels.
- Avoid bot pile-ons: if another agent is clearly addressed, do not answer unless asked to review, execute, or coordinate.

## Handoff format

Use this structure for agent-to-agent work:

```text
<@target_agent_id>
Goal:
Context:
Needed from you:
Output format:
```

Example:

```text
<@1497931025500733490>
Goal: Check the WSL SSH configuration.
Context: User wants LAN SSH access from another computer.
Needed from you: Inspect sshd status, IP address, and firewall assumptions.
Output format: Short findings + exact commands.
```

## Routing rules

1. **If a human explicitly mentions this agent**
   - Respond normally.
   - If another agent is also mentioned, clarify or coordinate based on the user's instruction.

2. **If another agent mentions this agent**
   - Respond only to the requested handoff.
   - Keep output concise and structured.
   - Avoid taking over the whole user task unless asked.

3. **If a message mentions another agent, not this one**
   - Do not respond unless the channel is configured for free response and the message clearly asks for general input.

4. **If the channel is free-response for this agent**
   - Answer user questions directly.
   - Still avoid interrupting an active agent-to-agent handoff.
   - Prefer short summaries when multiple agents are active.

5. **If the channel is mention-first**
   - Only respond when directly mentioned, replied to, or operating inside an approved active thread/session.

## Discord limitations

- Display names can change; use IDs/mentions.
- Bot messages may be filtered or ignored depending on gateway settings.
- Some setups do not allow bots to trigger each other naturally.
- Threads can help contain multi-step work, but should not be created unnecessarily.

## Configuration checklist

For each agent, document:

```text
Agent mention:
Free-response channels:
Mention-first channels:
Allowed bot-to-bot behavior:
Home channel:
Thread policy:
Escalation owner:
```

## Verification

After changing Discord routing/free-response behavior:

1. Send a direct mention and verify the agent responds.
2. Mention another agent and verify this agent stays quiet.
3. Test an approved free-response channel, if configured.
4. Test a peer-agent handoff using the standard format.
5. Confirm memory records only stable routing facts, not temporary test results.
