---
name: sf-stage-progression-validator
description: >
  Validate whether a Salesforce opportunity is ready to progress to the next stage,
  using data pulled from a Salesforce PDF, pasted opportunity data, screenshots, and
  Airtable account context. Also help the user fill missing Salesforce fields with
  paste-ready values when they are actively updating an opportunity.
  Use this skill whenever the user:
  - uploads or pastes a Salesforce opportunity page, screenshot, or PDF
  - asks "can I move this to Stage X" or "is this ready to progress"
  - asks "what fields am I missing" for a deal or account
  - wants to know stage readiness, missing fields, or what to fill before advancing a stage
  - mentions a stage number or stage name alongside an account name or deal name
  - says anything like "check my stage", "stage check", "ready to move", "can I advance", or "what do I need to fill"
  - asks for help filling Salesforce fields such as Next Steps, Last Steps, Current State, Implicit Pain, Executive Priorities, Mobilizer, SI Migration, Decision Process, or Economic Buyer
  - shares a Salesforce validation/save error after trying to move a stage
  Always trigger even if the user doesn't use the word "Salesforce" — if they're talking about stages, opportunity fields, field completion, Salesforce save errors, or deal progression, this skill applies.
---

# SF Stage Progression Validator

This skill validates whether a Salesforce opportunity is ready to advance to a target stage. It merges Salesforce opportunity data with Airtable account context, validates stage requirements, identifies blockers, and when useful generates paste-ready Salesforce field updates.

This skill has two main modes:

1. **Validation Mode** — use when the user asks whether an opportunity is ready to move stages.
2. **Field Fill / Operator Mode** — use when the user is actively updating Salesforce and needs concise, paste-ready values or help clearing validation errors.

Always read the full rules in this file before beginning validation.

---

## Core Principles

- Salesforce is the system of record.
- Airtable is supporting context only.
- If Salesforce and Airtable conflict, use Salesforce and flag the conflict.
- If a Salesforce backend validation error is provided, treat the error message as the highest-confidence source for what is blocking the save.
- Do not invent details to clear validation.
- Use Airtable to draft field values only when the context is reasonably supported by notes or activity.
- Clearly separate:
  - Confirmed Salesforce fields
  - Confirmed Airtable context
  - Airtable-inferred values that need Salesforce confirmation
  - Missing or not confirmed fields

---

## Step 1 — Identify the User's Intent

Determine which mode applies.

### A. Validation Mode
Use this when the user asks:
- Can I move this to Stage X?
- Is this ready to progress?
- Validate this opportunity.
- What fields are missing?
- Ready to move?

### B. Field Fill / Operator Mode
Use this when the user asks:
- Help me fill this field.
- What should I put in Next Steps?
- Pull Last Steps.
- What else do I need to fill?
- Give me paste-ready values.
- Pull what you can from Airtable.
- I got this error when trying to save.

In Field Fill / Operator Mode, do **not** default to a long validation report. Give concise, actionable, paste-ready field values.

---

## Step 2 — Identify the Input

Determine what the user has provided:

**Salesforce data** — one of:
- A PDF attachment of the Salesforce opportunity page → extract text from the PDF
- Pasted text from Salesforce → parse inline
- A screenshot → extract visible fields from the image
- A Salesforce save/progression error → parse the exact blocking fields

**Account name** — extract this from the Salesforce data or the user's message. You will need it to query Airtable.

**Target stage** — the user may state it explicitly ("move to Stage 3") or it may be implied ("can I advance"). If not stated, infer the logical next stage from the current stage and state the assumption.

**Deal type** — determine whether this is a renewal or new-business opportunity. Look for Salesforce Type, opportunity name patterns, or user context. If unclear and required for validation, ask one short clarification question.

---

## Step 3 — Salesforce Validation Error Recovery

When the user shares a Salesforce save/progression error, use this workflow before relying on the stored stage matrix.

Treat the Salesforce error message as the most current backend validation rule.

### Error Recovery Steps

