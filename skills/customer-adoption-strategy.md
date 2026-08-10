---
name: customer-adoption-strategy
description: >
  Build an evidence-based adoption and engagement strategy for one customer account by combining
  Airtable account history, meeting evidence, Customer Tasks, entitlements, public company and
  website research, verified stakeholder research, and official 6sense resources. Classify the
  account motion, prioritize no more than two active use cases, create a measurable 30/60/90-day
  plan, produce separate internal and customer-safe versions, and conditionally publish the plan
  under the canonical Notion Customer Plans hierarchy. Use for /account strategy, /adoption plan,
  /customer adoption plan, /success plan, /use case plan, /refresh adoption plan, or
  /publish adoption plan. Default to Read/Draft; Notion writes require an explicit current-turn
  save, publish, push, or update request. V1 never writes Airtable Customer Tasks.
---

# Customer Adoption Strategy — V1

## Purpose

Create a specific, executable account plan that answers:

1. What does the customer sell, who do they sell to, and how do they go to market?
2. What business outcomes and use cases has the customer actually expressed?
3. What 6sense capabilities do they own, have configured, use repeatedly, and derive value from?
4. Is the account primarily Marketing/ABM-led, Sales Intelligence-led, Data/Operations-led, Hybrid, or Foundation-first?
5. Which one or two use cases should be active now, which use case should be deferred, and why?
6. Who owns each workstream, what prerequisites remain, and how will adoption and business movement be measured?
7. What should be stored internally, what can be shared with the customer, and what should become an Airtable Customer Task later?

This workflow fills the strategic layer between Meeting Summarizer, Meeting Prep, Docs, PS Adoption Audit, and Customer Task Centre. It is not a generic company dossier, feature catalogue, renewal memo, or task-management replacement.

---

## Shared Contracts and Schemas

Before running this workflow, apply:

- `contracts/tool-access-safety.md` for connector authorization and narrowest-sufficient access.
- `contracts/write-safety.md` for Read/Draft/Conditional Write boundaries and write confirmation.
- `contracts/task-lifecycle.md` for active task states, matching, deduplication, and completion semantics.
- `contracts/fiscal-calendar.md` for renewal-quarter and relative-date calculations.
- `contracts/untrusted-input.md` for transcripts, emails, screenshots, files, websites, and external content.
- `schema/airtable-schema-map.md` for Airtable sources, fields, IDs, and allowed values.
- `schema/notion-customer-plans-map.md` for the canonical customer hub, page hierarchy, customer-safe boundary, and publication idempotency.

Supporting domain rules:

- Use `skills/docs-assistant.md` for official 6sense source routing and confidence rules.
- Use the PS ownership boundaries and route catalogue in `skills/ps-adoption-audit.md` when a recommended use case may require Professional Services.

Supporting skills contribute domain rules only. They do not expand this workflow's connector permissions.

If this skill conflicts with a shared contract or schema, the contract or schema wins.

---

## Triggers

Route here for exact aliases and clear natural-language intent such as:

- `/account strategy [Account]`
- `/adoption plan [Account]`
- `/customer adoption plan [Account]`
- `/success plan [Account]`
- `/use case plan [Account]`
- `/refresh adoption plan [Account]`
- `/publish adoption plan [Account]`
- `Research [Account] and build a 30/60/90-day adoption plan.`
- `Turn the latest [Account] meeting into an adoption strategy.`
- `Build an internal strategy and customer-safe success plan for [Account].`
- `Refresh the existing [Account] plan using the latest meeting.`
- `Push the approved [Account] plan into Notion.`

Collision boundaries:

- `/meeting strategy [Account]` remains Customer Meeting Prep.
- `/use cases` remains Docs / RevCity / 6sense.com Assistant.
- Exact `/use case plan [Account]` routes here.
- Generic `strategy`, `plan`, or `customer plan` is not enough when the intended workflow is unclear.
- Portfolio-wide PS candidate identification remains PS Adoption Audit.
- Customer Task creation, completion, cancellation, reopening, or status changes remain Customer Task Centre.

