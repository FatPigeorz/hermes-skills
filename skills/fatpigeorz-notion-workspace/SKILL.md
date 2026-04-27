---
name: fatpigeorz-notion-workspace
description: FatPigeorz's accessible Notion workspace structure and conventions for Hermes agents. Load with the generic notion skill before reading or writing the user's Notion workspace.
version: 0.1.0
author: FatPigeorz + Hermes
license: MIT
metadata:
  hermes:
    tags: [notion, workspace, fatpigeorz, agentix, research]
    related_skills: [notion]
---

# FatPigeorz Notion Workspace

Use this skill when the user asks about their Notion workspace, Agentix working area, research notes, experiments, literature, methods, or asks an agent to create/update Notion content.

Always load the generic `notion` skill too for API syntax and prerequisites.

## Credentials

- The Notion integration key is stored as `NOTION_API_KEY` in the default Hermes env: `/home/fatpigeorz/agents/.hermes/.env`.
- Tom's profile also has `NOTION_API_KEY` in `/home/fatpigeorz/agents/.hermes/profiles/tom/.env`.
- Never print or commit the Notion API key.
- Target pages/databases must be explicitly shared with the Notion integration in the Notion UI before the API can see them.

## Current API version note

The generic Notion skill documents newer `data_sources` endpoints, but this workspace was successfully inspected with Notion API version `2022-06-28` and classic database endpoints:

- Search: `POST /v1/search`
- Query database: `POST /v1/databases/{database_id}/query`
- Read children: `GET /v1/blocks/{block_id}/children`

Use the working version first if newer endpoints fail.

## Accessible workspace map

As of 2026-04-27, the Notion integration can see 6 objects.

### Top-level / project hierarchy

```text
Research Projects — page 34bb24bd-0bb7-80ff-8f94-ccd891aa66e8
└── test — page 34bb24bd-0bb7-810c-84b6-c88dbe1504f5
    ├── Literature — page 34bb24bd-0bb7-8135-bd53-e9ebde3dcf83
    ├── Experiments — page 34bb24bd-0bb7-8102-94f5-e23916311a38
    │   └── Experiments — database 8ea82fa3-d1df-4f17-8cd6-e9ff430a5d38
    └── Methods — page 34bb24bd-0bb7-8181-a099-ed22ead0bb93
```

### Pages

| Title | Type | ID | Notes |
|---|---|---|---|
| Research Projects | page | `34bb24bd-0bb7-80ff-8f94-ccd891aa66e8` | Contains child page `test`. |
| test | page | `34bb24bd-0bb7-810c-84b6-c88dbe1504f5` | Contains `Literature`, `Experiments`, and `Methods`. |
| Literature | page | `34bb24bd-0bb7-8135-bd53-e9ebde3dcf83` | Currently no visible blocks via API. |
| Experiments | page | `34bb24bd-0bb7-8102-94f5-e23916311a38` | Contains the `Experiments` database. |
| Methods | page | `34bb24bd-0bb7-8181-a099-ed22ead0bb93` | Currently no visible blocks via API. |

### Databases

#### Experiments

- Database ID: `8ea82fa3-d1df-4f17-8cd6-e9ff430a5d38`
- URL: `https://www.notion.so/8ea82fa3d1df4f178cd6e9ff430a5d38`
- Parent page: `Experiments` page `34bb24bd-0bb7-8102-94f5-e23916311a38`
- Current sample rows: none visible at inspection time.

Properties:

| Property | Type | Use |
|---|---|---|
| Name | title | Experiment title/name. |
| Motivation | rich_text | Why the experiment exists; hypothesis or objective. |
| Finding | rich_text | Result, conclusion, or observed finding. |

## Agentix note

The user says the main working place is **Agentix**, but `Agentix` was not visible to the Notion integration during inspection. Searches for `Agentix`, `agentix`, and `Agent` returned no results, and accessible page block content did not include Agentix.

Likely cause: the Agentix page/database has not been shared with the Notion integration yet.

When the user asks to work in Agentix:

1. Search Notion for `Agentix` first.
2. If not found, explain that the integration currently cannot access Agentix.
3. Ask the user to open Agentix in Notion → `...` menu → Connections / Connect to → select the Hermes/Notion integration.
4. After access is granted, re-scan and update this skill with the Agentix page/database IDs and structure.

## Common operations for this workspace

### Search accessible pages/databases

```bash
set -a; . /home/fatpigeorz/agents/.hermes/.env; set +a
curl -s -X POST 'https://api.notion.com/v1/search' \
  -H "Authorization: Bearer $NOTION_API_KEY" \
  -H 'Notion-Version: 2022-06-28' \
  -H 'Content-Type: application/json' \
  -d '{"page_size": 20}' | jq '.results[] | {object, id, url}'
```

### Query Experiments database

```bash
set -a; . /home/fatpigeorz/agents/.hermes/.env; set +a
curl -s -X POST 'https://api.notion.com/v1/databases/8ea82fa3-d1df-4f17-8cd6-e9ff430a5d38/query' \
  -H "Authorization: Bearer $NOTION_API_KEY" \
  -H 'Notion-Version: 2022-06-28' \
  -H 'Content-Type: application/json' \
  -d '{"page_size": 10}'
```

### Add an experiment row

```bash
set -a; . /home/fatpigeorz/agents/.hermes/.env; set +a
curl -s -X POST 'https://api.notion.com/v1/pages' \
  -H "Authorization: Bearer $NOTION_API_KEY" \
  -H 'Notion-Version: 2022-06-28' \
  -H 'Content-Type: application/json' \
  -d '{
    "parent": {"database_id": "8ea82fa3-d1df-4f17-8cd6-e9ff430a5d38"},
    "properties": {
      "Name": {"title": [{"text": {"content": "Experiment name"}}]},
      "Motivation": {"rich_text": [{"text": {"content": "Why this experiment matters"}}]},
      "Finding": {"rich_text": [{"text": {"content": "Result or TBD"}}]}
    }
  }'
```

## Maintenance

When more Notion pages/databases become accessible, update this skill immediately with:

- page/database titles and IDs
- parent/child hierarchy
- database schema/property types
- known conventions for where agents should write notes, plans, experiments, methods, and literature