1. Parse the exact field names from the error.
2. Compare them against the current rules in this skill.
3. If the error names a field not present in the matrix, add it to the immediate blocker list and label it as **Backend-required from Salesforce error**.
4. Pull what can be confirmed from Salesforce and Airtable.
5. Generate a short correction pack with:
   - Blocking field name exactly as Salesforce shows it
   - Whether it is confirmed from Salesforce
   - Whether it can be supported by Airtable
   - Paste-ready value if available
   - “Do not guess” warning if not confirmable
6. Recommend the next save attempt.

### Error Recovery Output Format

```md
### Salesforce Error Blockers
- [Exact Salesforce field name] — [status]

### Paste-Ready Values
**[Field Name]**
```text
[value]
```

### Needs Confirmation / Do Not Guess
- [Field] — [why]

### Recommended Save Attempt
- Fill these fields first: [list]
- Then try moving the opportunity again.
```

### Important Rule

If Salesforce requires a field like **Primary Competitor RIP**, do not invent a competitor or rip reason. If Airtable does not confirm it, say so clearly.

---

## Step 4 — Pull Airtable Context

Use the Airtable MCP tool to enrich the opportunity data with account-level context stored in Ranjodh's Airtable base.

**Airtable base:** `app6O8peF5ywLe1GM`

### 4a. Find the account record

Search the Accounts table (`tblr6UnvfaqfNvwyU`) for the account name extracted from Salesforce or the user's message.

Retrieve:
- Account Name (`fldOSLvopNOX6ae3Z`)
- ACV (`fldjieKzPumeF6afD`)
- Renewal Date (`fldPmw5pHDNDgZYgA`)
- Activity Notes (`flddz3lqUmEhLhmN5`)
- Stage (`fldTYypHjPaFcatCi`)
- Churn Risk (`fldy4GIC8xDuPjS8y`)

### 4b. Airtable Account Confirmation Rule

Before using Airtable content, verify that the returned Account Name exactly or strongly matches the Salesforce Account Name.

If the displayed row or returned content does not match the requested account:
- Do not use that record.
- Retry using an Account Name contains filter.
- If multiple records appear, use the one whose Account Name exactly matches the Salesforce Account Name.
- Always mention which Airtable account name was used in the output.

### 4c. Pull recent activity and notes

Search the Detailed Notes table (`tblI5cCnIY63S6pZq`) for records linked to or mentioning the account.

Retrieve:
- Title (`fldbf738tn5U18z7D`)
- Notes (`fldcfsEsMgFHoB8VH`)
- Activity Type (`fldfi0aH7CncdNtb9`)
- Date (`fld5Dd0gO8vHadVjl`)
- Next Steps (`flduD4nsj6ZkEXmLZ`)

Focus on the 5 most recent relevant entries when possible.

If no Detailed Notes records are found, proceed with the Accounts table Activity Notes and say that no separate Detailed Notes records were found.

### 4d. Synthesize Airtable context

From Airtable data, extract field-level information that may supplement or confirm Salesforce data. Examples:
- Champion / mobilizer contacts
- Economic buyer references
- Competitor mentions
- Current state and pain
- Executive priorities
- ROI or business outcome references
- Technical blockers
- Workflow progress
- Evaluation criteria
- Next steps
- Last completed actions
- Renewal risk signals

If the account is not found in Airtable, proceed with Salesforce data only and note the gap.

---

## Step 5 — Merge Field Data

Build a unified picture of what is populated by combining:

1. Fields explicitly visible in Salesforce
2. Fields inferable from Airtable notes and activity entries
3. Fields the user states are complete
4. Salesforce backend validation errors shared by the user

For each required field, determine:

- **Confirmed populated in Salesforce** — field is clearly present and has a non-placeholder value
- **Confirmed from Airtable** — Airtable clearly supports the value, but Salesforce still needs to be updated/confirmed
- **Inferred from Airtable** — likely value based on notes; needs Salesforce confirmation
- **Missing / incomplete** — no evidence the field is populated or supported
- **Do not guess** — field requires a concrete business fact not available in Salesforce or Airtable

