---
name: gr-cli-guide
description: >
  Command-line reference for @gravity-rail/cli — domain listing, CRUD patterns,
  output formats, and write safety. Use when scripting or exploring the API
  from a terminal.
keywords:
  - gr
  - cli
  - terminal
  - command-line
  - bash
  - scripting
---

# Gravity Rail CLI Guide

Public CLI for `@gravity-rail/cli`. Pattern: `gr <domain> <action> [options]`.

## Quick start

```bash
npm install -g @gravity-rail/cli
gr login
gr workflows list -w $WORKSPACE_UUID -o json
gr tasks get -w $WORKSPACE_UUID --id 42
```

## Authentication

| Method | Usage |
|---|---|
| OAuth | `gr login` / `gr logout` / `gr whoami` |
| API key | `export GRAVITY_RAIL_API_KEY=…` then run commands |

## Global options

| Flag | Description |
|---|---|
| `-w, --workspace <uuid>` | Workspace UUID (required for most commands) |
| `-o, --output-format <fmt>` | `json`, `jsonl`, or `table` |
| `--allow-write` | Required for create/update/delete/archive |
| `-d, --data <json>` / `-f, --file <path>` | JSON body for writes |
| `--help` | Domain or command help (`gr tasks --help`) |

## Write safety

Reads work without flags. Mutations require `--allow-write`:

```bash
gr members list -w $WORKSPACE_UUID
gr members create -w $WORKSPACE_UUID --data '{"first_name":"Jane"}' --allow-write
```

## Output

Prefer `-o json` when parsing. Table is default in an interactive TTY.

```bash
gr chats list -w $WORKSPACE_UUID -o json | jq '.[].id'
gr chats token-usage -w $WORKSPACE_UUID --id 42 -o json
```

## Discover commands

```bash
gr --help
gr chats --help
gr workflows tasks --help
```

<!-- generated:cli-registry -->

## Domains

| Domain | Description |
|---|---|
| `tasks` | Manage tasks |
| `chats` | Manage chats |
| `members` | Manage members |
| `member-roles` | Workspace member roles (alias for gr members roles). Use when resolving anonymousMemberRoleId / signupMemberRoleId. |
| `workspaces` | Manage workspaces |
| `workflows` | Manage workflows |
| `workflow-versions` | Manage workflow versions (publish, activate, inspect snapshots) |
| `agents` | Manage agents |
| `agent-tasks` | Inspect and cancel async sub-agent tasks (operator-facing; requires members_read/write) |
| `assignments` | Manage assignments |
| `data-types` | Manage data types and records |
| `sites` | Manage sites, pages, and menus |
| `files` | Manage files, folders, and labels |
| `events` | Manage event rules and custom events |
| `experiments` | Manage member A/B experiments, groups, and reporting |
| `calendars` | Manage calendars, schedules, events, and event types |
| `qualifications` | Manage qualifications, scenarios, and test chat runs |
| `personas` | Manage workspace-scoped test personas (identity templates for scenario runs) |
| `journeys` | Manage journeys, steps, goals, connections, and enrollment |
| `phone-numbers` | Workspace phone numbers (inbound/outbound voice & SMS). Provisioning chain (org → workspace):
  1. gr org phone-numbers search --org <org-uuid> [--area-code 775]
  2. gr org phone-numbers purchase --org <org-uuid> --data '{"phoneNumber":"+1…"}' --allow-write
  3. gr org phone-numbers assign --org <org-uuid> --data '{"providerPhoneUuid":"…","workspaceUuid":"…"}' --allow-write
  4. gr phone-numbers create -w $WID --data '{"name":"…","providerPhoneUuid":"…"}' --allow-write
     OR gr phone-numbers link -w $WID --id <id> --provider-phone-uuid <uuid> --allow-write
  5. gr phone-numbers update --id <id> --data '{"defaultWorkflowId":<wf>}' --allow-write

IDs: workspace phone id (numeric) vs uuid (string, used in event rules phoneNumberUUID).
Inbound: phone_numbers.defaultWorkflowId  |  Outbound: workflows.defaultPhoneNumberId |
| `inboxes` | Manage email inboxes |
| `operator-groups` | Manage operator groups |
| `access-grants` | Manage access grants |
| `notification-rules` | Manage notification rules |
| `supervisors` | Manage supervisors |
| `prompts` | Inspect and debug LLM prompts |
| `milestones` | Manage milestones |
| `custom-toolkits` | Manage custom toolkits and their tools |
| `mcp-servers` | Manage MCP servers |
| `api-keys` | Manage API keys |
| `subscriptions` | Manage subscriptions |
| `support` | Manage support requests |
| `reports` | View usage reports |
| `ai-models` | List available AI models |
| `features` | List available features |
| `pronunciations` | Manage pronunciation entries (org-scoped; -w resolves organization) |
| `org` | Organization membership and domains |
| `auth` | Manage authentication, 2FA, and credentials |
| `apps` | Manage apps, builders, and inquiries |
| `oauth` | Manage OAuth connections |
| `concierge` | Concierge chat management |
| `member-memories` | Manage member persistent memories |
| `sync` | Run and monitor connection syncs |
| `confluence` | Confluence knowledge base integration |
| `discord-bots` | Manage Discord bot integrations |
| `slack-apps` | Manage Slack app integrations |
| `teams-apps` | Manage Microsoft Teams app integrations |
| `fhir-connections` | Manage FHIR connections |
| `app-connections` | Manage platform app connections |
| `hubspot` | HubSpot CRM integration |
| `monday` | Monday.com integration |
| `salesforce` | Salesforce CRM integration |
| `billing` | Billing portal (customer invoices, subscriptions) |
| `login` | Authenticate via OAuth |
| `logout` | Clear cached credentials |
| `manage` | Interactive workspace manager chat (text, realtime, or voice) |
| `chat` | Chat with a workflow, member, or existing chat (text, realtime, or voice) |
| `get-started` | Bootstrap instructions for new users and LLM agents |
| `setup` | Alias for get-started — bootstrap instructions |
| `whoami` | Show current user info |

## Command index

#### `access-grants`

- `gr access-grants create` — Create an access grant _(workspace, write)_
- `gr access-grants get` — Get an access grant by ID _(workspace)_
- `gr access-grants given` — List access grants given by me _(workspace)_
- `gr access-grants list` — List all access grants _(workspace)_
- `gr access-grants member` — List access grants for a specific member _(workspace)_
- `gr access-grants received` — List access grants received by me _(workspace)_
- `gr access-grants resolve` — Resolve an access grant _(workspace)_
- `gr access-grants revoke` — Revoke an access grant _(workspace, write)_

#### `account`

- `gr account verify-phone` — Verify your account phone for concierge chat --call / --sms _(write)_

#### `agent-tasks`

- `gr agent-tasks cancel` — Cancel a running agent task (idempotent; terminal tasks are returned as-is) _(workspace, write)_
- `gr agent-tasks get` — Get a single agent task by UUID _(workspace)_
- `gr agent-tasks list` — List agent tasks in the workspace (newest first) _(workspace)_
- `gr agent-tasks messages` — Show recent messages from a sub-agent task context chat _(workspace)_

#### `agents`

