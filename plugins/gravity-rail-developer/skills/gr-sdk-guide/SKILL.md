---
name: gr-sdk-guide
description: Guide for building with @gravity-rail/sdk — authentication, API methods, types, schemas, scopes, and integration patterns.
---

# Gravity Rail SDK Guide

The `@gravity-rail/sdk` package is a TypeScript SDK for building on the Gravity Rail platform — AI assistants, multi-channel communication, workflows, and workspace automation.

## Installation

```bash
npm install @gravity-rail/sdk
# or
yarn add @gravity-rail/sdk
# or
pnpm add @gravity-rail/sdk
```

Optional peer dependency for runtime validation:

```bash
npm install zod
```

## Quick Start

```typescript
import { GravityRailClient } from '@gravity-rail/sdk';

const client = new GravityRailClient(
  process.env.GRAVITY_RAIL_API_KEY,
  'https://api.gravityrail.com'
);

const workflows = await client.getWorkflows(workspaceId);
const chats = await client.getChats(workspaceId);
const member = await client.createWorkspaceMember(workspaceId, {
  first_name: 'Jane',
  last_name: 'Doe',
  email: 'jane@example.com',
  role_id: roleId,
});
```

## Authentication

### API Key

The simplest approach. Create keys in workspace settings or via the SDK:

```typescript
const client = new GravityRailClient(
  'your-api-key',
  'https://api.gravityrail.com'
);
```

Keys can be scoped to specific permissions using the scope system.

### OAuth2 with PKCE

For browser-based applications:

```typescript
const client = new GravityRailClient(undefined, 'https://api.gravityrail.com');

client.setEnhancedAuthHandler(async (authInfo) => {
  // Redirect to login or show auth modal
});
```

### Member Identity Switching

Act on behalf of a specific workspace member:

```typescript
client.setActingMember(memberUuid);
// All subsequent requests use this member's identity
const myChats = await client.getChats(workspaceId);
client.setActingMember(undefined); // reset
```

## Method Naming Conventions

Methods follow predictable patterns:

| Pattern | Example |
|---|---|
| `get<Entity>s(wid)` | `getWorkflows(wid)` — list all |
| `get<Entity>(wid, id)` | `getWorkflow(wid, id)` — get one |
| `create<Entity>(wid, data)` | `createWorkflow(wid, {...})` — create |
| `update<Entity>(wid, id, data)` | `updateWorkflow(wid, id, {...})` — update |
| `delete<Entity>(wid, id)` | `deleteWorkflow(wid, id)` — delete |

Sub-resources add the parent context:

| Pattern | Example |
|---|---|
| `get<Sub>s(wid, parentId)` | `getChatMessages(wid, chatId)` |
| `create<Sub>(wid, parentId, data)` | `createChatLabel(wid, {...})` |

## Domain Overview

The SDK organizes 500+ methods by domain:

| Domain | Representative Methods |
|---|---|
| **Workflows** | `getWorkflows`, `createWorkflow`, `getWorkflowTemplates`, `createWorkflowFromTemplate` |
| **Assistants** | `getAssistants`, `createAssistant`, `createSupervisor` |
| **Agents** | `getAgents`, `createAgent`, `archiveAgent` |
| **Chats** | `getChats`, `getChatMessages`, `sendChatMessage`, `sendAssistantMessage`, `exportChat` |
| **Members** | `getWorkspaceMembers`, `createWorkspaceMember`, `getMemberLabels` |
| **Data Types** | `getDataTypes`, `createDataType`, `getDataRecords`, `createDataRecord`, `upsertDataRecord` |
| **Events** | `getEventRules`, `createEventRule`, `createEvent`, `runEvent` |
| **Calendars** | `getCalendars`, `createCalendarEvent`, `getAvailableSlots`, `linkCalendarToGoogle` |
| **Files** | `getFiles`, `createFolder`, `createFile`, `generateFileUploadUrl` |
| **Sites** | `getSites`, `createSite`, `createPage`, `crawlSite` |
| **Communications** | `getPhoneNumbers`, `initiateCall`, `getInboxes`, `getInboxThreads` |
| **Toolkits** | `getCustomToolkits`, `createCustomTool`, `createMcpServer`, `getMcpServerTools` |
| **Operator Groups** | `getOperatorGroups`, `createOperatorGroup`, `getLiveOperators` |
| **Qualifications** | `getQualifications`, `createQualification`, `assignQualification`, `submitForReview` |
| **Billing** | `getApiKeys`, `createApiKey`, `getSubscriptions` |
| **Integrations** | `getDiscordBots`, `getSlackApps`, `getFhirConnections` |
| **Workspaces** | `getWorkspace`, `exportWorkspace`, `importWorkspace`, `createClientWorkspace` |