Apply the completion rules in this file when assessing each field.

---

## Step 6 — Run Stage Validation

Using the merged field data:

1. Normalize current stage and target stage to canonical StageName values.
2. Select the correct validation mode:
   - **Renewal mode** if the deal is a renewal → use the Renewal Process Overlay.
   - **Generic mode** if new business or unspecified → use the cumulative mandatory field matrix.
3. Build the full cumulative required field list for the target stage.
4. Add any backend-required fields surfaced by Salesforce validation errors.
5. Compare required fields against confirmed/inferred populated fields.
6. Identify blockers.
7. Determine overall readiness verdict.

---

## Step 7 — Output Selection

Choose the shortest useful output for the user's current goal.

### A. Full Validation Output
Use for stage-readiness questions.

Required sections:

```md
### Data Sources Used
- Salesforce: [PDF / pasted fields / screenshot / error]
- Airtable: [account found / not found; account name used; notes reviewed Y/N]

### Result
- Ready to Progress / Not Ready to Progress

### Current Stage
- [Current stage]

### Target Stage
- [Target stage]

### Required Fields for Target Stage
- [Full cumulative list after de-duplication]

### Missing or Incomplete Fields
- [Only blockers]

### Inferred Fields / Needs Salesforce Confirmation
- [Fields inferred from Airtable notes]

### Recommendation
- [Short guidance]

### Suggested Next Actions
- [Practical steps]

### Assumptions Used
- [Only if assumption-based logic affected the result]

### Account Context Note
- [Optional risk or relationship context]
```

### B. Field Fill / Operator Mode Output
Use when the user is actively filling Salesforce.

```md
### Confirmed from Salesforce
- [field/value]

### Confirmed from Airtable
**[Field Name]**
```text
[paste-ready value]
```

### Needs Confirmation
- [field] — [why]

### Do Not Guess
- [field] — not found in Salesforce or Airtable

### Recommended Save Attempt
- Fill these fields first: [list]
- Then try saving / moving the stage again.
```

### C. Paste-Ready Field Pack
Use when the user asks for values to paste.

```md
**[Field Name]**
```text
[paste-ready value]
```
```

Keep values concise, Salesforce-friendly, and based only on confirmed Salesforce/Airtable context.

---

## Step 8 — Next Steps and Last Steps Generation

### Next Steps

When the user asks for **Next Steps**:
- Generate forward-looking actions only.
- No dates unless requested.
- Use concise bullets or one Salesforce-ready paragraph.
- Prioritize actions tied to stage progression, renewal risk, buyer alignment, technical blockers, and customer commitments.
- If the user asks for pointers, use `-` bullets.

Example:

```text
- Prepare CMO-facing ROI / renewal value discussion with customer team.
- Confirm meeting timing and align senior internal attendee.
- Follow up on technical blocker and confirm workaround/status.
```

### Last Steps

When the user asks for **Last Steps**:
- Pull the most recent completed actions from Salesforce activity history and Airtable notes.
- Include dates if available.
- Keep to 3–5 bullets.
- Do not include future actions.
- Do not list open tasks as completed steps.

Example:

```text
4/26 - Clarified audience workflow account-creation/contact-enrichment limitation and shared workaround with customer.
4/23 - Looped in sales POC for additional SI licenses and initiated support ticket.
4/22 - Reviewed renewal risk, ROI narrative needs, package confusion, and technical mapping blocker with customer team.
```

---

# Reference: Stage Validation Rules

## 1. Confidence Model

### Confirmed from Salesforce schema
- `StageName`, `CloseDate`, `Amount`, `Type`, `LeadSource`, `NextStep`, `Next_Steps__c`, `Stage_0_Date_New__c`, `Stage_1_Date_New__c` exist
- `Stage_Exit_Criteria_Audit__c` is a multi-select picklist
- Stage 5 document fields (`NDA__c`, `Online_TOS_MSA__c`, `Order_Form__c`, `DPA__c`, `PO_Needed__c`) are picklists, not booleans