- `gr agents archive` — Archive a agent _(workspace, write)_
- `gr agents create` — Create a agent _(workspace, write)_
- `gr agents delete` — Delete a agent _(workspace, write)_
- `gr agents get` — Get a agent by ID _(workspace)_
- `gr agents list` — List agents _(workspace)_
- `gr agents unarchive` — Unarchive a agent _(workspace, write)_
- `gr agents update` — Update a agent _(workspace, write)_

#### `ai-models`

- `gr ai-models chat` — List chat models
- `gr ai-models list` — List all available AI models
- `gr ai-models realtime` — List realtime models
- `gr ai-models voice` — List voice models

#### `api-keys`

- `gr api-keys create` — Create a workspace API key _(workspace, write)_
- `gr api-keys global create` — Create a global/account-level API key _(write)_
- `gr api-keys global list` — List global/account-level API keys
- `gr api-keys global revoke` — Revoke a global/account-level API key _(write)_
- `gr api-keys list` — List workspace API keys _(workspace)_
- `gr api-keys revoke` — Revoke a workspace API key _(workspace, write)_

#### `app-connections`

- `gr app-connections available` — List available app connections _(workspace)_
- `gr app-connections delete` — Delete a platform app _(workspace, write)_
- `gr app-connections enable` — Enable a platform app _(workspace, write)_
- `gr app-connections get` — Get a platform app _(workspace)_
- `gr app-connections list` — List enabled app connections _(workspace)_
- `gr app-connections relink-member` — Re-enable sync for a previously unlinked member by clearing the manual-unlink exclusion on this connection. Idempotent: returns cleared=false when no exclusion exists. _(workspace, write)_
- `gr app-connections sync-config` — Get sync configuration for a connection _(workspace)_
- `gr app-connections update` — Update a platform app _(workspace, write)_
- `gr app-connections update-sync-config` — Update sync configuration for a connection (pass raw config as --data) _(workspace, write)_

#### `apps`

- `gr apps builders apps` — List apps for a builder
- `gr apps builders create` — Create a builder _(write)_
- `gr apps builders delete` — Delete a builder _(write)_
- `gr apps builders get` — Get a builder by ID
- `gr apps builders list` — List builders
- `gr apps builders update` — Update a builder _(write)_
- `gr apps create` — Create an app _(write)_
- `gr apps delete` — Delete an app _(write)_
- `gr apps get` — Get an app by ID
- `gr apps inquiries create` — Create a inquiry _(write)_
- `gr apps inquiries delete` — Delete a inquiry _(write)_
- `gr apps inquiries get` — Get a inquiry by ID
- `gr apps inquiries list` — List inquirys
- `gr apps inquiries update` — Update a inquiry _(write)_
- `gr apps list` — List apps
- `gr apps regenerate-secret` — Regenerate client secret for an app
- `gr apps update` — Update an app _(write)_

#### `assignments`

- `gr assignments cancel` — Cancel an assignment _(workspace, write)_
- `gr assignments create` — Create a assignment _(workspace, write)_
- `gr assignments delete` — Delete a assignment _(workspace, write)_
- `gr assignments get` — Get a assignment by ID _(workspace)_
- `gr assignments list` — List assignments _(workspace)_
- `gr assignments messages list` — List messages for an assignment _(workspace)_
- `gr assignments tasks detail` — Get current task detail for an assignment _(workspace)_
- `gr assignments tasks get` — Get an assignment task _(workspace)_
- `gr assignments tasks select` — Select current assignment task _(workspace, write)_
- `gr assignments tools list` — List tools for an assignment _(workspace)_
- `gr assignments update` — Update a assignment _(workspace, write)_

#### `auth`

- `gr auth 2fa-status` — Get two-factor authentication status
- `gr auth account` — Get current user account info
- `gr auth browser` — Open an authenticated browser session using CLI credentials
- `gr auth cancel-credential-change` — Cancel a pending credential change _(write)_
- `gr auth change-password` — Change password _(write)_
- `gr auth consent` — Get current consent state
- `gr auth consent-update` — Update consent (e.g. --data '{"consents":[{"consent_type":"cookie_analytics","granted":true}]}') _(write)_
- `gr auth password-status` — Get password status
- `gr auth pending-credential-changes` — List pending credential changes
- `gr auth request-credential-change` — Request a credential change _(write)_
- `gr auth set-password` — Set password (for accounts without one) _(write)_
- `gr auth update-account` — Update current user account _(write)_

#### `billing`

- `gr billing customer` — Get a customer by UUID
- `gr billing customers` — List your customer accounts
- `gr billing invoices` — List invoices for a customer
- `gr billing subscriptions` — List subscriptions for a customer
- `gr billing update-customer` — Update a customer record (billing owner only) _(write)_

#### `calendars`

- `gr calendars create` — Create a calendar _(workspace, write)_
- `gr calendars delete` — Delete a calendar _(workspace, write)_
- `gr calendars event-types create` — Create a calendar event type _(workspace, write)_
- `gr calendars event-types delete` — Delete a calendar event type _(workspace, write)_
- `gr calendars event-types get` — Get a calendar event type _(workspace)_
- `gr calendars event-types list` — List calendar event types _(workspace)_
- `gr calendars event-types update` — Update a calendar event type _(workspace, write)_
- `gr calendars events cancel` — Cancel a calendar event _(workspace, write)_
- `gr calendars events create` — Create a calendar event _(workspace, write)_
- `gr calendars events delete` — Delete a calendar event _(workspace, write)_
- `gr calendars events get` — Get a calendar event _(workspace)_
- `gr calendars events list` — List calendar events (optionally filter by calendar) _(workspace)_
- `gr calendars events update` — Update a calendar event _(workspace, write)_
- `gr calendars get` — Get a calendar by ID _(workspace)_
- `gr calendars list` — List calendars _(workspace)_
- `gr calendars schedules create` — Create a schedule _(workspace, write)_
- `gr calendars schedules delete` — Delete a schedule _(workspace, write)_
- `gr calendars schedules list` — List schedules for a calendar _(workspace)_
- `gr calendars schedules update` — Update a schedule _(workspace, write)_
- `gr calendars update` — Update a calendar _(workspace, write)_

#### `chat`

- `gr chat` — Chat with a workflow, member, or existing chat (text, realtime, or voice) _(workspace, write)_

#### `chats`

- `gr chats archive` — Archive a chat _(workspace, write)_
- `gr chats create` — Create a chat _(workspace, write)_
- `gr chats delete` — Delete a chat _(workspace, write)_
- `gr chats export` — Export a single chat _(workspace)_
- `gr chats export-all` — Export all chats _(workspace)_
- `gr chats filters create` — Create a chat filter _(workspace, write)_
- `gr chats filters delete` — Delete a chat filter _(workspace, write)_
- `gr chats filters get` — Get a chat filter by ID _(workspace)_
- `gr chats filters list` — List chat filters _(workspace)_
- `gr chats filters update` — Update a chat filter _(workspace, write)_
- `gr chats get` — Get a chat by ID _(workspace)_
- `gr chats inspect` — Inspect a chat with messages, assignment state, and data records _(workspace)_
- `gr chats labels create` — Create a chat label _(workspace, write)_
- `gr chats labels delete` — Delete a chat label _(workspace, write)_
- `gr chats labels get` — Get a chat label by ID _(workspace)_
- `gr chats labels list` — List chat labels _(workspace)_
- `gr chats labels update` — Update a chat label _(workspace, write)_
- `gr chats list` — List chats _(workspace)_
- `gr chats messages list` — List messages for a chat. Use --limit/--offset to page; the API caps a single request at 100. Tool-call shell rows that the API filter excludes (parent_message_id IS NOT NULL or parent_call_id IS NOT NULL) are still hidden — use `gr chats inspect --id <chat>` for the full debug view. _(workspace)_
- `gr chats messages send` — Send a user message to a chat (triggers AI inference). Use --stream to stream the response. _(workspace, write)_
- `gr chats messages send-as-assistant` — Send an assistant message to a chat (does not trigger AI inference) _(workspace, write)_
- `gr chats needs-response` — List chats that need a response _(workspace)_
- `gr chats summarize` — Summarize a chat _(workspace)_
- `gr chats token-usage` — Get token usage for a chat _(workspace)_
- `gr chats unarchive` — Unarchive a chat _(workspace, write)_
- `gr chats update` — Update a chat _(workspace, write)_

