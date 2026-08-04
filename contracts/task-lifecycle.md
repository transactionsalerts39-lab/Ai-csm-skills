# Customer Task Lifecycle Contract — V5

This is the canonical task contract for Update Notes, Meeting Summarizer, Weekly Command Centre, Customer Task Centre, Account Follow-Up Builder, and Meeting Prep. If a skill conflicts with this file, this file wins.

## Task Sources

- Airtable Customer Tasks is authoritative for customer/account work.
- Customer Task Centre is Airtable-only. It must not read, display, deduplicate, create, close, reopen, or update Notion tasks.
- Apply `schema/notion-task-map.md` only when the routed canonical skill explicitly authorizes Notion task access.
- Notion `6 sense → Projects & tasks` remains authoritative for high-priority internal, manager, admin, enablement, AI/OKR, and project work in those explicitly Notion-aware workflows.
- Do not create, close, reopen, or synchronize a second copy merely because a similar task appears in another system.
- A source-aware write updates only the authoritative record unless Ranjodh explicitly asks to sync both and the routed workflow authorizes both systems.
- If a Notion-only customer task is explicitly promoted to Airtable in a Notion-aware workflow, deduplicate first and retain the Notion URL as source context.

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

When showing several tasks, number the rows or task references for convenience. A user may say `mark 1.2 done` within that conversation. Resolve the reference to the underlying Airtable record ID internally.

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

When similarity is uncertain, do not merge or close automatically.

- If several plausible records remain, ask one clarification before any write.
- If one record is the clear candidate but identity/completion evidence is still insufficient, a
  write-authorized ingestion workflow may set the separate Needs Review checkbox and leave the
  lifecycle Status active. In a read-only workflow, recommend that flag without writing it.
- Do not change lifecycle Status to `Needs Review` merely as a substitute for resolving multiple
  record matches.

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

Set Accounts → Task status with this precedence:

1. `closed` when no linked task is active and the exact option exists.
2. Otherwise, `yet to start` only when the exact option exists, every active task has Status `Open`,
   and no active task has Completion Evidence or Completed Date.
3. Otherwise, `Open`, including when any linked task is In Progress, waiting, Blocked, or Needs
   Review.

`Waiting on Customer` remains active. Never create a select option implicitly.