## Type System

Full TypeScript types for every API entity:

```typescript
import type {
  Workspace, Member, Chat, Task, Workflow, Assistant,
  DataType, DataRecord, EventRule, Calendar, Site,
  PhoneNumber, Inbox, OperatorGroup, Qualification,
  Subscription, ApiKey, Agent, MemberRole,
} from '@gravity-rail/sdk';
```

Types are exported from the main entry point — no separate imports needed.

## Zod Schemas

Runtime validation schemas are available via a separate import (requires `zod` as a peer dependency). Schemas use PascalCase naming:

```typescript
import { MemberSchema, TaskFormDataSchema, WorkflowFormDataSchema } from '@gravity-rail/sdk/schemas';

// Parse API responses
const member = MemberSchema.parse(apiResponse);

// Validate form data
const result = TaskFormDataSchema.safeParse(formData);
if (!result.success) {
  console.error(result.error.flatten());
}
```

## Scopes & Permissions

The SDK exports the complete scope system for building OAuth apps and managing API key permissions:

```typescript
import { SCOPES, ScopeCategory, ScopeManager, SCOPE_PRESETS } from '@gravity-rail/sdk';

// Browse available scopes by category
const chatScopes = Object.values(SCOPES).filter(
  (s) => s.category === ScopeCategory.CHATS
);

// 22 scope categories: workspace, members, chats, DMs, assistants,
// workflows, assignments, automations, data types, records, files,
// sites, agents, calendars, inboxes, phones, webhooks, apps, labels,
// analytics, operator, org
```

## Error Handling

```typescript
import { GravityRailClient, ApiError } from '@gravity-rail/sdk';

try {
  const task = await client.getTask(workspaceId, taskId);
} catch (error) {
  if (error instanceof ApiError) {
    console.error(`${error.status}: ${error.message}`);
    // 403 → check API key scopes
    // 404 → verify workspace and entity IDs
    // 429 → rate limited, back off
  }
}
```

## Common Integration Patterns

### Webhook-driven automation

```typescript
// Create an event rule that triggers on chat creation
await client.createEventRule(workspaceId, {
  name: 'New Chat Handler',
  trigger: 'chat.created',
  condition: '"vip" in member.labels',
  actions: [{
    type: 'send_ai_message',
    config: { message: 'Welcome, VIP!' },
  }],
});
```

### Polling for new data

```typescript
// Poll for new chats periodically
setInterval(async () => {
  const chats = await client.getChats(workspaceId);
  const recent = chats.filter(c =>
    new Date(c.created_at) > lastCheck
  );
  // Process new chats...
  lastCheck = new Date();
}, 30_000);
```

### White-label workspace provisioning

```typescript
// Create and provision a client workspace
const clientWs = await client.createClientWorkspace(workspaceId, {
  name: 'Acme Corp',
  product_id: productId,
});
await client.provisionClientWorkspace(workspaceId, clientWs.id);

// Export config from template workspace and import
const config = await client.exportWorkspace(templateWorkspaceId);
await client.importWorkspace(clientWs.id, config);
```

### Structured data collection

```typescript
// Define a schema — AI assistants collect this conversationally
const dataType = await client.createDataType(workspaceId, {
  name: 'Intake Form',
  slug: 'intake-form',
  is_collection: true,
  fields: [
    { name: 'Full Name', field_type: 'text', required: true },
    { name: 'Email', field_type: 'email', required: true },
    { name: 'Priority', field_type: 'dropdown', options: ['Low', 'Medium', 'High'] },
  ],
});

// Upsert records (match on a unique field)
await client.upsertDataRecord(workspaceId, dataType.id, {
  match_field: 'Email',
  field_values: { 'Email': 'alice@example.com', 'Priority': 'High' },
});
```

## Method Discovery

The SDK client is fully typed. Use your editor's autocomplete on `client.` to browse all available methods. Methods are organized by domain prefix:

- `client.get*` — list or get entities
- `client.create*` — create new entities
- `client.update*` — update existing entities
- `client.delete*` — delete entities

For the complete API reference, see the [developer documentation](https://developer.gravityrail.com).

## Related

- [`@gravity-rail/cli`](https://www.npmjs.com/package/@gravity-rail/cli) — Command-line interface for interactive workspace management
- [Developer Docs](https://developer.gravityrail.com) — Full API reference and guides