#### `concierge`

- `gr concierge` — Call or SMS your verified phone via the Concierge (server-side) _(write)_
- `gr concierge chat` — Interactive concierge chat (text, realtime, or voice) _(write)_
- `gr concierge list` — List concierge chats
- `gr concierge messages` — Show messages for a concierge chat

#### `confluence`

- `gr confluence authorize` — Start Confluence OAuth flow _(workspace, write)_
- `gr confluence configure` — Configure Confluence OAuth credentials _(workspace, write)_
- `gr confluence delete` — Delete Confluence connection _(workspace, write)_
- `gr confluence disable` — Disable Confluence connection _(workspace, write)_
- `gr confluence disconnect` — Disconnect Confluence (clear tokens) _(workspace, write)_
- `gr confluence enable` — Enable Confluence connection _(workspace, write)_
- `gr confluence pages` — List pages in a Confluence space _(workspace)_
- `gr confluence spaces` — List available Confluence spaces _(workspace)_
- `gr confluence status` — Get Confluence connection status _(workspace)_
- `gr confluence sync-config` — Get Confluence sync configuration _(workspace)_
- `gr confluence update-sync-config` — Update Confluence sync configuration _(workspace, write)_

#### `custom-toolkits`

- `gr custom-toolkits create` — Create a custom toolkit _(workspace, write)_
- `gr custom-toolkits delete` — Delete a custom toolkit _(workspace, write)_
- `gr custom-toolkits disable` — Disable a custom toolkit _(workspace, write)_
- `gr custom-toolkits enable` — Enable a custom toolkit _(workspace, write)_
- `gr custom-toolkits get` — Get a custom toolkit by ID _(workspace)_
- `gr custom-toolkits list` — List custom toolkits _(workspace)_
- `gr custom-toolkits tools create` — Create a custom tool in a toolkit _(workspace, write)_
- `gr custom-toolkits tools delete` — Delete a custom tool _(workspace, write)_
- `gr custom-toolkits tools update` — Update a custom tool _(workspace, write)_
- `gr custom-toolkits update` — Update a custom toolkit _(workspace, write)_

#### `data-types`

- `gr data-types archive` — Archive a data type _(workspace, write)_
- `gr data-types clone` — Clone a data type _(workspace, write)_
- `gr data-types create` — Create a data type _(workspace, write)_
- `gr data-types delete` — Delete a data type _(workspace, write)_
- `gr data-types get` — Get a data type by ID _(workspace)_
- `gr data-types get-by-slug` — Get a data type by slug _(workspace)_
- `gr data-types list` — List data types _(workspace)_
- `gr data-types records create` — Create a data record _(workspace, write)_
- `gr data-types records delete` — Delete a data record _(workspace, write)_
- `gr data-types records get` — Get a data record _(workspace)_
- `gr data-types records list` — List records for a data type _(workspace)_
- `gr data-types records update` — Update a data record _(workspace, write)_
- `gr data-types unarchive` — Unarchive a data type _(workspace, write)_
- `gr data-types update` — Update a data type _(workspace, write)_

#### `dev`

- `gr dev import-local` — Import remote workspace locally _(workspace)_

#### `discord-bots`

- `gr discord-bots accounts` — List Discord bot accounts _(workspace)_
- `gr discord-bots create` — Create a Discord bot _(workspace, write)_
- `gr discord-bots delete` — Delete a Discord bot _(workspace, write)_
- `gr discord-bots get` — Get a Discord bot by ID _(workspace)_
- `gr discord-bots guilds` — List Discord guilds _(workspace)_
- `gr discord-bots list` — List Discord bots _(workspace)_
- `gr discord-bots register-commands` — Register slash commands for a Discord bot _(workspace, write)_
- `gr discord-bots threads` — List Discord threads _(workspace)_
- `gr discord-bots update` — Update a Discord bot _(workspace, write)_

#### `events`

- `gr events get` — Get a event rule by ID (alias for events rules get) _(workspace)_
- `gr events list` — List event rules (use --journey-step-id or --event-type to filter) (alias for events rules list) _(workspace)_
- `gr events rules create` — Create an event rule


Field reference for --data JSON (create/update):

Required
  objectType (string)                   task | routine | journey | workflow | calendar | ...
  eventType (string)                    e.g. routine:triggered, journey_step:execute, chat:created
  actionType (string)                   e.g. chat:create, member:label, sms:send, webhook:send

Common optional
  name (string)
  active (bool)
  order (number)                        Execution order when multiple rules match
  delay (number)                        Seconds to wait before running the action
  conditions (string)                   CEL expression; omit when always true
  actionParams (object)                 Shape depends on actionType — see examples below

Scope (set exactly one when objectType requires it)
  routineId, workflowId, taskId, dataTypeId, journeyStepId, calendarId, ...

chat:create actionParams (outbound voice/SMS)
  {"workflowId":<id>,"channel":"phone-voice"|"phone-sms","phoneNumberId":<id>,
   "inboxId":<optional>,"assistantEnabled":<bool>}

member:label actionParams
  {"addLabelIds":["<uuid>"],"removeLabelIds":["<uuid>"]}
 _(workspace, write)_
- `gr events rules delete` — Delete a event rule _(workspace, write)_
- `gr events rules executions` — List executions for an event rule _(workspace)_
- `gr events rules get` — Get a event rule by ID _(workspace)_
- `gr events rules list` — List event rules (use --journey-step-id or --event-type to filter) _(workspace)_
- `gr events rules search` — Search event rules by object/event/action type or related entity ID _(workspace)_
- `gr events rules update` — Update an event rule


Field reference for --data JSON (create/update):

Required
  objectType (string)                   task | routine | journey | workflow | calendar | ...
  eventType (string)                    e.g. routine:triggered, journey_step:execute, chat:created
  actionType (string)                   e.g. chat:create, member:label, sms:send, webhook:send

Common optional
  name (string)
  active (bool)
  order (number)                        Execution order when multiple rules match
  delay (number)                        Seconds to wait before running the action
  conditions (string)                   CEL expression; omit when always true
  actionParams (object)                 Shape depends on actionType — see examples below

Scope (set exactly one when objectType requires it)
  routineId, workflowId, taskId, dataTypeId, journeyStepId, calendarId, ...

chat:create actionParams (outbound voice/SMS)
  {"workflowId":<id>,"channel":"phone-voice"|"phone-sms","phoneNumberId":<id>,
   "inboxId":<optional>,"assistantEnabled":<bool>}

member:label actionParams
  {"addLabelIds":["<uuid>"],"removeLabelIds":["<uuid>"]}
 _(workspace, write)_

