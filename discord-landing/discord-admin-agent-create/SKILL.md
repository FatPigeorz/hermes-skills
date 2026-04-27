---
name: discord-admin-agent-create
description: Admin-agent workflow for creating or onboarding a new Hermes Discord agent/profile, including identity, credentials, gateway boundaries, and handoff to Discord landing skills.
version: 0.1.0
author: FatPigeorz + Hermes
license: MIT
metadata:
  hermes:
    tags: [discord, gateway, admin, onboarding, profile]
    related_skills: [discord-agent-identity, discord-mention-routing]
---

# Discord Admin Agent Create

Use this skill only for an admin/operator agent that is responsible for creating or onboarding new Hermes agents in a Discord server.

## Scope

This skill is for admin-level setup such as:

- Creating a new Hermes profile or runtime home.
- Connecting a dedicated Discord bot token.
- Assigning a clear Discord identity and role.
- Configuring free-response or mention-first behavior.
- Handing the new agent off to the Discord landing skills.

Do **not** use this skill for a regular agent merely learning its own identity; use `discord-agent-identity` instead.

## Preconditions

Before creating/onboarding an agent, the admin needs:

1. Desired agent name and role.
2. Discord bot account/token or confirmation that the bot already exists.
3. Target server/guild and channel policy.
4. Model/provider plan and credentials.
5. Whether the new agent may free-respond anywhere.
6. Whether it needs local filesystem/terminal access.

Never invent credentials. If a token/API key is missing, report the exact missing credential.

## Creation checklist

1. Create or select the Hermes profile/runtime for the new agent.
2. Configure model/provider.
3. Configure gateway Discord token and allowed server/channel settings.
4. Record exact Discord bot account ID and mention.
5. Apply the server's default policy:
   - the initialization channel is the new agent's free-response channel
   - mention-first outside configured free-response channels
   - mention handling and receiving messages from all server members enabled when supported
   - explicit mentions for peer-agent handoffs
   - no identity inference from display names
6. Load/apply:
   - `discord-agent-identity`
   - `discord-mention-routing`
7. Verify the agent responds to direct mention.
8. Verify it does not respond when another agent is addressed.
9. Document the final configuration.

## Output format

```text
Created/onboarded agent:
Profile/runtime path:
Discord ID / mention:
Role:
Model/provider:
Free-response channels:
Mention-first channels:
Credentials still needed:
Verification performed:
Next steps:
```

## Safety notes

- Do not expose bot tokens in chat.
- Do not grant free-response globally unless explicitly requested.
- Do not add broad filesystem or terminal access unless the agent role requires it.
- Prefer explicit per-agent ownership to avoid bot overlap.