---

## Scope

V1 is optimized for one named customer account per plan.

- If the account name is missing, ask: `Which customer account should I build the adoption strategy for?`
- If one Airtable account strongly matches, use it.
- If multiple plausible Airtable records remain, ask one concise clarification before continuing or writing.
- A named account outside Ranjodh's current portfolio may be used only when the user explicitly names it.
- Use Asia/Kolkata for relative dates. Preserve the customer's timezone when quoting customer deadlines or meeting times.

---

## Mode and Write Boundary

### Default mode — Read / Draft

The default run may:

- Read the exact Airtable account, linked Detailed Notes, and linked Customer Tasks.
- Read the exact account subtree under Notion Customer Plans when one exists.
- Research public company, website, market, and people information.
- Verify named professional profiles.
- Research official 6sense Support Docs, RevCity, and 6sense.com resources.
- Draft an internal adoption strategy.
- Draft a customer-safe adoption plan.
- Recommend Airtable Customer Task changes as a preview.

The default run must not:

- Create or update Notion.
- Create, update, close, cancel, or reopen Airtable Customer Tasks.
- Update Airtable account fields, notes, cadence, risk, entitlements, or renewal data.
- Send email, schedule meetings, post Slack/Teams messages, submit a PS request, or update Salesforce, Clari, Gainsight, or another customer system.

### Conditional Notion write

A current-turn request containing an explicit action such as `save`, `publish`, `push to Notion`, `update in Notion`, or `refresh and save` authorizes only the Notion plan publication defined in `schema/notion-customer-plans-map.md`.

Examples:

- `/adoption plan Jacquard` → preview only.
- `/adoption plan Jacquard save to Notion` → build the plan and publish the exact Notion hierarchy.
- `/refresh adoption plan Jacquard` → refresh preview only.
- `/refresh adoption plan Jacquard and save` → compare, update, and verify the current Notion pages.
- `/publish adoption plan Jacquard` → complete the evidence-backed plan, then publish it to Notion.

A Notion publication does not authorize Airtable writes.

### Airtable task boundary

V1 may show proposed task changes, including likely updates to existing tasks, but never applies them.

If the user explicitly asks to create or update the approved tasks, route that action through Customer Task Centre using Airtable as the authoritative system. Do not create task checkboxes in Notion as a substitute.

---

## Approved Connector Sources

This top-level skill explicitly authorizes the following narrow source set.

### Airtable — read only

Approved scope:

- Exact named Accounts record
- Linked Detailed Notes
- Linked Customer Tasks
- Relevant account metadata defined in `schema/airtable-schema-map.md`

Do not query unrelated accounts except what is minimally required to disambiguate the account name.

### Exa — public company and people discovery

Approved scope:

- Company website and official public pages
- Public company, product, solution, industry, customer, integration, resource, leadership, hiring, and announcement pages
- Public professional-profile discovery for relevant named stakeholders
- Recent public business changes that materially affect the account strategy

Open and verify the actual pages used. Do not rely only on search snippets.

### LinkedIn — named profile validation

Approved scope:

- Validate a person already identified through Airtable, meeting evidence, the company website, or Exa
- Confirm current title, company, and location when available

Do not treat a LinkedIn title as proof that a person is a decision-maker, champion, executive sponsor, or current customer contact.

### Official 6sense resources — read only

Approved scope:

- Official Support Docs for product behaviour, setup, permissions, integrations, expected functionality, and customer-safe implementation guidance
- RevCity Product Updates for availability, beta, release, and launch context
- RevCity community, enablement, and playbook content for examples and ideas only
- Official 6sense.com research, guides, blueprints, positioning, and customer stories

Always open the actual resource before making a claim.

### Notion Customer Plans — narrow read; conditional write

Approved scope:

- Canonical `6 sense → Customer Plans` hub
- Exact named account parent beneath that hub
- `Internal Adoption Strategy — Current`
- `Customer-Safe Adoption Plan — Current`

