# Notion Customer Plans Source Map

Use this file only for workflows that explicitly authorize the Notion customer-planning workspace.

This map governs durable account strategy and customer-safe success plans. It does **not** change the task-source boundary: Airtable Customer Tasks remains authoritative for customer/account work, ownership, due dates, lifecycle state, waiting state, and completion evidence.

## Canonical Hierarchy

- Workspace hub: `6 sense`
- Workspace hub page ID: `2e6ecca2-ea5e-801e-a035-c3ef35345a3e`
- Workspace hub URL: `https://app.notion.com/p/2e6ecca2ea5e801ea035c3ef35345a3e`
- Customer-planning hub: `Customer Plans`
- Customer-planning hub page ID: `3a6ecca2-ea5e-8126-91c1-e4585168484a`
- Customer-planning hub URL: `https://app.notion.com/p/3a6ecca2ea5e812691c1e4585168484a`

Required structure:

```text
6 sense
└── Customer Plans
    └── [Exact Account Name]
        ├── Internal Adoption Strategy — Current
        └── Customer-Safe Adoption Plan — Current
```

The `Customer Plans` page is the customer hub/tab under the `6 sense` mega parent page. Every customer must have one account parent page directly beneath this hub.

## Account Parent Resolution

Before creating or updating a customer plan:

1. Fetch the canonical `Customer Plans` hub by exact page ID.
2. Search within that page for the exact Airtable Account Name.
3. Prefer an exact title match among direct child pages.
4. A case, punctuation, suffix, or abbreviation difference is not automatically the same account. Use Airtable account identity and available public-domain evidence to normalize only when one match is strong.
5. If several plausible customer parents remain, ask one concise clarification before writing.
6. Never use a same-named page found elsewhere in the workspace as the destination.
7. If no account parent exists and the user explicitly authorized publication, create one direct child page titled with the exact Airtable Account Name.

## Account Parent Content

The account parent is a durable navigation hub, not the complete strategy document.

It should contain:

- A one-sentence description: `Customer planning hub for [Account].`
- A short `Current focus` section with the active strategic themes.
- A `Plan status` section with the current phase and whether customer alignment is pending.
- Direct child pages for the internal and customer-safe current plans.

Do not place private renewal risk, churn commentary, unsupported stakeholder conclusions, or detailed execution analysis on the parent page.

## Current Plan Pages

Use these exact titles:

- `Internal Adoption Strategy — Current`
- `Customer-Safe Adoption Plan — Current`

Rules:

- Maintain no more than one current internal page and one current customer-safe page per account.
- Search and fetch before creating.
- If the exact current page exists, update it in place rather than creating another page.
- Fetch the existing page before an update and preserve any child pages, databases, attachments, or references.
- Use targeted updates when practical. Use full replacement only when the plan is intentionally being regenerated and no child content would be lost.
- Use Notion's page history as the default revision history.
- Create a dated snapshot or `Previous Versions` child only when the user explicitly asks to archive or version the plan.

## Internal Plan Boundary

The internal plan may contain:

- Account metadata and renewal context
- Adoption and engagement gaps
- Internal relationship or stakeholder gaps
- Evidence confidence and contradictions
- Potential PS route
- Internal ownership and dependencies
- Proposed Airtable Customer Task changes
- Commercial or risk context supported by authorized evidence

It must label public research and inference clearly and must not claim customer confirmation without supporting evidence.

## Customer-Safe Plan Boundary

The customer-safe page may contain:

- Shared or proposed business objectives
- Agreed or proposed use cases
- Workstreams and milestones
- Confirmed participants, or role placeholders such as `Owner to confirm`
- 30/60/90-day outcomes
- Success measures
- Dependencies and inputs to confirm
- Working cadence
- Verified customer-safe resources

It must exclude:

- ACV, renewal forecast, churn/downsell risk, private sentiment, or internal commercial strategy
- Internal-only stakeholder judgments
- Externally inferred decision authority presented as fact
- Private notes, unsupported conclusions, or confidential strategy
- PS routing codes or internal escalation language unless explicitly made customer-safe

## Task and Execution Boundary

- Notion stores strategy, rationale, milestones, measurement, and customer-safe collaboration material.
- Airtable Customer Tasks stores executable customer/account actions.
- A Notion checklist or roadmap is explanatory only and must not be treated as the authoritative task lifecycle.
- Publishing a plan does not create, update, close, cancel, or reopen Airtable Customer Tasks.
- Proposed task changes remain preview-only until a separate Airtable-authorized workflow receives an explicit action request.
- Never create automatic bidirectional task synchronization.

## Publication Idempotency

Before every Notion write:

1. Resolve the exact account parent.
2. Resolve the exact current child page title.
3. Fetch the existing destination.
4. Compare the planned content with the current content.
5. Skip a normalized no-change update.
6. Update one exact destination only.
7. Fetch the account parent and changed child page after writing.
8. Confirm the final hierarchy and return the observed page URLs.

If a write partially succeeds, report every created or updated page and every missing or failed page. Do not claim full publication until the expected hierarchy is verified.

## Refresh Rules

A refresh should update the two current pages in place and preserve the account parent.

The internal page should record material movement under these categories when relevant:

- Validated assumptions
- Changed priorities
- New stakeholders
- New blockers or dependencies
- Use-case stage movement
- Adoption or measurement movement
- Recommended Airtable task changes

If no material change exists, leave the current page unchanged and report `No material plan change found.`

## Write Confirmation

After a successful publication or refresh, report:

- Notion workspace updated
- Customer account parent
- Pages created, updated, or unchanged
- Internal plan URL
- Customer-safe plan URL
- Any ambiguity or failure
- Explicit statement that Airtable Customer Tasks were not changed
