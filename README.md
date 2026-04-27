# hermes-skills

Selected Hermes skills for Discord agent onboarding and team integration.

## Layout

Canonical Hermes tap layout is flat under `skills/`:

```text
skills/<skill-name>/SKILL.md
```

The categorized folders in this repo are source/organization copies; `hermes skills tap add FatPigeorz/hermes-skills` discovers the flat `skills/` tree.

## Included skills

### discord-landing
- `discord-agent-identity` — configure an agent's exact Discord bot identity, role, boundaries, and server integration
- `discord-mention-routing` — coordinate agents through explicit mentions, safe routing, and free-response boundaries
- `discord-admin-agent-create` — admin workflow for creating or onboarding a new Hermes Discord agent/profile

### social-media
- `discord-agent-onboarding` — canonical onboarding flow for a new Discord agent
- `discord-free-response-config` — configure mention-gated vs free-response behavior correctly

### autonomous-ai-agents
- `agent-self-configuration` — verify agent identity, profile, and reply policy before acting

### productivity
- `discord-peer-coordination` — coordinate with another bot/person via explicit Discord mentions when direct agent-to-agent integration is unavailable