#### `experiments`

- `gr experiments create` — Create a draft experiment (pass JSON via --data) _(workspace, write)_
- `gr experiments delete` — Delete a draft experiment or archive a running one _(workspace, write)_
- `gr experiments get` — Get an experiment by ID _(workspace)_
- `gr experiments groups create` — Create a group on an experiment (pass JSON via --data) _(workspace, write)_
- `gr experiments groups delete` — Delete a group with no member assignments _(workspace, write)_
- `gr experiments groups update` — Update a group (pass JSON via --data) _(workspace, write)_
- `gr experiments list` — List experiments _(workspace)_
- `gr experiments members list` — List experiment assignments for a member _(workspace)_
- `gr experiments stats` — Get assignment and exposure counts by group _(workspace)_
- `gr experiments update` — Update an experiment (pass JSON via --data) _(workspace, write)_

#### `features`

- `gr features get` — Get a feature by slug
- `gr features list` — List all available features
- `gr features workspace` — List available features for a workspace _(workspace)_

#### `fhir-connections`

- `gr fhir-connections create` — Create a FHIR connection _(workspace, write)_
- `gr fhir-connections delete` — Delete a FHIR connection _(workspace, write)_
- `gr fhir-connections get` — Get a FHIR connection by ID _(workspace)_
- `gr fhir-connections list` — List FHIR connections _(workspace)_
- `gr fhir-connections test` — Test a FHIR connection _(workspace)_
- `gr fhir-connections update` — Update a FHIR connection _(workspace, write)_

#### `files`

- `gr files create` — Create a file _(workspace, write)_
- `gr files delete` — Delete a file _(workspace, write)_
- `gr files folders create` — Create a folder _(workspace, write)_
- `gr files folders delete` — Delete a folder _(workspace, write)_
- `gr files folders get` — Get a folder by ID _(workspace)_
- `gr files folders list` — List folders _(workspace)_
- `gr files folders reindex` — Reindex a folder _(workspace, write)_
- `gr files folders update` — Update a folder _(workspace, write)_
- `gr files get` — Get a file by ID _(workspace)_
- `gr files labels create` — Create a file label _(workspace, write)_
- `gr files labels delete` — Delete a file label _(workspace, write)_
- `gr files labels get` — Get a file label by ID _(workspace)_
- `gr files labels list` — List file labels _(workspace)_
- `gr files labels update` — Update a file label _(workspace, write)_
- `gr files list` — List files _(workspace)_
- `gr files update` — Update a file _(workspace, write)_

#### `get-started`

- `gr get-started` — Bootstrap instructions for new users and LLM agents

#### `hubspot`

- `gr hubspot authorize` — Start HubSpot OAuth flow _(workspace, write)_
- `gr hubspot configure` — Configure HubSpot connection _(workspace, write)_
- `gr hubspot contact-properties` — List available HubSpot contact properties _(workspace)_
- `gr hubspot delete` — Delete HubSpot connection _(workspace, write)_
- `gr hubspot disable` — Disable HubSpot connection _(workspace, write)_
- `gr hubspot disconnect` — Disconnect HubSpot _(workspace, write)_
- `gr hubspot enable` — Enable HubSpot connection _(workspace, write)_
- `gr hubspot link-member` — Link a member to a HubSpot contact _(workspace, write)_
- `gr hubspot linked-members` — List members linked to HubSpot contacts _(workspace)_
- `gr hubspot managed-rules` — List HubSpot managed sync rules and their status _(workspace)_
- `gr hubspot reconciliation` — Find matching HubSpot contacts for workspace members _(workspace)_
- `gr hubspot status` — Get HubSpot connection status _(workspace)_
- `gr hubspot sync-config` — Get HubSpot sync configuration _(workspace)_
- `gr hubspot transform-presets` — List available HubSpot field transform presets _(workspace)_
- `gr hubspot unlink-member` — Unlink a member from HubSpot _(workspace, write)_
- `gr hubspot update-sync-config` — Update HubSpot sync configuration _(workspace, write)_

#### `inboxes`

- `gr inboxes create` — Create a inbox _(workspace, write)_
- `gr inboxes delete` — Delete a inbox _(workspace, write)_
- `gr inboxes emails get` — Get a specific email _(workspace)_
- `gr inboxes emails list` — List emails in an inbox _(workspace)_
- `gr inboxes emails mark-read` — Mark an email as read _(workspace, write)_
- `gr inboxes emails mark-unread` — Mark an email as unread _(workspace, write)_
- `gr inboxes emails threads` — List email threads in an inbox _(workspace)_
- `gr inboxes get` — Get a inbox by ID _(workspace)_
- `gr inboxes list` — List inboxs _(workspace)_
- `gr inboxes update` — Update a inbox _(workspace, write)_

#### `journeys`

- `gr journeys archive` — Archive a journey (soft delete) _(workspace, write)_
- `gr journeys connections create` — Create a DAG edge between two steps (with optional CEL condition) _(workspace, write)_
- `gr journeys connections delete` — Remove a connection _(workspace, write)_
- `gr journeys connections list` — List connections for a journey (aggregated from steps) _(workspace)_
- `gr journeys connections update` — Update a connection _(workspace, write)_
- `gr journeys create` — Create a journey _(workspace, write)_
- `gr journeys enroll` — Enroll member(s) in a journey _(workspace, write)_
- `gr journeys get` — Get a journey by ID _(workspace)_
- `gr journeys goals create` — Create a goal (high-value outcome to track) for a journey _(workspace, write)_
- `gr journeys goals delete` — Delete a goal _(workspace, write)_
- `gr journeys goals list` — List goals for a journey (from journey detail) _(workspace)_
- `gr journeys goals update` — Update a goal _(workspace, write)_
- `gr journeys list` — List all journeys in a workspace _(workspace)_
- `gr journeys members delete` — Hard-delete a canceled enrollment so member can re-enroll fresh _(workspace, write)_
- `gr journeys members get` — Get a member's journeys _(workspace)_
- `gr journeys members list` — List members enrolled in a journey _(workspace)_
- `gr journeys notes create` — Create a note on a journey (canvas sticky note) _(workspace, write)_
- `gr journeys notes delete` — Delete a journey note _(workspace, write)_
- `gr journeys notes list` — List notes for a journey _(workspace)_
- `gr journeys notes update` — Update a journey note _(workspace, write)_
- `gr journeys reenroll` — Re-enroll a canceled member back into a journey _(workspace, write)_
- `gr journeys step-member-counts` — Get member counts per step for a journey _(workspace)_
- `gr journeys steps actions get` — Get a journey step action (event rule) by ID _(workspace)_
- `gr journeys steps actions list` — List event rules (actions) attached to a journey step _(workspace)_
- `gr journeys steps actions update` — Update a journey step action — modify template, conditions, or active state _(workspace, write)_
- `gr journeys steps advance` — Mark a step as completed for a member (triggers downstream propagation) _(workspace, write)_
- `gr journeys steps archive` — Archive a step _(workspace, write)_
- `gr journeys steps create` — Create a step in a journey _(workspace, write)_
- `gr journeys steps list` — List steps for a journey (from journey detail) _(workspace)_
- `gr journeys steps members` — List members at a specific journey step _(workspace)_
- `gr journeys steps skip` — Skip a step for a member (requires journeys:admin scope) _(workspace, write)_
- `gr journeys steps update` — Update a step _(workspace, write)_
- `gr journeys unenroll` — Unenroll member(s) from a journey _(workspace, write)_
- `gr journeys update` — Update a journey _(workspace, write)_