### Assumed pending RevOps/admin confirmation
- Stage progression is cumulative
- Stage skipping is allowed if all cumulative requirements for the target stage are met
- `Stage 5.5 - Closed Pending Review` inherits Stage 5 requirements with no additional confirmed fields
- `Dead - Lost` is not validated unless the user explicitly provides closed-lost required fields
- Next Steps requirement is satisfied if either `NextStep` or `Next_Steps__c` is populated

When strict admin-grade validation is requested, clearly separate confirmed logic from assumption-based logic in the output.

---

## 2. Core Rule

Stage progression is cumulative:
- Each stage inherits mandatory requirements from all previous stages
- A field required at an earlier stage remains required at all later stages
- Fields marked with `+` are additional fields on top of all prior requirements
- Stage 6 requires all Stage 5 contracting fields to be in a completed state, not in progress

---

## 3. Canonical Stage Values

| Stage Key | Exact StageName Value |
|---|---|
| Stage 0 | Stage 0 - Calendared Meeting |
| Stage 1 | Stage 1 - Understand Pain & Priority |
| Stage 2 | Stage 2 - Agreement on Evaluation Plan |
| Stage 3 | Stage 3 - Solution Validation |
| Stage 4 | Stage 4 - Executive Confirmation |
| Stage 5 | Stage 5 - Contracting |
| Stage 5.5 | Stage 5.5 - Closed Pending Review |
| Stage 6 | Stage 6 - Closed Won |
| Dead | Dead - Lost |

Normalize user inputs: `Stage 0`, `0`, `Calendared Meeting` → `Stage 0 - Calendared Meeting`. Same pattern for all stages.

---

## 4. Known Field Mapping

| Business Label | Salesforce Field | Notes |
|---|---|---|
| Stage Name | `StageName` | Confirmed |
| Close Date | `CloseDate` | Confirmed |
| ACV Amount | `Amount` | Confirmed |
| Opportunity Type | `Type` | Confirmed |
| Lead Source | `LeadSource` | Confirmed |
| Next Steps (standard) | `NextStep` | Confirmed |
| Next Steps (custom) | `Next_Steps__c` | Confirmed |
| Stage 0 Date | `Stage_0_Date_New__c` | Confirmed |
| Stage 1 Date | `Stage_1_Date_New__c` | Confirmed |
| Stage Exit Criteria Audit | `Stage_Exit_Criteria_Audit__c` | Confirmed multi-select picklist |
| NDA | `NDA__c` | Confirmed picklist |
| Online TOS/MSA | `Online_TOS_MSA__c` | Confirmed picklist |
| Order Form | `Order_Form__c` | Confirmed picklist |
| DPA | `DPA__c` | Confirmed picklist |
| PO Needed | `PO_Needed__c` | Confirmed picklist |

For unmapped fields, preserve the business label exactly as Salesforce displays it.

---

## 5. Generic Mandatory Fields by Stage

Use this matrix when the deal is not confirmed as a renewal.

### Stage 0
- Stage Name
- Close Date
- ACV Amount
- Opportunity Type
- Lead Source
- Next Steps
- Stage 0 Date

### Stage 1 (+)
- All Stage 0 fields
- Champion Contact
- Primary Competitor
- CRM
- MAP
- Next Steps

### Stage 2 (+)
- All Stage 1 fields
- Prioritized Use Cases
- Tech Fit
- Evaluation Plan Link
- Discovery Call Date

### Stage 3 (+)
- All Stage 2 fields
- SC Next Steps
- Tech Solution Reviewed with SC

### Stage 4 (+)
- All Stage 3 fields
- Executive Summary Link
- Stage Exit Criteria Audit
- Close Plan with Customer

### Stage 5 (+)
- All Stage 4 fields
- NDA
- Online TOS/MSA
- Order Form
- DPA
- PO Needed
- Payment Terms

