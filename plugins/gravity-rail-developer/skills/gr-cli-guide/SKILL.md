---
name: gr-cli-guide
description: Guide for using the @gravity-rail/cli to manage workspaces, members, chats, workflows, and 40+ domains from the command line.
---

# Gravity Rail CLI Guide

The `@gravity-rail/cli` package provides a command-line interface for managing Gravity Rail workspaces. It connects to `https://api.gravityrail.com` by default.

## Installation

```bash
# Global install
npm install -g @gravity-rail/cli

# Or use npx (no install)
npx @gravity-rail/cli --help

# Or with yarn
yarn global add @gravity-rail/cli
```

After installing globally, the `gr` command is available:

```bash
gr --help
```

## Authentication

### OAuth2 (interactive)

```bash
gr login
```

Opens your browser for OAuth authorization. Credentials are cached locally in `~/.gr-cli/credentials-prod.json`.

### API Key (non-interactive / CI)

```bash
export GRAVITY_RAIL_API_KEY=your-api-key
gr tasks list -w $WORKSPACE_UUID
```

### Check session

```bash
gr whoami
gr logout
```

## Command Pattern

```
gr <domain> <action> [flags]
gr <domain> <sub-resource> <action> [flags]
```

Every domain supports `--help` at every level:

```bash
gr --help              # all domains
gr tasks --help        # task actions
gr chats labels --help # chat label actions
```

## Global Flags

| Flag | Description |
|---|---|
| `--api-url <url>` | API base URL (default: `https://api.gravityrail.com`) |
| `-w, --workspace <uuid>` | Workspace UUID |
| `-o, --output-format <fmt>` | `json`, `jsonl`, `table` |
| `--allow-write` | Required for any mutating operation |
| `--id <id>` | Entity ID for get/update/delete |
| `-d, --data <json>` | Inline JSON payload |
| `-f, --file <path>` | JSON payload from file |
| `-v, --verbose` | Verbose output to stderr |

## Write Safety

The CLI is **read-only by default**. Any create, update, or delete command requires `--allow-write`:

```bash
# Reads — no flag needed
gr tasks list -w $UUID
gr members get -w $UUID --id 42

# Writes — requires --allow-write
gr tasks create -w $UUID --data '{"name":"New Task"}' --allow-write
gr members delete -w $UUID --id 42 --allow-write
```

This prevents accidental mutations when exploring production data.

## Output Formats

Output auto-detects: table for TTY, JSON when piped.

```bash
gr tasks list -w $UUID                    # table in terminal
gr tasks list -w $UUID -o json            # explicit JSON
gr tasks list -w $UUID -o json | jq '.'   # pipe to jq
gr tasks list -w $UUID -o jsonl           # one JSON object per line
```

JSON data can be piped as input too:

```bash
echo '{"name":"Test"}' | gr tasks create -w $UUID --allow-write
cat config.json | gr workspaces update -w $UUID --allow-write
```

## Domains

### Core

| Domain | Description |
|---|---|
| `tasks` | Tasks — CRUD, archive, clone |
| `chats` | Chats — CRUD, labels, filters, messages, export |
| `members` | Members — CRUD, labels, filters, roles, fields, credentials |
| `workspaces` | Workspaces — CRUD, features, notifications, DMs, themes |
| `workflows` | Workflows — CRUD, notes, contributors, templates |
| `agents` | AI agents — CRUD, archive |
| `personas` | AI personas — CRUD with model and voice configuration (formerly `assistants`) |
| `journeys` | Multi-step member journeys — CRUD, archive, notes, membership |
| `assignments` | Assignments — CRUD, messages, tasks, tools |
| `data-types` | Data types and records — CRUD, field indexing |
| `sites` | Sites, pages, menus — CRUD |
| `files` | Files, folders, labels — CRUD |
| `events` | Event rules and custom events — CRUD |
| `calendars` | Calendars, schedules, events, event types — CRUD |
| `qualifications` | Qualifications, requirements, submissions |

### Communication

| Domain | Description |
|---|---|
| `phone-numbers` | Phone numbers |
| `inboxes` | Email inboxes |
| `notification-rules` | Notification rules |

### Integrations