#### `login`

- `gr login` — Authenticate via OAuth

#### `logout`

- `gr logout` — Clear cached credentials

#### `manage`

- `gr manage` — Interactive workspace manager chat (text, realtime, or voice) _(workspace, write)_

#### `mcp-servers`

- `gr mcp-servers create` — Create a MCP server _(workspace, write)_
- `gr mcp-servers delete` — Delete a MCP server _(workspace, write)_
- `gr mcp-servers disable` — Disable an MCP server _(workspace, write)_
- `gr mcp-servers enable` — Enable an MCP server _(workspace, write)_
- `gr mcp-servers get` — Get a MCP server by ID _(workspace)_
- `gr mcp-servers list` — List MCP servers _(workspace)_
- `gr mcp-servers tools list` — List tools for an MCP server _(workspace)_
- `gr mcp-servers update` — Update a MCP server _(workspace, write)_

#### `member-memories`

- `gr member-memories delete` — Delete a memory for a member _(workspace, write)_
- `gr member-memories list` — List memories for a member _(workspace)_

#### `member-roles`

- `gr member-roles create` — Create a member role _(workspace, write)_
- `gr member-roles delete` — Delete a member role _(workspace, write)_
- `gr member-roles get` — Get a member role by ID _(workspace)_
- `gr member-roles list` — List member roles _(workspace)_
- `gr member-roles update` — Update a member role _(workspace, write)_

#### `members`

- `gr members archive` — Archive a member _(workspace, write)_
- `gr members create` — Create a member _(workspace, write)_
- `gr members delete` — Delete a member _(workspace, write)_
- `gr members export` — Export members _(workspace)_
- `gr members fields create` — Create a member field _(workspace, write)_
- `gr members fields delete` — Delete a member field _(workspace, write)_
- `gr members fields list` — List fields for a member _(workspace)_
- `gr members fields update` — Update a member field _(workspace, write)_
- `gr members filters create` — Create a member filter _(workspace, write)_
- `gr members filters delete` — Delete a member filter _(workspace, write)_
- `gr members filters get` — Get a member filter by ID _(workspace)_
- `gr members filters list` — List member filters _(workspace)_
- `gr members filters update` — Update a member filter _(workspace, write)_
- `gr members get` — Get a member by ID _(workspace)_
- `gr members import` — Import members from data _(workspace, write)_
- `gr members labels create` — Create a member label _(workspace, write)_
- `gr members labels delete` — Delete a member label _(workspace, write)_
- `gr members labels get` — Get a member label by ID _(workspace)_
- `gr members labels list` — List member labels _(workspace)_
- `gr members labels update` — Update a member label _(workspace, write)_
- `gr members list` — List members _(workspace)_
- `gr members me` — Get the current member _(workspace)_
- `gr members roles create` — Create a member role _(workspace, write)_
- `gr members roles delete` — Delete a member role _(workspace, write)_
- `gr members roles get` — Get a member role by ID _(workspace)_
- `gr members roles list` — List member roles _(workspace)_
- `gr members roles update` — Update a member role _(workspace, write)_
- `gr members unarchive` — Unarchive a member _(workspace, write)_
- `gr members update` — Update a member _(workspace, write)_

#### `milestones`

- `gr milestones create` — Create a milestone _(workspace, write)_
- `gr milestones delete` — Delete a milestone _(workspace, write)_
- `gr milestones get` — Get a milestone by ID _(workspace)_
- `gr milestones list` — List milestones _(workspace)_
- `gr milestones update` — Update a milestone _(workspace, write)_

#### `monday`

- `gr monday authorize` — Get Monday.com OAuth authorization URL _(workspace)_
- `gr monday board-columns` — List columns for a Monday.com board _(workspace)_
- `gr monday board-groups` — List groups for a Monday.com board _(workspace)_
- `gr monday boards` — List Monday.com boards _(workspace)_
- `gr monday configure` — Configure Monday.com connection _(workspace, write)_
- `gr monday delete` — Delete Monday.com connection _(workspace, write)_
- `gr monday disable` — Disable Monday.com connection _(workspace, write)_
- `gr monday disconnect` — Disconnect Monday.com _(workspace, write)_
- `gr monday enable` — Enable Monday.com connection _(workspace, write)_
- `gr monday link-member` — Link a member to a Monday.com item _(workspace, write)_
- `gr monday linked-members` — List members linked to Monday.com items _(workspace)_
- `gr monday reconciliation` — Find matching Monday.com items for workspace members _(workspace)_
- `gr monday status` — Get Monday.com connection status _(workspace)_
- `gr monday sync-config` — Get Monday.com sync configuration _(workspace)_
- `gr monday unlink-member` — Unlink a member from Monday.com _(workspace, write)_
- `gr monday update-sync-config` — Update Monday.com sync configuration _(workspace, write)_
- `gr monday user-info` — Get current Monday.com user info _(workspace)_

#### `notification-rules`

- `gr notification-rules create` — Create a notification rule _(workspace, write)_
- `gr notification-rules delete` — Delete a notification rule _(workspace, write)_
- `gr notification-rules list` — List notification rules _(workspace)_
- `gr notification-rules update` — Update a notification rule _(workspace, write)_

#### `oauth`

- `gr oauth auth-url` — Get OAuth authorization URL for a provider
- `gr oauth disconnect` — Disconnect an OAuth provider _(write)_
- `gr oauth scopes` — List available OAuth scopes
- `gr oauth status` — Get OAuth connection status for a provider

#### `operator-groups`

- `gr operator-groups add-member` — Add a member to an operator group _(workspace, write)_
- `gr operator-groups create` — Create a operator group _(workspace, write)_
- `gr operator-groups delete` — Delete a operator group _(workspace, write)_
- `gr operator-groups get` — Get a operator group by ID _(workspace)_
- `gr operator-groups list` — List operator groups _(workspace)_
- `gr operator-groups live` — List live operators _(workspace)_
- `gr operator-groups presence` — Get presence status _(workspace)_
- `gr operator-groups remove-member` — Remove a member from an operator group _(workspace, write)_
- `gr operator-groups update` — Update a operator group _(workspace, write)_

#### `org`

- `gr org domains create` — Register an organization domain and return ownership TXT instructions _(write)_
- `gr org domains delete` — Delete a registered organization domain _(write)_
- `gr org domains email disable` — Disable email on an organization domain _(write)_
- `gr org domains email enable` — Enable email DNS records on a verified organization domain _(write)_
- `gr org domains email get` — Get email DNS setup and status for an organization domain
- `gr org domains email verify` — Verify email DNS records, including MX _(write)_
- `gr org domains email verify-mx` — Verify email DNS records, including MX _(write)_
- `gr org domains get` — Get a registered organization domain
- `gr org domains list` — List domains registered to an organization
- `gr org domains register` — Register an organization domain and return ownership TXT instructions _(write)_
- `gr org domains site disable` — Disable custom site routing on an organization domain _(write)_
- `gr org domains site enable` — Enable custom site CNAME verification on a verified organization domain _(write)_
- `gr org domains site get` — Get custom site CNAME setup and status for an organization domain
- `gr org domains site verify` — Verify the site CNAME points at Gravity Rail _(write)_
- `gr org domains site verify-cname` — Verify the site CNAME points at Gravity Rail _(write)_
- `gr org domains verify-ownership` — Verify domain ownership by checking the _gravity-verify TXT record _(write)_
- `gr org list` — List my organizations
- `gr org phone-numbers assign` — Assign an org phone number to a workspace _(write)_
- `gr org phone-numbers delete` — Delete a phone number from the org (releases from Twilio) _(write)_
- `gr org phone-numbers list` — List all phone numbers owned by an organization
- `gr org phone-numbers purchase` — Purchase a phone number from Twilio for this org _(write)_
- `gr org phone-numbers reassign` — Reassign a phone number to a different workspace _(write)_
- `gr org phone-numbers release` — Release a phone number from its current workspace _(write)_
- `gr org phone-numbers search` — Search for available phone numbers to purchase