### Stage 5.5
- All Stage 5 fields, carry-forward only

### Stage 6
- All cumulative fields through Stage 5
- All Stage 5 contracting fields must be in a completed state

### Dead - Lost
- No requirement enforced unless the user explicitly provides closed-lost rules

---

## 6. Special Interpretation Rules

### Next Steps ambiguity
- Treat as satisfied if either `NextStep` or `Next_Steps__c` is non-empty.
- If both are empty, mark as missing.
- For strict validation, note that the exact enforced field is unconfirmed by admin.

### Evaluation Plan Link vs Stage Exit Criteria Audit
- These are distinct requirements.
- `Evaluation Plan Link` is a Stage 2 requirement.
- `Stage_Exit_Criteria_Audit__c` is a multi-select picklist required at Stage 4.
- For Stage 2, accept either a clearly populated Evaluation Plan Link field or user confirmation that the relevant Stage Exit Criteria Audit value for evaluation plan is complete.
- If neither is present, mark Evaluation Plan Link as missing.

### De-duplication
If the same business requirement appears across inherited stages, show it only once in the final required list unless the user explicitly distinguishes two separate fields.

### Backend error override
If the Salesforce save error requires fields that are not listed in this skill, add them to the blocker list and state that they were surfaced by Salesforce backend validation.

---

## 7. Completion Rules

| Field Type | Complete If |
|---|---|
| Text / long text | Non-empty and not a placeholder |
| Picklist | Value selected and not `Not Started`, `Unknown`, or blank |
| Date | Valid date present |
| Currency / amount | Populated and > 0 unless zero-value explicitly allowed |
| Lookup / contact | Record populated |
| Checkbox | `true` where business requirement implies affirmative completion |
| URL / link | Non-empty |
| Multi-select picklist | At least one relevant required value present |

### Stage 5 contracting fields

**NDA__c**
- Complete: `Executed`, `Not Needed`
- Incomplete: `Not Started`, `Sitting with Legal`, `Sitting with Sales`, `Sitting with Customer`

**Online_TOS_MSA__c**
- Complete: `Executed MSA`, `Online TOS Only`, `Not Needed`
- Incomplete: `Not Started`, `Sitting with Legal`, `Sitting with Sales`, `Sitting with Customer`

**Order_Form__c**
- Complete: `Executed`, `Not Needed`
- Incomplete: `Not Started`, `Sitting with Legal`, `Sitting with Sales`, `Sitting with Customer`

**DPA__c**
- Complete: `Executed`, `Not Needed`
- Incomplete: `Not Started`, `Sitting with Legal`, `Sitting with Sales`, `Sitting with Customer`

**PO_Needed__c**
- Complete: `Not Needed`, `PO Needed`
- Incomplete: `Not Started`, `Not Sure`

### Stage 6 completion rule
All Stage 5 contracting fields must meet completed-state rules. In-progress values do not qualify for Stage 6.

---

## 8. Input Normalization

