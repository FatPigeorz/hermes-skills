---
name: discord-peer-coordination
description: Coordinate with another bot or person through explicit Discord mentions, correct channel selection, concise handoffs, and delivery verification.
version: 1.0.0
author: Hermes Agent
license: MIT
metadata:
  hermes:
    tags: [discord, messaging, coordination, bots, mentions, handoff]
---

# Discord Peer Coordination

Use this skill when the user wants you to **ask another bot or person to do something through Discord chat**.

## When to use

Trigger this skill when the user says things like:
- "mention Tom and ask him to ..."
- "ping Spike to do X"
- "ask the other bot to handle this"
- "tell Jerry to do the same thing"

## Core principle

This skill is for **chat-level coordination**, especially when there is no direct agent-to-agent integration.

Do not:
- assume the other bot saw your message unless you actually send it
- assume a display name is enough when a precise target can be resolved
- dump a vague request on the other bot when the user's intent can be phrased clearly

Instead:
1. resolve the target
2. choose the correct channel
3. send a concise, actionable message with an explicit mention
4. confirm delivery back to the user

## Workflow

### 1. Resolve the target
If the user names a specific bot/person/channel:
- use `send_message(action='list')` first when needed to identify the correct target
- prefer exact target/channel identifiers over guesswork
- if the user provides a channel mention like `<#...>`, use that channel directly when possible

If the task requires a Discord user mention rather than just sending into a channel, gather the user ID first via the appropriate discovery path before composing the message.

### 2. Determine the right delivery surface
Prefer:
- the specific channel requested by the user
- otherwise the channel where the collaboration should happen
- otherwise the current shared channel if that is the obvious default

Do not open unnecessary threads unless explicitly requested or clearly beneficial.

Post coordination messages as normal channel messages; do **not** use Discord reply/threaded-reply functionality unless the user explicitly requests it.

For gateway/profile settings that affect whether bot mentions are accepted (`allow_bots`, allowed users/channels, mention-gating, session grouping), load `discord-configuration`.

### 3. Write the request for actionability
A good mention message includes:
- direct mention
- concrete request
- minimal context
- success condition if relevant

Good format:
```text
<@USER_ID> please do X in this channel.
```

Better format when context matters:
```text
<@USER_ID> please do X here. Context: Y. Reply when done.
```

### 4. Keep it concise
The mention should be short enough that the target bot/person can act immediately.

Avoid:
- unnecessary backstory
- unrelated skills/catalogs
- ambiguous pronouns like "same thing" without a nearby referent

If the request is "do the same thing," restate the thing explicitly in one line.

### 5. Send the message
Use `send_message(...)` to the resolved target channel.

If the task is specifically about getting another bot's attention, prefer a real mention in the message body rather than relying only on channel delivery.

### 6. Confirm back to the user
After sending, report:
- who you mentioned
- where you sent it
- the message ID if available

## Recommended message templates

### Simple request
```text
<@USER_ID> please handle this in this channel.
```

### Request with explicit task
```text
<@USER_ID> please configure your free-response behavior the same way here: only free-respond in the allowed channel/DM, and require mention elsewhere.
```

### Request with reply signal
```text
<@USER_ID> please do X here and reply when done.
```

## Pitfalls

- Do not confuse "send a message to that channel" with "mention the specific target"
- Do not assume the other bot can infer "same thing" without explicit restatement
- Do not use this skill as a replacement for `delegate_task`; this is messaging coordination, not internal subagent execution
- Do not forget target/channel resolution when multiple similar targets exist
- Do not promise the other bot will comply; only confirm that the request was delivered

## Good defaults

- If the user specifies both a target and a channel, honor both
- If only the target is specified, use the current collaboration channel when reasonable
- If only the channel is specified, phrase the request in a way that clearly identifies the intended recipient if known
- If multiple bots are relevant, send separate tailored mentions instead of one ambiguous message

## Success checklist

- target resolved correctly
- correct channel chosen
- message includes an explicit mention when appropriate
- task is phrased concretely
- delivery confirmed back to the user