#### `organizations`

- `gr organizations list` — List organizations

#### `orgs`

- `gr orgs list` — List organizations

#### `personas`

- `gr personas create` — Create a persona _(workspace, write)_
- `gr personas delete` — Delete a persona _(workspace, write)_
- `gr personas get` — Get a persona by ID _(workspace)_
- `gr personas list` — List personas _(workspace)_
- `gr personas update` — Update a persona _(workspace, write)_

#### `phone-numbers`

- `gr phone-numbers archive` — Archive a phone number _(workspace, write)_
- `gr phone-numbers available` — List available phone numbers for purchase _(workspace)_
- `gr phone-numbers calls inspect` — Inspect one call record (status, termination, chat linkage) _(workspace)_
- `gr phone-numbers calls list` — List calls for a phone number _(workspace)_
- `gr phone-numbers create` — Create a phone number

Field reference for --data JSON (create/update):

Identity & branding
  name (string)                         Display label
  brandName, brandDescription, brandWebsite (string, optional)

Inbound routing (phone_numbers.defaultWorkflowId = INBOUND workflow)
  defaultWorkflowId (number|null)       Workflow for inbound voice/SMS chats (required for voice)
  allowSignup (bool, default false)     Unknown callers can start signup flow
  signupMemberRoleId (number)           Required when allowSignup=true
  allowAnonymous (bool, default false)  Unknown callers get anonymous member per call
  anonymousMemberRoleId (number)        Required when allowAnonymous=true
  restartDelay (number|null, default 900) Seconds before a new inbound conversation starts
  restartPrompt (string|null)

Channels
  enableVoice (bool, default true)
  enableSms (bool, default true)
  enableWhatsApp (bool, default false)
  assistantEnabled (bool, default true)

Caller-facing messages
  voiceUnavailableMessage (string)      Played when voice cannot be handled
  voiceGreetingMessage (string)         Played before AI connects
  anonymousRejectionMessage (string)  Unknown caller denied (TTS)
  smsUnavailableMessage (string)
  voicemailGreeting (string)

Work modes (voiceWorkMode / smsWorkMode)
  always_forward | forward_off_hours | always_message | message_off_hours | voicemail | voicemail_off_hours
  voiceForwardNumber, smsForwardNumber, voiceOffHoursMessage, smsOffHoursMessage

Provider link
  providerPhoneUuid (string)            Org provider phone UUID — use gr phone-numbers link after org assign

Constraints
  allowAnonymous=true  → anonymousMemberRoleId required
  allowSignup=true     → signupMemberRoleId required

Outbound (different field on workflows, not this resource):
  workflows.defaultPhoneNumberId = OUTBOUND origin for that workflow
  Event rules may reference phoneNumberUUID (workspace phone uuid string)

List roles: gr member-roles list -w $WID
Provisioning: gr phone-numbers setup --help _(workspace, write)_
- `gr phone-numbers diagnose` — Simulate inbound routing for a caller number (no PSTN) _(workspace)_
- `gr phone-numbers experience` — Preview caller experience by role (no PSTN) _(workspace)_
- `gr phone-numbers get` — Get a phone number (behavior matrix; use --raw for JSON) _(workspace)_
- `gr phone-numbers inbound-log` — Recent inbound call activity for this line (includes pre-routing rejections via terminationReason) _(workspace)_
- `gr phone-numbers link` — Link a workspace phone number to a provider (org) phone _(workspace, write)_
- `gr phone-numbers list` — List phone numbers _(workspace)_
- `gr phone-numbers messages list` — List messages for a phone number _(workspace)_
- `gr phone-numbers request` — Request a phone number from Twilio _(workspace, write)_
- `gr phone-numbers setup` — Provision/link a workspace phone line with inbound defaults (happy-path wizard) _(workspace, write)_
- `gr phone-numbers test-inbound` — Server-side inbound routing simulation (JSON; no Twilio call) _(workspace)_
- `gr phone-numbers update` — Update a phone number

Field reference for --data JSON (create/update):

Identity & branding
  name (string)                         Display label
  brandName, brandDescription, brandWebsite (string, optional)

Inbound routing (phone_numbers.defaultWorkflowId = INBOUND workflow)
  defaultWorkflowId (number|null)       Workflow for inbound voice/SMS chats (required for voice)
  allowSignup (bool, default false)     Unknown callers can start signup flow
  signupMemberRoleId (number)           Required when allowSignup=true
  allowAnonymous (bool, default false)  Unknown callers get anonymous member per call
  anonymousMemberRoleId (number)        Required when allowAnonymous=true
  restartDelay (number|null, default 900) Seconds before a new inbound conversation starts
  restartPrompt (string|null)

Channels
  enableVoice (bool, default true)
  enableSms (bool, default true)
  enableWhatsApp (bool, default false)
  assistantEnabled (bool, default true)

Caller-facing messages
  voiceUnavailableMessage (string)      Played when voice cannot be handled
  voiceGreetingMessage (string)         Played before AI connects
  anonymousRejectionMessage (string)  Unknown caller denied (TTS)
  smsUnavailableMessage (string)
  voicemailGreeting (string)

Work modes (voiceWorkMode / smsWorkMode)
  always_forward | forward_off_hours | always_message | message_off_hours | voicemail | voicemail_off_hours
  voiceForwardNumber, smsForwardNumber, voiceOffHoursMessage, smsOffHoursMessage

Provider link
  providerPhoneUuid (string)            Org provider phone UUID — use gr phone-numbers link after org assign

Constraints
  allowAnonymous=true  → anonymousMemberRoleId required
  allowSignup=true     → signupMemberRoleId required

Outbound (different field on workflows, not this resource):
  workflows.defaultPhoneNumberId = OUTBOUND origin for that workflow
  Event rules may reference phoneNumberUUID (workspace phone uuid string)

List roles: gr member-roles list -w $WID
Provisioning: gr phone-numbers setup --help _(workspace, write)_
- `gr phone-numbers users` — List workflows and event rules using this number for outbound _(workspace)_

#### `prompts`

- `gr prompts inspect` — Show the full composed prompt for a task or chat, broken into layers _(workspace)_

#### `pronunciations`

- `gr pronunciations create` — Create a pronunciation entry (term + respelling) _(workspace, write)_
- `gr pronunciations delete` — Delete a pronunciation entry _(workspace, write)_
- `gr pronunciations list` — List pronunciation entries for the workspace organization _(workspace)_
- `gr pronunciations my-create` — Create a personal pronunciation _(workspace, write)_
- `gr pronunciations my-list` — List my personal pronunciations _(workspace)_
- `gr pronunciations suggest` — Get pronunciation suggestions for a word _(workspace)_
- `gr pronunciations update` — Update a pronunciation entry _(workspace, write)_
- `gr pronunciations voices` — Get recommended voices _(workspace)_