| User Input | Maps To |
|---|---|
| `Amount` | ACV Amount |
| `Type` | Opportunity Type |
| `competitor` | Primary Competitor |
| `primary competitor rip` | Primary Competitor RIP |
| `champion` | Champion Contact |
| `mobilizer` | Mobilizer |
| `next step` / `next steps` | Next Steps |
| `last step` / `last steps` | Last Steps |
| `eval plan` | Evaluation Plan Link |
| `stage exit audit` | Stage Exit Criteria Audit |
| `close plan` | Close Plan with Customer |
| `renew term` | Renew Term (# of Months) |
| `SI migration` | SI Migration |

Use exact Salesforce API names when provided. Otherwise preserve business labels in output.

---

# Renewal Process Overlay

Use this overlay when the deal is confirmed as a renewal opportunity. It takes precedence over the generic mandatory field matrix for renewal deals.

If there is a conflict between the generic field matrix and the renewal overlay:
- Call out the conflict explicitly when relevant.
- For renewal deals, prefer the renewal overlay as operational source of truth.
- If Salesforce backend validation provides a different requirement, prefer the backend error as the immediate blocker.

---

## Renewal Stage Matrix

### Stage 0 - Calendared Meeting

**During Stage**
- Ask customer about renewal intent.
- Share renewal process.
- Understand upsell potential.
- Engage SC if needed.

**Exit Criteria**
- Internal kickoff call completed to review 6sense usage, contracts, and executive multithreading.

**Required Fields**
- Opportunity CSM
- Contract Expiration Close Date
- Discovery Call
- Next Steps
- Just Ask Response
- Just Ask Response Date

### Stage 1 - Understand Pain & Priorities

**During Stage**
- Request BVR.
- Capture ROI to defend renewal and value.
- Understand current state, tech stack, pains, and ATL executive priorities.

**Exit Criteria**
- ROI clearly understood.
- EBR / executive renewal alignment motion scheduled or actively being scheduled.

**Required Fields**
- CRM
- Current State
- Data Provider
- Executive Priorities
- Executive Priorities Metrics
- Expected Renewal ACV
- Implicit Pain
- Incumbent Competitor(s)
- MAP
- Mobilizer
- Mobilizer Proof Point
- Primary Competitor(s)
- Primary Competitor RIP
- Priority Metrics Targets / Notes
- Renew Term (# of Months)
- SI Migration

**Observed backend-required fields for Stage 1 → Stage 2**
- Primary Competitor RIP
- Renew Term (# of Months)
- SI Migration

**Primary Competitor RIP handling**
- If Salesforce allows `None`, `No RIP`, `Not Applicable`, or equivalent and no competitor rip is confirmed, use that.
- If Salesforce requires a named competitor or picklist value, do not infer it unless clearly mentioned in Salesforce, Airtable, or the user's message.
- Never invent a competitor to clear validation.

### Stage 2 - Agreement on Evaluation Plan

**During Stage**
- Present EBR to align stakeholders and showcase BVR data, ROI, and use-case wins.
- Understand decision process and evaluation criteria.

**Exit Criteria**
- Evaluation for renewal agreed.
- Scoping call scheduled.

**Required Fields**
- Decision Process
- Economic Buyer
- Economic Buyer Proof Point
- Pilot / Trial Type
- Prioritized Evaluation Criteria

### Stage 3 - Solution Validation

**During Stage**
- Conduct scoping call to align on units and usage.
- Validate technical integrations.
- Prioritize use cases.

**Exit Criteria**
- Customer agrees to renew and move forward exclusively with 6sense, VOC captured.

**Required Fields**
- Prioritized Use Cases
- Tech Valid Success Factor Reason
- Technical Validation Success Factor(s)

### Stage 4 - Executive Confirmation

**During Stage**
- Present proposal with options and term length.
- Understand paper process and timeline.

**Exit Criteria**
- Renewal close plan approved by customer.

**Required Fields**
- Paper Process
- Pricing Meeting Date

### Stage 5 - Contracting

**During Stage**
- Contract prepared and renewal documents sent.
- Update Success Plan for future-state goals.

**Exit Criteria**
- Customer signs and contracts are attached to the opportunity.

**Required Fields**
- Anything Account Team Should Know
- Cust Implementation Team
- Customer Team Primary Location
- Future State GTM Objectives & Initiatives
- What's the Upsell Potential
- Why Did We Win

### Stage 6 - Closed Won

**During Stage**
- Send win wire.
- Send thank-you email to executive with leadership CC'd.

**Exit Criteria**
- None explicitly defined.

**Required Fields**
- None explicitly defined.

---

## Renewal Evaluation Process

When checking a renewal opportunity against a target stage:

1. Check renewal overlay required fields for the target stage.
2. Add cumulative requirements from prior renewal stages.
3. Add backend-required fields from any Salesforce error message.
4. Check whether the prior stage's exit criteria appear to have been met.
5. Report three separate outputs:
   - Field completion status
   - Exit-criteria readiness
   - Recommended next action during the stage
6. If the opportunity is field-complete but exit criteria are not satisfied, mark as **Operationally not ready**.

---

# Paste-Ready Renewal Field Guidance

When the user asks what to fill, generate concise Salesforce-ready values for supported fields.

## Common Stage 1 Renewal Fields

### Current State
Summarize the customer's active usage, renewal posture, implementation status, and blockers.

### Business Outcomes
Summarize what the customer is trying to accomplish with 6sense.

### Implicit Pain / Implicate Pain
Summarize why the customer needs change, why renewal value is at risk, and what friction exists.

### Executive Priorities
Summarize the executive-level outcomes the renewal must support.

### Executive Priorities Metrics
Use only metric categories or exact metrics supported by Salesforce/Airtable. If exact values are unavailable, say exact values are not captured.

### Priority Metrics Targets / Notes
Summarize success indicators, target outcomes, or validation points.

### Mobilizer
Use only a clearly active stakeholder from Salesforce/Airtable.

### Mobilizer Proof Point
Explain the stakeholder's active involvement and why they are a credible mobilizer.

### CRM / MAP
Use only confirmed systems.

### Data Provider
Do not guess. If not found, state not confirmed.

### Primary Competitor / Incumbent Competitor
Do not guess. If not found, state not confirmed.

### Primary Competitor RIP
If no competitor rip is confirmed and Salesforce allows None / N/A / No RIP, recommend that. Otherwise mark as do not guess.

### Renew Term (# of Months)
Use Salesforce if present. For renewals, this is often already on the opportunity.

### SI Migration
Summarize SI package, license, usage, credit, or migration context if supported.

## Common Stage 2 Renewal Fields

### Decision Process
Summarize how the renewal evaluation and approval process appears to work. Flag if inferred.

### Economic Buyer
Use only if clearly identified. If a CMO, CFO, VP, or contract signer is referenced, mark as inferred unless Salesforce confirms the role.

### Economic Buyer Proof Point
Use only evidence that ties the person to approval, budget, contract signature, executive renewal review, or final decision.

### Pilot / Trial Type
If Salesforce shows `None` or there is no new pilot/trial for a renewal, say so. Do not invent.

### Prioritized Evaluation Criteria
Summarize the criteria the customer is using to judge renewal value.

---

# Edge Cases

- **No Salesforce data provided**: Ask the user to paste the opportunity fields, upload a PDF, or share a screenshot before full validation. If they only want Airtable context, pull Airtable and clearly state that Salesforce confirmation is missing.
- **No target stage stated**: Infer the next logical stage and state the assumption.
- **Account not in Airtable**: Proceed with Salesforce data only. Note the absence.
- **Airtable returns the wrong account**: Do not use it. Retry and verify the account name.
- **Stage skipping requested**: Apply cumulative validation across all intermediate stages and flag the skip explicitly.
- **Dead - Lost validation**: Only validate if the user explicitly provides closed-lost field requirements.
- **Conflict between Airtable notes and Salesforce data**: Default to Salesforce as system of record and flag the conflict.
- **Salesforce validation error conflicts with skill rules**: Treat the Salesforce error as the immediate backend blocker and update the blocker list accordingly.

---

# Notes for Future Improvement

Update this skill when RevOps/Salesforce Admin confirms:
- Which Next Steps field is truly enforced
- Exact API names for all non-schema-confirmed business fields
- Whether stage skipping is blocked at the validation layer
- Whether Stage 5.5 has special requirements
- Whether Dead - Lost has required loss-reason fields
- Whether requirements are conditional by region, segment, product, deal type, or renewal subtype
- Whether the renewal visual overrides, supplements, or merely guides the existing validation model
- Exact picklist values for Primary Competitor RIP
- Exact allowed values for Pilot / Trial Type on renewal opportunities
- Whether `None`, `Not Applicable`, or equivalent is acceptable for competitor-related fields

Until then, this skill operates as a strong operational guide and Salesforce field-completion assistant rather than a guaranteed mirror of every backend validation rule.
