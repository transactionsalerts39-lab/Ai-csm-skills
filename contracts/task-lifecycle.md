# Customer Task Lifecycle Contract — V4

This is the canonical task contract for Update Notes, Meeting Summarizer, Weekly Command Centre, Customer Task Centre, and Meeting Prep. If a skill conflicts with this file, this file wins.

## Cross-System Task Sources

Apply `schema/notion-task-map.md` when a workflow reads the 6sense Notion task page.

- Airtable Customer Tasks remains authoritative for customer/account work.
- Notion `6 sense → Projects & tasks` is authoritative for high-priority internal, manager, admin, enablement, AI/OKR, and project work.
- Unified views may rank both sources together, but must preserve source ownership.
- Do not create, close, reopen, or synchronize a second copy merely because a similar task appears in both systems.
- A source-aware write updates only the authoritative record unless Ranjodh explicitly asks to sync both.
- If a Notion-only customer task is promoted to Airtable, deduplicate first and retain the Notion URL as source context.

## Active and Closed States

Active states:

- `Open`
- `In Progress`
- `Waiting on Internal Team`
- `Waiting on Customer`
- `Blocked`
- `Needs Review`

Closed states:

- `Done`
- `Cancelled`

A linked account remains task-open while any linked task is in an active state, including `Waiting on Customer`.

## User Experience and Record Identity

Use Airtable record IDs internally for exact updates. Never require Ranjodh to remember or type a record ID.

Accept natural language such as:

- `Sandler is done`
- `Close the SGU primary admin task`
- `The Kentik email was sent`
- `Reopen the Pivot follow-up`

When showing several tasks, number the rows for convenience. A user may say `mark number 3 done` within that conversation. Resolve the number to the underlying Airtable record ID internally.

If one strong match exists, act on it. If multiple plausible matches exist, ask one short clarification question before writing.

## Creation and Deduplication

Create a task only for a concrete action with a reasonably clear account and owner.

Before creating, search active tasks for the same account and compare:

1. Normalized action/object
2. Owner
3. Due date or timing
4. Source context
5. Existing Airtable record ID

Reprocessing the same source must update the existing task, not create a duplicate.

Do not use status as part of semantic identity. A task remains the same task when it moves between active states.

When similarity is uncertain, do not merge or close automatically. Mark the best candidate `Needs Review` or ask for clarification.

## Completion Gates

Auto-close a task only when all are true:

1. The account matches.
2. One specific active task matches strongly.
3. The source describes the same action/object.
4. Completion is in the past or present-perfect tense.
5. The actor is the task owner or an authorized actor completing it.
6. The statement is not negated, conditional, future-looking, quoted history, or merely planned.
7. The work is fully complete rather than partially complete.
8. Evidence can be summarized clearly.

Words such as `sent`, `shared`, `confirmed`, `scheduled`, and `resolved` are evidence only when all gates pass.

Do not auto-close from:

- `will send`
- `drafted`
- `planning to`
- `working on it`
- `not yet sent`
- `I confirmed that I will send it`
- a quoted or forwarded older email
- another person's unrelated completion
- partial completion

If completion is plausible but uncertain, leave the task active and set `Needs Review`.

## State Transitions

Allowed normal transitions:

- `Open` → `In Progress`, either waiting state, `Blocked`, `Needs Review`, `Done`, or `Cancelled`
- `In Progress` → either waiting state, `Blocked`, `Needs Review`, `Done`, or `Cancelled`
- `Waiting on Internal Team` → `In Progress`, `Blocked`, `Needs Review`, `Done`, or `Cancelled`
- `Waiting on Customer` → `In Progress`, `Needs Review`, `Done`, or `Cancelled`
- `Blocked` → `In Progress`, either waiting state, `Needs Review`, `Done`, or `Cancelled`
- `Needs Review` → any state after the ambiguity is resolved

Reopen `Done` or `Cancelled` only when Ranjodh explicitly requests reopening or new evidence clearly proves that additional work remains. Record the reopening reason in Source Summary or Completion Evidence and clear Completed Date when supported.

## Waiting Semantics

- `Customer Waiting?` means the customer is waiting on Ranjodh, Support, AE/AM, Shared, or another internal owner.
- `Waiting on Customer` means 6sense is waiting for the customer.
- Do not treat those two concepts as interchangeable.

## Rollup

Set Accounts → Task status:

- `Open` when any linked task is active, including `Waiting on Customer`.
- `closed` when no linked task is active.
- Use `yet to start` only when the exact Airtable option exists and every active task is genuinely unstarted.
- Never create a select option implicitly.