#### `qualifications`

- `gr qualifications for-workflow` — Fetch (auto-creating if absent) the qualification for a workflow _(workspace)_
- `gr qualifications get` — Get a qualification by ID _(workspace)_
- `gr qualifications list` — List qualifications (one per workflow) _(workspace)_
- `gr qualifications runs evaluate` — Evaluate a completed scenario-bound test chat run _(workspace, write)_
- `gr qualifications runs get` — Get a test chat run _(workspace)_
- `gr qualifications runs list` — List recent test chat runs for a workflow _(workspace)_
- `gr qualifications runs start` — Start a test chat run _(workspace, write)_
- `gr qualifications scenarios create` — Create a scenario on a qualification (pass JSON via --data) _(workspace, write)_
- `gr qualifications scenarios delete` — Archive a scenario _(workspace, write)_
- `gr qualifications scenarios get` — Get a scenario _(workspace)_
- `gr qualifications scenarios list` — List scenarios on a qualification _(workspace)_
- `gr qualifications scenarios update` — Update a scenario (pass JSON via --data) _(workspace, write)_

#### `reports`

- `gr reports ai-usage` — Get AI usage report _(workspace)_
- `gr reports member-usage` — Get usage for a specific member _(workspace)_
- `gr reports phone-usage` — Get phone usage report _(workspace)_
- `gr reports sms-usage` — Get SMS usage report _(workspace)_
- `gr reports voice-usage` — Get voice usage report _(workspace)_

#### `routines`

- `gr routines create` — Create a routine


Field reference for --data JSON (create/update):

Core
  name (string)                         Display name
  workflowId (number)                   Workflow run when the routine fires
  targetMemberId (number)               Member who receives the scheduled outreach

Schedule
  scheduleEnabled (bool)                Master on/off for the cron trigger
  scheduleMode (string)                 "CRON" (only mode today)
  cronString (string)                   Standard cron, e.g. "0 7 * * *"
  timezone (string, optional)           IANA name, e.g. "America/Los_Angeles"
                                        Defaults to workspace timezone, else UTC

Visibility / lifecycle
  archived (bool)                       Archive flag on update
  event rules                           Use gr events rules create — not inline here
 _(workspace, write)_
- `gr routines delete` — Delete a routine _(workspace, write)_
- `gr routines get` — Get a routine by ID _(workspace)_
- `gr routines list` — List routines _(workspace)_
- `gr routines run` — Run a routine now _(workspace, write)_
- `gr routines runs list` — List runs for a routine _(workspace)_
- `gr routines update` — Update a routine


Field reference for --data JSON (create/update):

Core
  name (string)                         Display name
  workflowId (number)                   Workflow run when the routine fires
  targetMemberId (number)               Member who receives the scheduled outreach

Schedule
  scheduleEnabled (bool)                Master on/off for the cron trigger
  scheduleMode (string)                 "CRON" (only mode today)
  cronString (string)                   Standard cron, e.g. "0 7 * * *"
  timezone (string, optional)           IANA name, e.g. "America/Los_Angeles"
                                        Defaults to workspace timezone, else UTC

Visibility / lifecycle
  archived (bool)                       Archive flag on update
  event rules                           Use gr events rules create — not inline here
 _(workspace, write)_

#### `salesforce`

- `gr salesforce authorize` — Start Salesforce OAuth flow _(workspace, write)_
- `gr salesforce configure` — Configure Salesforce connection _(workspace, write)_
- `gr salesforce delete` — Delete Salesforce connection _(workspace, write)_
- `gr salesforce describe` — Describe a Salesforce object schema _(workspace)_
- `gr salesforce disable` — Disable Salesforce connection _(workspace, write)_
- `gr salesforce disconnect` — Disconnect Salesforce _(workspace, write)_
- `gr salesforce enable` — Enable Salesforce connection _(workspace, write)_
- `gr salesforce link-member` — Link a member to a Salesforce record _(workspace, write)_
- `gr salesforce linked-members` — List members linked to Salesforce records _(workspace)_
- `gr salesforce reconciliation` — Find matching Salesforce records for workspace members _(workspace)_
- `gr salesforce search` — Search Salesforce records _(workspace)_
- `gr salesforce status` — Get Salesforce connection status _(workspace)_
- `gr salesforce sync-config` — Get Salesforce sync configuration _(workspace)_
- `gr salesforce unlink-member` — Unlink a member from Salesforce _(workspace, write)_
- `gr salesforce update-sync-config` — Update Salesforce sync configuration _(workspace, write)_

#### `setup`

- `gr setup` — Alias for get-started — bootstrap instructions

#### `sites`

- `gr sites create` — Create a site _(workspace, write)_
- `gr sites delete` — Delete a site _(workspace, write)_
- `gr sites get` — Get a site by ID _(workspace)_
- `gr sites list` — List sites _(workspace)_
- `gr sites menu create` — Create a menu item _(workspace, write)_
- `gr sites menu delete` — Delete a menu item _(workspace, write)_
- `gr sites menu list` — List menu items for a site _(workspace)_
- `gr sites menu update` — Update a menu item _(workspace, write)_
- `gr sites pages create` — Create a page _(workspace, write)_
- `gr sites pages delete` — Delete a page _(workspace, write)_
- `gr sites pages get` — Get a page _(workspace)_
- `gr sites pages list` — List pages for a site _(workspace)_
- `gr sites pages update` — Update a page _(workspace, write)_
- `gr sites update` — Update a site _(workspace, write)_

#### `slack-apps`

- `gr slack-apps accounts` — List Slack app accounts _(workspace)_
- `gr slack-apps create` — Create a Slack app _(workspace, write)_
- `gr slack-apps delete` — Delete a Slack app _(workspace, write)_
- `gr slack-apps get` — Get a Slack app by ID _(workspace)_
- `gr slack-apps installations` — List Slack installations _(workspace)_
- `gr slack-apps list` — List Slack apps _(workspace)_
- `gr slack-apps threads` — List Slack threads _(workspace)_
- `gr slack-apps update` — Update a Slack app _(workspace, write)_

#### `subscriptions`

- `gr subscriptions cancel` — Cancel a subscription _(write)_
- `gr subscriptions list` — List all subscriptions
- `gr subscriptions reactivate` — Reactivate a cancelled subscription
- `gr subscriptions update` — Update a subscription _(write)_
- `gr subscriptions workspace` — List subscriptions for a workspace _(workspace)_

#### `supervisors`

- `gr supervisors create` — Create a supervisor _(workspace, write)_
- `gr supervisors delete` — Delete a supervisor _(workspace, write)_
- `gr supervisors get` — Get a supervisor by ID _(workspace)_
- `gr supervisors list` — List supervisors _(workspace)_
- `gr supervisors update` — Update a supervisor _(workspace, write)_

#### `support`

- `gr support close` — Close one of your support requests (workspace-scoped) _(write)_
- `gr support comment` — Add a customer comment to a support request _(write)_
- `gr support create` — Create a support request _(write)_
- `gr support get` — Get a support request
- `gr support list` — List support requests
- `gr support resource-click` — Record a click on a pushed resource (consume + track usefulness) _(write)_