Do not use broad workspace search as a substitute for the canonical hierarchy unless the exact configured hub cannot be fetched. Do not read personal Notion pages or the unrelated personal `Tasks` page.

### Denied by default

Do not access Gmail, Google Calendar, Google Drive, Slack, Teams, Salesforce, Clari, call-recording systems, or another connected application unless the user's current message separately and explicitly requests that system and operation.

A request for `latest context`, `meeting evidence`, `email`, or `follow-up` does not implicitly authorize those systems.

---

## Evidence Model

Every material conclusion must be traceable to one of these labels:

- **Customer confirmed** — explicitly stated in the supplied transcript, meeting notes, or another trusted current customer interaction.
- **Internal record** — supported by Airtable account metadata, Detailed Notes, or Customer Tasks.
- **Official 6sense confirmed** — supported by a verified Support Doc or official product update.
- **Publicly observed** — visible on the customer's official website, verified public profile, filing, announcement, job posting, or other public source.
- **Inferred — validate** — a reasoned hypothesis derived from evidence but not confirmed by the customer.
- **Not captured / needs validation** — material information is missing or conflicting.

Do not silently merge these categories.

### Evidence priority

Use this order for account-specific truth:

1. Current-turn transcript, meeting notes, or user-provided source
2. Dated Airtable Detailed Notes
3. Active Airtable Customer Tasks and relevant recently completed tasks
4. Dated entries in Accounts → Activity notes
5. Airtable account metadata for entitlement, renewal, risk, cadence, and commercial context
6. Existing Notion Customer Plans as prior internal hypotheses and agreed-plan history
7. Public company and people research

Last Activity Date is a retrieval signal, not proof of adoption movement or customer engagement.

### Freshness

- 0–30 days: Current
- 31–60 days: Aging
- More than 60 days: Historical unless a still-active task or unresolved commitment carries it forward
- Undated: Freshness unverified

Stale or public evidence alone cannot establish current customer readiness, product usage, entitlement, or ownership.

---

## Workflow

### Step 1 — Resolve the account

1. Extract the account name.
2. Search Airtable Accounts by Account Name.
3. Resolve one exact or strong match and retain the Airtable record ID internally.
4. Pull the public company domain only after account identity is stable.
5. If several plausible company domains or Airtable records remain, ask one concise clarification.

### Step 2 — Check for an existing Notion plan

Read `schema/notion-customer-plans-map.md` and:

1. Fetch the canonical Customer Plans hub.
2. Search only within that hub for the exact account parent.
3. Fetch the existing internal and customer-safe current pages when present.
4. Treat existing plans as prior strategy state, not fresh customer confirmation.
5. Note whether the run is a first plan, refresh, or no-change review.

A draft run may read the existing plan. It must not update it without explicit publication intent.

### Step 3 — Pull Airtable account context

Retrieve from Accounts:

- Account Name and internal record ID
- Activity notes
- Engagement Status and Outreach Step
- Meeting Sync established and Cadence Frequency
- ACV and Renewal Date
- Churn Risk and renewal-risk fields when populated
- Renewal Forecast Category and expected renewal values when populated
- Entitlements and Upsell opportunity when populated
- Last Clari Sync Date when relevant
- Last Activity Date as a retrieval signal
- Task status
- Current Active CSM

Retrieve linked Customer Tasks:

- Every active task: Open, In Progress, Waiting on Internal Team, Waiting on Customer, Blocked, and Needs Review
- Recently completed tasks from the prior 30–60 days when they explain current adoption state
- Customer Waiting? items
- P1 and P2 items
- Support, implementation, enablement, reporting, integration, renewal, and stakeholder tasks

Retrieve linked Detailed Notes:

- The 10–15 most recent relevant records when available
- Meeting Notes, transcripts, account updates, support records, QBR/EBR notes, and adoption-related records
- Older records only when they explain current use cases, historical adoption, stakeholder changes, product configuration, prior experiments, value evidence, or unresolved risk

Do not dump raw records. Extract patterns, decisions, open loops, evidence dates, and ownership.

### Step 4 — Establish the meeting-evidence baseline