| Domain | Description |
|---|---|
| `discord-bots` | Discord bot integrations |
| `slack-apps` | Slack app integrations |
| `fhir-connections` | FHIR connection integrations |
| `app-connections` | App connections |
| `monday` | Monday.com integrations |
| `hubspot` | HubSpot CRM integration |
| `salesforce` | Salesforce integration |
| `confluence` | Confluence integration |

### Platform

| Domain | Description |
|---|---|
| `operator-groups` | Operator groups and presence |
| `access-grants` | Access grants |
| `supervisors` | AI supervisors |
| `routines` | Scheduled remote-agent runs — list, run, view run history |
| `milestones` | Milestones |
| `custom-toolkits` | Custom toolkits and tools |
| `mcp-servers` | MCP server connections |
| `api-keys` | API keys |
| `subscriptions` | Subscriptions |
| `reports` | Usage reports |
| `ai-models` | AI models (global) |
| `features` | Feature registry (global) |
| `pronunciations` | Pronunciation entries |
| `org` | Organization membership |
| `auth` | 2FA, credentials, passwords |
| `apps` | Apps and builders (global) |
| `oauth` | OAuth connections (global) |

## Special Commands

| Command | Description |
|---|---|
| `login` | Authenticate via OAuth (opens browser) |
| `logout` | Clear cached credentials |
| `whoami` | Show current user info |
| `organizations list` | List organizations (alias: `orgs list`) |
| `dev import-local` | Import remote workspace locally |

## CRUD Pattern

Most domains follow a standard CRUD pattern:

```bash
# List all
gr tasks list -w $UUID

# Get one by ID
gr tasks get -w $UUID --id 42

# Create
gr tasks create -w $UUID --data '{"name":"My Task"}' --allow-write

# Update
gr tasks update -w $UUID --id 42 --data '{"name":"Updated"}' --allow-write

# Delete
gr tasks delete -w $UUID --id 42 --allow-write
```

Sub-resources follow the same pattern with an extra path level:

```bash
gr chats labels list -w $UUID
gr chats labels create -w $UUID --data '{"name":"VIP","color":"#FFD700"}' --allow-write
```

## Recipes

### Export all chats as JSON

```bash
gr chats list -w $UUID -o json > chats.json
```

### Find members by searching output

```bash
gr members list -w $UUID -o json | jq '.[] | select(.email | contains("@example.com"))'
```

### Bulk create members from file

```bash
# members.json contains an array of member objects
cat members.json | jq -c '.[]' | while read member; do
  echo "$member" | gr members create -w $UUID --allow-write
done
```

### List workflows and their IDs

```bash
gr workflows list -w $UUID -o json | jq '.[] | {id, name}'
```

### Export a chat's messages

```bash
gr chats messages list -w $UUID --id $CHAT_ID -o json > messages.json
```

### Send a message and stream the AI response

```bash
gr chats messages send -w $UUID --id $CHAT_ID --data '"Hello, how can I help?"' --stream --allow-write
```

### Create a data type with fields

```bash
gr data-types create -w $UUID --data '{
  "name": "Contact Form",
  "slug": "contact-form",
  "is_collection": true,
  "fields": [
    {"name": "Full Name", "field_type": "text", "required": true},
    {"name": "Email", "field_type": "email", "required": true}
  ]
}' --allow-write
```

### Chain commands: get workspace, then list its workflows

```bash
WID=$(gr workspaces list -o json | jq -r '.[0].uuid')
gr workflows list -w $WID
```

### Check API key scopes

```bash
gr api-keys list -w $UUID -o json | jq '.[] | {id, name, scopes}'
```

## Scripting & CI Tips

- Use `-o json` for machine-readable output
- Use `-o jsonl` for streaming processing (one JSON object per line)
- Set `GRAVITY_RAIL_API_KEY` environment variable for non-interactive auth
- Use `--api-url` to point at a different API endpoint
- Combine with `jq` for filtering, transforming, and extracting fields
- Use `--allow-write` explicitly — it's a safety gate, not a bug

## Discovery

The CLI is self-documenting. When in doubt:

```bash
gr --help                 # list all domains
gr <domain> --help        # list domain actions
gr <domain> <sub> --help  # list sub-resource actions
```

## Overriding the API URL

The CLI connects to `https://api.gravityrail.com` by default. Override with:

- `--api-url <url>` flag
- `GRAVITY_RAIL_API_URL` environment variable
- `GRAVITY_RAIL_FRONTEND_URL` environment variable (for OAuth redirect)