#### `sync`

- `gr sync config` — Get sync configuration for a connection
- `gr sync items` — List items for a specific sync run
- `gr sync run` — Start a sync run and stream progress (SSE) _(write)_
- `gr sync runs` — List recent sync runs for a connection
- `gr sync update-config` — Update sync configuration for a connection _(write)_

#### `tasks`

- `gr tasks ability-types` — List available ability types _(workspace)_
- `gr tasks archive` — Archive a task _(workspace, write)_
- `gr tasks clone` — Clone a task _(workspace, write)_
- `gr tasks create` — Create a task _(workspace, write)_
- `gr tasks delete` — Delete a task _(workspace, write)_
- `gr tasks get` — Get a task by ID _(workspace)_
- `gr tasks list` — List tasks _(workspace)_
- `gr tasks unarchive` — Unarchive a task _(workspace, write)_
- `gr tasks update` — Update a task _(workspace, write)_

#### `teams-apps`

- `gr teams-apps accounts` — List linked Teams (Entra) accounts for this app _(workspace)_
- `gr teams-apps conversations` — List Teams conversations linked to chats _(workspace)_
- `gr teams-apps create` — Create a Teams app _(workspace, write)_
- `gr teams-apps delete` — Delete a Teams app _(workspace, write)_
- `gr teams-apps get` — Get a Teams app by ID _(workspace)_
- `gr teams-apps installations` — List per-tenant Teams installations _(workspace)_
- `gr teams-apps list` — List Teams apps _(workspace)_
- `gr teams-apps update` — Update a Teams app _(workspace, write)_

#### `whoami`

- `gr whoami` — Show current user info

#### `workflow-versions`

- `gr workflow-versions activate` — Activate a version so it receives new assignments _(workspace, write)_
- `gr workflow-versions active` — Get the currently active version of a workflow _(workspace)_
- `gr workflow-versions deactivate` — Deactivate a version so it no longer receives new assignments _(workspace, write)_
- `gr workflow-versions diff` — Compare two versions (use 0 for draft) _(workspace)_
- `gr workflow-versions get` — Get a specific version by number (includes full snapshot) _(workspace)_
- `gr workflow-versions list` — List all published versions of a workflow _(workspace)_
- `gr workflow-versions publish` — Publish the live workflow as a new immutable version _(workspace, write)_

#### `workflows`

- `gr workflows activity` — Get workflow activity _(workspace)_
- `gr workflows archive` — Archive a workflow _(workspace, write)_
- `gr workflows clone` — Clone a workflow _(workspace, write)_
- `gr workflows contributors create` — Add a workflow contributor _(workspace, write)_
- `gr workflows contributors delete` — Remove a workflow contributor _(workspace, write)_
- `gr workflows contributors list` — List workflow contributors _(workspace)_
- `gr workflows contributors update` — Update a workflow contributor _(workspace, write)_
- `gr workflows create` — Create a workflow


Field reference for --data JSON (create/update):

Core
  name (string)                         Display name
  description (string, optional)
  defaultPhoneNumberId (number|null)    Outbound caller ID (workspace phone id)
  permissions (object, optional)        Workflow-level permission flags

Member role access (memberRoleAccess)
  Array of objects, NOT bare role ids:
  [{"memberRoleId": 6, "accessMode": 1}]
  accessMode is a non-negative bitmask of workflow permissions (see API docs).

Other
  archived (bool)                       Archive flag on update
  dataRecords / tasks / versions        Use dedicated subcommands — not inline here
 _(workspace, write)_
- `gr workflows create-from-template` — Create a workflow from a template _(workspace)_
- `gr workflows delete` — Delete a workflow _(workspace, write)_
- `gr workflows diagram` — Get workflow diagram _(workspace)_
- `gr workflows get` — Get a workflow by ID _(workspace)_
- `gr workflows list` — List workflows _(workspace)_
- `gr workflows notes create` — Create a workflow note _(workspace, write)_
- `gr workflows notes delete` — Delete a workflow note _(workspace, write)_
- `gr workflows notes list` — List workflow notes _(workspace)_
- `gr workflows notes update` — Update a workflow note _(workspace, write)_
- `gr workflows templates` — List workflow templates _(workspace)_
- `gr workflows unarchive` — Unarchive a workflow _(workspace, write)_
- `gr workflows update` — Update a workflow


Field reference for --data JSON (create/update):

Core
  name (string)                         Display name
  description (string, optional)
  defaultPhoneNumberId (number|null)    Outbound caller ID (workspace phone id)
  permissions (object, optional)        Workflow-level permission flags

Member role access (memberRoleAccess)
  Array of objects, NOT bare role ids:
  [{"memberRoleId": 6, "accessMode": 1}]
  accessMode is a non-negative bitmask of workflow permissions (see API docs).

Other
  archived (bool)                       Archive flag on update
  dataRecords / tasks / versions        Use dedicated subcommands — not inline here
 _(workspace, write)_

#### `workspaces`

- `gr workspaces counts` — Get workspace entity counts _(workspace)_
- `gr workspaces create` — Create a workspace _(write)_
- `gr workspaces defaults` — Get workspace defaults _(workspace)_
- `gr workspaces delete` — Delete a workspace _(write)_
- `gr workspaces dms get` — Get messages for a DM chat _(workspace)_
- `gr workspaces dms list` — List direct message chats _(workspace)_
- `gr workspaces dms send` — Send a message in a DM chat _(workspace, write)_
- `gr workspaces export` — Export workspace configuration _(workspace)_
- `gr workspaces features` — List available workspace features
- `gr workspaces get` — Get a workspace by UUID (via --id or -w)
- `gr workspaces import` — Import workspace configuration _(workspace, write)_
- `gr workspaces invitations approve` — Approve a workspace invitation _(workspace, write)_
- `gr workspaces invitations delete` — Delete a workspace invitation _(workspace, write)_
- `gr workspaces invitations deny` — Deny a workspace invitation _(workspace, write)_
- `gr workspaces invitations invite` — Invite a user to the workspace _(workspace, write)_
- `gr workspaces invitations list` — List workspace invitations _(workspace)_
- `gr workspaces list` — List workspaces
- `gr workspaces notifications dismiss-all` — Dismiss all notifications _(workspace, write)_
- `gr workspaces notifications get` — Get a notification by ID _(workspace)_
- `gr workspaces notifications list` — List notifications _(workspace)_
- `gr workspaces notifications mark-read` — Mark a notification as read _(workspace, write)_
- `gr workspaces preview-import` — Preview workspace import _(workspace)_
- `gr workspaces products create` — Create a product _(workspace, write)_
- `gr workspaces products delete` — Delete a product _(workspace, write)_
- `gr workspaces products get` — Get a product by ID
- `gr workspaces products list` — List products _(workspace)_
- `gr workspaces products update` — Update a product _(workspace, write)_
- `gr workspaces scopes` — List workspace scopes
- `gr workspaces themes list` — List workspace themes _(workspace)_
- `gr workspaces themes update` — Update workspace themes _(workspace, write)_
- `gr workspaces update` — Update a workspace _(write)_
- `gr workspaces update-defaults` — Update workspace defaults _(workspace, write)_

_Command index reflects the current `@gravity-rail/cli` npm release. Pin with `npx @gravity-rail/cli@<version>` for reproducible scripts._