Use the most relevant current meeting evidence, not merely the newest timestamp.

Extract:

- Customer-stated objectives
- Current workflows and products mentioned
- Existing 6sense use
- Adoption or value blockers
- Requested use cases
- Success criteria
- Stakeholders and roles
- Agreed commitments
- Open questions

At the start of the output, state:

```text
Meeting evidence used:
- [Account] — [meeting title/date]
- Source: [current-turn transcript / Airtable Detailed Notes / other authorized source]
- Evidence depth: [raw transcript / structured notes / limited account history]
```

If no substantive meeting evidence exists, continue with an `INCOMPLETE` strategy, emphasize validation questions, and do not present inferred priorities as customer confirmed.

### Step 5 — Research the company and go-to-market motion

Use Exa to answer only strategy-relevant questions.

Research:

1. Core products, services, solutions, and recent launches
2. Customer segments, industries, company sizes, and geographies
3. Enterprise, transactional, PLG, channel, partner, outbound, marketing-led, or hybrid GTM signals
4. Buyer problems and likely buying roles
5. Website journey from education to evaluation and commercial action
6. Content, customer stories, integrations, and ecosystem positioning
7. Recent leadership, hiring, acquisition, funding, market, or product changes that could affect GTM

Prioritize official company pages and current primary sources. Use third-party material only when it adds necessary context and label it accordingly.

Do not produce a generic company history. Every finding must connect to a potential adoption decision, stakeholder, signal, use case, prerequisite, or measurement plan.

### Step 6 — Build the website-signal hypothesis

Map visible website behaviours into a tentative journey:

```text
Early education → Solution or industry research → Evaluation or proof → Commercial action
```

For each tier, identify:

- Example pages or behaviours
- What the behaviour may indicate
- Appropriate marketing action
- Appropriate seller action
- Caveats or exclusions

This is a hypothesis for customer validation. Public website structure does not prove that WebTag is implemented, that the pages are currently tracked, or that the customer agrees with the interpretation.

### Step 7 — Discover and validate stakeholders

Use current internal evidence first, then Exa discovery, then LinkedIn validation for identified names.

Classify each person as:

- **Known stakeholder** — appears in customer meetings, Airtable, or current user-provided evidence
- **Externally identified stakeholder** — verified current employee; relationship with 6sense is unknown
- **Likely role — validate** — title suggests relevance but authority or ownership is not confirmed
- **Missing role** — a necessary function is not represented

Map stakeholders to:

- Executive sponsor
- Business/use-case owner
- Operational owner
- Platform or technical administrator
- Sales-adoption owner
- Marketing-adoption owner
- Analytics or measurement owner
- End-user leader

Never claim that an externally identified employee is a decision-maker, sponsor, champion, or approver without supporting account evidence.

### Step 8 — Build the adoption baseline

For every relevant 6sense capability, separate these stages:

1. **Entitled** — product or access is contractually available
2. **Configured** — required integration, permissions, setup, and data are in place
3. **Activated** — at least one user or team is using it
4. **Embedded** — it is part of a repeatable operating workflow
5. **Value demonstrated** — observable adoption, operational, or business evidence exists

Allowed stage labels:

- Not verified
- Entitled
- Configured
- Activated
- Embedded
- Value demonstrated
- Blocked
- Not applicable

Never infer entitlement from public product interest, a draft plan, or a feature mention. Never infer adoption from configuration alone. Never infer value from attendance or training completion alone.

Required table:

```text
Current Adoption Baseline
| Capability / Use Case | Current Stage | Evidence | Gap to Next Stage | Confidence |
|---|---|---|---|---|
```

### Step 9 — Classify the account motion

Assess the account across:

- Marketing/ABM maturity
- Seller/SI maturity
- Data and measurement readiness
- Customer engagement
- Stakeholder coverage

Assign one primary archetype:

- **Marketing / ABM-led** — audience, intent, campaigns, paid media, 6QA, AI Email, or marketing orchestration is the principal motion.
- **Sales Intelligence-led** — seller prioritization, SDR/BDR workflows, CRM prospecting, contact discovery, alerts, and manager reinforcement is the principal motion.
- **Data / Operations-led** — matching, model, Data Workflows, integration architecture, reporting, API, or operational governance is the principal motion.
- **Hybrid** — Marketing and Sales must operate one connected signal-to-action motion.
- **Foundation-first** — ICP, integrations, data, ownership, tracking, entitlement, or measurement must be fixed before another activation use case is credible.

Include confidence and evidence. Do not force an ABM-versus-SI binary.

### Step 10 — Map needs to verified 6sense capabilities

For each candidate recommendation, show:

- Customer problem
- Desired business or operational outcome
- Supporting account evidence
- Relevant 6sense capability
- Current adoption stage
- Entitlement status
- Configuration and readiness prerequisites
- Customer owner
- Ranjodh / 6sense owner
- Customer-safe official resources
- Adoption measure
- Business measure
- Confidence and open questions

Use source routing from Docs Assistant:

- Support Docs confirm product behaviour and implementation.
- RevCity Product Updates confirm release and availability language.
- RevCity community/playbooks suggest examples only.
- 6sense.com supports strategy, research, guides, positioning, and customer stories.

Do not overpromise product availability, beta access, roadmap timing, entitlements, tenant configuration, or results.

### Step 11 — Generate and prioritize use cases

Score each candidate from 0–2 on:

1. Business relevance
2. Customer evidence
3. Entitlement and technical feasibility
4. Stakeholder and execution readiness
5. Time to value
6. Measurability

Maximum: 12.

Interpretation:

- 10–12: Primary candidate if readiness gates pass
- 7–9: Secondary candidate or prerequisite-first motion
- 4–6: Validate before activation
- 0–3: Exclude from the active plan

Scoring ranks ideas but never overrides missing entitlement, missing ownership, a Support/Product defect, or an unverified customer objective.

Plan limits:

- Exactly one Primary use case when evidence supports one
- No more than one Secondary active use case
- No more than one Future / Deferred use case
- Additional ideas belong in `Later opportunities` only when materially useful

A good strategy states what should not be done yet.

### Step 12 — Apply ownership boundaries

Keep work with the correct owner:

- Support/Product — break/fix, outage, defect, data-refresh failure, product limitation, or tenant-specific investigation
- Commercial/Deal Desk/Finance/Legal — pricing, renewal terms, invoicing, contract, procurement, or legal negotiation
- CSM — cadence, ordinary follow-up, basic documentation, coordination, and relationship management
- PS route, when qualified:
  - RMC — intent, segments, audiences, AI Email, marketing workflows, campaign strategy or activation
  - SIC — Sales Intelligence configuration, training, seller/SDR adoption, and repeatable prospecting workflow
  - RTC — model efficacy, matching, Data Workflows, MarTech architecture, migration, or technical scoping
  - VA — reporting, Reporting API, Data Packs, standard/custom reports, and data ingestion

A potential PS route does not prove entitlement, capacity, approval, or submission. Keep it in the internal plan only unless the wording is made customer-safe.

### Step 13 — Build the execution plan

Default to a 90-day plan with:

- 0–30 days — baseline, ownership, prerequisites, design, and first configuration
- 31–60 days — active pilot or workflow execution
- 61–90 days — measurement, optimization, value readout, and scale/no-scale decision

For every phase show:

- Work
- Owner
- Output
- Success evidence
- Dependencies

If renewal is fewer than 90 days away, compress the plan and explicitly show the shortened validation window. Do not propose a 120-day plan that ends too close to or after renewal without flagging the problem.

### Step 14 — Define measurement at three levels

#### Product adoption

Examples:

- Active users
- Days active
- Account, alert, or workflow interactions
- Segments/workflows tied to an agreed use case
- Reports consumed
- Repeated weekly behaviour

#### Customer engagement

Examples:

- Recurring cadence established
- Named sponsor, business owner, administrator, and end-user owner
- Working-session attendance
- Action completion
- Unowned dependencies
- Time to decision or response

#### Business movement

Examples:

- Qualified accounts acted on
- Meetings generated
- Opportunities created or accelerated
- Website or intent-qualified account progression
- Campaign engagement
- Pipeline generated or influenced
- Time saved or workflow efficiency

Use only metrics the customer can access and verify. If no baseline exists, make baseline capture the first milestone. Do not invent hard targets from generic benchmarks.

### Step 15 — Cross-check existing Airtable Customer Tasks

Before proposing new work:

1. List every active Airtable Customer Task linked to the account.
2. Compare each proposed action with the active tasks using account, normalized action/object, owner, due date/timing, and source context.
3. Recommend updating a strong existing match rather than creating a duplicate.
4. Mark uncertain matches as `Needs task review` in the preview.
5. Keep Waiting on Customer active.
6. Never close a task based on the plan, a draft, future intent, or public research.

Required preview categories:

- Existing execution items to preserve
- Existing tasks to refine
- Proposed new tasks
- Likely duplicates to avoid

No Airtable change occurs in V1.

### Step 16 — Produce separate internal and customer-safe plans

#### Internal plan

May include:

- Account metadata and renewal context
- Current value gap and adoption risk
- Evidence labels and contradictions
- Company/GTM research
- Adoption baseline
- Motion classification
- Stakeholder and relationship map
- Use-case scoring
- Internal owners and dependencies
- Potential PS route
- Proposed Airtable task changes
- Validation gaps

#### Customer-safe plan

Include only:

- Shared or proposed objectives
- Agreed or proposed workstreams
- Confirmed owners or `Owner to confirm`
- 30/60/90-day milestones
- Success measures
- Inputs and decisions to confirm
- Working cadence
- Verified customer-safe resources when useful

Exclude:

- ACV, renewal forecast, churn/downsell risk, internal sentiment, or private commercial strategy
- Internal-only stakeholder analysis
- Externally inferred decision authority stated as fact
- Unsupported conclusions
- Private notes or internal escalation language
- Claims that reveal surveillance, such as `we saw you search for...`

### Step 17 — Publish or refresh Notion when authorized

Apply `schema/notion-customer-plans-map.md`.

For a first publication:

1. Resolve the exact account parent under the canonical Customer Plans hub.
2. Create the account parent only when absent.
3. Create or update `Internal Adoption Strategy — Current`.
4. Create or update `Customer-Safe Adoption Plan — Current`.
5. Keep the account parent concise and navigational.
6. Fetch the parent and both current pages after writing.
7. Return only observed Notion URLs.

For a refresh:

1. Fetch the existing internal and customer-safe current pages.
2. Compare current evidence with existing assumptions and plan state.
3. Show material deltas:
   - Validated
   - Changed
   - New stakeholders
   - Blocked
   - Use-case stage movement
   - Measurement movement
   - Recommended Airtable task changes
4. Update current pages in place only when material change exists.
5. If no material change exists, skip the write and report `No material plan change found.`

A successful Notion write must report that Airtable Customer Tasks were not changed.

---

## Required Default Output

Use this structure for a full preview:

```text
# [Account] — Account Adoption Strategy

As of: [date/time Asia/Kolkata]
Mode: [Read/Draft or Notion publication]
Systems changed: [None or exact Notion pages]

## Pilot / Strategy Verdict
[Clear recommendation and account archetype]

## Evidence Used
- Meeting evidence
- Airtable evidence through [date]
- Existing Notion plan, if any
- Public company and people sources
- Official 6sense sources
- Material source limitations

## Account Snapshot
| Field | Evidence |
|---|---|

## Company and GTM Context
[Only findings that affect the adoption plan]

## Account-Motion Assessment
| Dimension | Assessment | Evidence |
|---|---|---|

## Current Adoption Baseline
| Capability / Use Case | Current Stage | Evidence | Gap to Next Stage | Confidence |
|---|---|---|---|---|

## Stakeholder Plan
| Stakeholder / Role | Evidence Status | Proposed Role | Next Engagement |
|---|---|---|---|

## Use-Case Prioritization
| Rank | Use Case | Score | Decision | Readiness Gap |
|---:|---|---:|---|---|

## Primary Use Case
[Problem, outcome, workflow, products, prerequisites, owners, measures]

## Secondary Use Case
[Include only when evidence supports it]

## Future / Deferred Use Case
[Why it is deferred]

## Recommended 90-Day Plan
| Period | Work | Owner | Output | Success Evidence |
|---|---|---|---|---|

## Measurement Framework
### Product Adoption
### Customer Engagement
### Business Movement

## Existing Customer Tasks and Proposed Changes
### Preserve
### Refine
### Proposed New Tasks
### Avoid Duplicates

## Information Still Required
[Numbered validation gaps]

## Customer-Safe Plan Preview
[Shareable plan without internal-only material]

## Completeness and Safety Check
- Exact Airtable account resolved: [Yes/No]
- Meeting evidence depth: [Raw/Structured/Limited]
- Active use cases: [0–2]
- Future/deferred use cases: [0–1]
- Entitlements verified: [Complete/Partial/No]
- Customer-safe boundary checked: [PASS/FAIL]
- Airtable writes performed: No
- Notion publication: [Not requested/Created/Updated/Unchanged/Failed]
- Final status: [PASS/INCOMPLETE/FAIL]
```

When the user asks for only the customer-safe version, output only that plan plus a brief publication confirmation when a write occurred.

---

## Edge Cases

### No substantive meeting evidence

Create an `INCOMPLETE` strategy focused on research-backed hypotheses and a validation agenda. Do not claim customer priorities.

### Public company identity is ambiguous

Stop external research until the correct domain is resolved. Do not attach research from a same-named company.

### Entitlement is missing

Label the capability `Not verified`. Do not call it available, purchased, configured, or active.

### Telemetry is unavailable

Do not manufacture adoption. Make baseline capture the first milestone.

### Support issue dominates

Separate the Support/Product issue from the adoption plan. Do not use adoption work or PS to bypass break/fix ownership.

### No viable active use case

Use `Foundation-first` and define the smallest prerequisite plan. Do not force a campaign, SI rollout, AI Email, or advertising motion.

### Existing Notion account page found elsewhere

Ignore it unless it is a direct child of the canonical Customer Plans hub or the user explicitly asks to move/reconcile it.

### Duplicate current Notion pages

Do not guess which one is authoritative. Ask one concise clarification before updating.

### Notion write unavailable or fails

Return the complete Notion-ready internal and customer-safe drafts, state that publication failed, and do not imply that pages were created or updated.

### No material refresh change

Do not rewrite the page for style. Report `No material plan change found.`

### User asks to share or send the plan

Publication to Notion does not prove the page was shared with the customer. Do not send email, change sharing permissions, or claim customer delivery without a separate explicit request and authorized workflow.

---

## Validation Checklist

Before presenting or publishing a complete plan, verify:

- One exact Airtable account is resolved.
- Public research belongs to the same company.
- Material claims have evidence labels.
- Public observations are not represented as customer confirmation.
- Entitlement, configuration, usage, workflow embedding, and value are separated.
- The plan contains no more than two active use cases and one future/deferred use case.
- Every active use case has a problem, outcome, capability, prerequisite, owner, and measure.
- Product behaviour is backed by verified official 6sense sources.
- Availability and entitlement are not overpromised.
- Stakeholder authority is not inferred from title alone.
- Existing active Airtable tasks are cross-checked and proposed duplicates are avoided.
- No Airtable write occurred.
- Customer-safe content excludes internal-only data and conclusions.
- Notion was written only after explicit current-turn authorization.
- Every Notion write was fetched and verified afterward.
- The final status is `PASS` only when the plan is sufficiently grounded and the requested write state reconciles.

---

## Final Rule

This workflow must produce a customer-specific operating plan, not a repackaged feature list. Research should change the recommendation; meeting evidence should constrain it; official documentation should make it supportable; ownership and measurement should make it executable; Notion should preserve the strategy; and Airtable must remain the authoritative system for customer tasks.
