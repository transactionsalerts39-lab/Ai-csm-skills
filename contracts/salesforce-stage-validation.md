# Salesforce Stage Validation Contract — 2026-07-28 RevOps Update

Use this contract with `skills/sf-stage-progression-validator.md`.

## Authority and evidence order

Use evidence in this order:

1. A current Salesforce backend save or progression error
2. Current Salesforce admin metadata or live validation-rule evidence
3. This transition matrix, derived from `UpdatedValidationRules.xlsx`
4. Airtable and other account context for drafting and operational guidance only

Never treat Airtable as proof that a Salesforce field is populated.

## Deployment timing

RevOps stated that Phase 1 validation-rule removals are scheduled for end of day Friday, July 31, 2026. No exact timezone or deployment confirmation was supplied.

- Through July 31, 2026, label Phase 1 fields `Phase 1 scheduled for removal`.
- If a move would be blocked only by Phase 1 fields before confirmed deployment, return Salesforce gate `Transitional`, not `Clear`.
- From August 1, 2026 onward, treat Phase 1 fields as `Removed / non-blocking` unless Salesforce or a trusted admin source proves otherwise.
- If Salesforce still names a Phase 1 field after the scheduled removal, treat it as an immediate blocker and flag a likely rollout discrepancy.
- Keep Phase 2 and future-phase fields active until their removal or adjustment is explicitly confirmed.

## Field status vocabulary

Use only these statuses:

- `Mandatory` — active Salesforce hard gate
- `Phase 1 scheduled for removal` — transitional until the Phase 1 boundary
- `Phase 2 pending` — active hard gate until Phase 2 deployment is confirmed
- `Future-phase adjustment pending` — active hard gate until a future adjustment is confirmed
- `Removed / non-blocking` — not a Salesforce stage blocker
- `Needs RevOps confirmation` — source ambiguity prevents a deterministic transition assignment
- `Backend-required override` — current Salesforce error proves the field blocks the attempted move
- `Operational only` — important deal-quality or exit-criteria information, but not supported as a validation blocker

## Validation model

- Validate the exact stage transition, not a cumulative target-stage inheritance model.
- For a stage skip, evaluate every intermediate transition in order and report the blockers by transition.
- Do not carry an earlier transition's validation fields forward unless Salesforce explicitly reports them.
- Keep stage fields separate from operational exit criteria.
- Blank workbook API names remain `Unmapped`; never invent API names.

## New Business and Upsell

### Opportunity creation

| Field | API name | Workbook status |
|---|---|---|
| Opportunity Name | `Name` | Mandatory |
| Close Date | `CloseDate` | Mandatory |
| Type | `Type` | Mandatory |
| Stage | `StageName` | Mandatory |
| ACV Amount | `Amount` | Mandatory |
| Next Steps | `Next_Steps__c` | Phase 1 scheduled for removal |
| Discovery Call | `Discovery_Call__c` | Phase 1 scheduled for removal |
| Primary Contact | `Primary_Contact__c` | Mandatory |
| Primary Campaign Source | `CampaignId` | Phase 2 pending |

The visible workbook says `closed date`; the field inventory and Salesforce-standard mapping support `CloseDate`. Use `CloseDate` and flag the workbook typo only if strict source reconciliation is requested.

### S0 → S1

| Field | API name | Workbook status |
|---|---|---|
| BDR | `BDR__c` | Phase 1 scheduled for removal |
| Decision Timeline | `Implementation_Timeline_Discussed__c` | Phase 1 scheduled for removal |
| Prep Doc | `Prep_Doc__c` | Phase 1 scheduled for removal |

After Phase 1, this transition has no workbook-confirmed hard gate.

### S1 → S2

| Field | API name | Workbook status |
|---|---|---|
| CRM | `CRM__c` | Mandatory |
| Current State | `Current_State__c` | Phase 1 scheduled for removal |
| Data Provider | `Data_Provider__c` | Phase 1 scheduled for removal |
| Business Outcomes | `Executive_Priorities__c` | Phase 1 scheduled for removal |
| MAP | `MAP__c` | Phase 1 scheduled for removal |
| Buying Team Notes | `Buying_Team_Notes__c` | Phase 1 scheduled for removal |
| Who does the customer sell to? | `Who_does_the_customer_sell_to__c` | Phase 1 scheduled for removal |
| Champion Contact | `Mobilizer_Contact__c` | Phase 1 scheduled for removal |
| Solution Consultant | `Solution_Consultant__c` | Phase 1 scheduled for removal |
| Primary Competitor RIP | `Primary_Competitor_Rip__c` | Phase 1 scheduled for removal |

### S2 → S3

| Field | API name | Workbook status |
|---|---|---|
| Win Strategy Meeting | `Win_Strategy_Meeting__c` | Phase 1 scheduled for removal |
| Economic Buyer Contact | `Economic_Buyer__c` | Phase 1 scheduled for removal |
| POV Approval | `POV_Approval__c` | Phase 1 scheduled for removal |
| POV Type | `POV_Type__c` | Mandatory |
| POV Approval Notes | `POV_Approval_Notes__c` | Phase 1 scheduled for removal |

### S3 → S4

| Field | API name | Workbook status |
|---|---|---|
| Technical Validation Success Factor(s) | `Technical_Validation_Success_Factor_s__c` | Phase 1 scheduled for removal |
| Prioritized Use Cases | `Use_Cases__c` | Phase 1 scheduled for removal |
| BVA Status | `BVA_Status__c` | Phase 1 scheduled for removal |

After Phase 1, this transition has no workbook-confirmed hard gate.

### S4 → S5

| Field | API name | Workbook status |
|---|---|---|
| Primary Quote | `SBQQ__PrimaryQuote__c` | Mandatory |
| Pricing Meeting Date | `Pricing_Meeting_Date__c` | Phase 1 scheduled for removal |
| Executive Contact Role | Unmapped | Phase 2 pending |
| Delivery Scoping | Unmapped | Phase 2 pending |
| Win Wire Review | `Win_Wire_Review__c` | Phase 1 scheduled for removal |
| Contact Role "6s Primary Admin" | Unmapped | Phase 2 pending |

The visible stage-exit sheet also places `Docusign completion OR Signed OF — Ready for Review warning` under S4 → S5, while the hidden source sheet places it under S5 → S5.5. Its API name is blank. Report this item as `Needs RevOps confirmation`; do not silently assign it to either transition. If a Salesforce error names it, use `Backend-required override`.

### S5 → S5.5

The only workbook candidate is the ambiguous Docusign / signed-order-form warning described above. Do not claim a deterministic hard gate without backend or RevOps confirmation.

### S5 → S6

| Field | API name | Workbook status |
|---|---|---|
| Anything Account Team Should Know? | `Anything_else_the_6s_Account_Team__c` | Phase 1 scheduled for removal |
| Customer Implementation Team | `Customer_Implementation_Team__c` | Phase 1 scheduled for removal |
| Customer Team Primary Location | `Customer_Team_Primary_Location__c` | Phase 1 scheduled for removal |
| Future GTM Objectives & Initiatives | `Future_State_GTM_Objectives_Initatives__c` | Phase 1 scheduled for removal |
| Why Did We Win? | `WinWire_Details_Deal_Description_TYs__c` | Phase 1 scheduled for removal |
| What's the Upsell Potential? | `What_s_the_upsell_potential__c` | Phase 1 scheduled for removal |
| OF Type | Unmapped | Mandatory |
| Order Execution Date | `Order_Execution_Date__c` | Mandatory |

## Renewal

### S0 → S1

| Field | API name | Workbook status |
|---|---|---|
| Contract Expiration Close Date | `Target_Close_Date__c` | Mandatory |
| Opportunity CSM | `Opportunity_CSM__c` | Mandatory |
| Discovery Call | `Discovery_Call__c` | Phase 1 scheduled for removal |
| Next Steps | `Next_Steps__c` | Phase 1 scheduled for removal |
| Just Ask Response | `Just_Ask_Response__c` | Phase 1 scheduled for removal |
| Just Ask Response Date | `Just_Ask_Response_Date__c` | Phase 1 scheduled for removal |

### S1 → S2

| Field | API name | Workbook status |
|---|---|---|
| Renew Term (# of Months) | `Renewal_Term_of_Months__c` | Phase 1 scheduled for removal |
| Current State | `Current_State__c` | Phase 1 scheduled for removal |
| Champion Contact | `Mobilizer_Contact__c` | Phase 1 scheduled for removal |
| CRM | `CRM__c` | Phase 1 scheduled for removal |
| MAP | `MAP__c` | Phase 1 scheduled for removal |
| Business Outcomes | `Executive_Priorities__c` | Phase 1 scheduled for removal |
| Executive Priorities Metrics | `Executive_Priorities_Metrics__c` | Phase 1 scheduled for removal |
| Data Provider | `Data_Provider__c` | Phase 1 scheduled for removal |
| SI Migration | `SI_Migration__c` | Phase 1 scheduled for removal |

After Phase 1, this transition has no workbook-confirmed hard gate.

### S2 → S3

| Field | API name | Workbook status |
|---|---|---|
| Economic Buyer | `Economic_Buyer_New__c` | Phase 1 scheduled for removal |
| POV Type | `POV_Type__c` | Phase 1 scheduled for removal |

After Phase 1, this transition has no workbook-confirmed hard gate.

### S3 → S4

| Field | API name | Workbook status |
|---|---|---|
| Prioritized Use Cases | `Use_Cases__c` | Phase 1 scheduled for removal |
| Tech Valid Success Factor Reason | `Tech_Valid_Success_Factor_Reason__c` | Phase 1 scheduled for removal |
| Technical Validation Success Factor(s) | `Technical_Validation_Success_Factor_s__c` | Phase 1 scheduled for removal |

After Phase 1, this transition has no workbook-confirmed hard gate.

### S4 → S5

| Field | API name | Workbook status |
|---|---|---|
| Pricing Meeting Date | `Pricing_Meeting_Date__c` | Phase 1 scheduled for removal |

After Phase 1, this transition has no workbook-confirmed hard gate.

### S5 → S6

| Field | API name | Workbook status |
|---|---|---|
| Anything Account Team Should Know? | `Anything_else_the_6s_Account_Team__c` | Phase 1 scheduled for removal |
| Customer Implementation Team | `Customer_Implementation_Team__c` | Future-phase adjustment pending |
| Customer Team Primary Location | `Customer_Team_Primary_Location__c` | Future-phase adjustment pending |
| What's the Upsell Potential? | `What_s_the_upsell_potential__c` | Future-phase adjustment pending |

The three future-phase fields remain active blockers until their adjustment is confirmed.

## Reconciliation checks

Use these totals to detect accidental omissions:

- New Business / Upsell Phase 1 removals: 29
- Renewal Phase 1 removals: 20
- Total Phase 1 removals: 49
- New Business / Upsell Phase 2 fields: 5
- Renewal future-phase fields: 3
- Total later-phase fields: 8
- New Business / Upsell mandatory fields: 11
- Renewal mandatory fields: 2
- Total mandatory fields: 13

The Docusign / signed-order-form warning is counted among the five New Business / Upsell Phase 2 fields but retains `Needs RevOps confirmation` for its transition.

## Operational readiness

Removed validation barriers do not remove deal-quality expectations.

Assess these separately as operational guidance when relevant:

- MEDDPICC information and evidence quality
- Next Steps and Last Steps freshness
- Current State and Business Outcomes
- Champion / Mobilizer and proof point
- Economic Buyer and decision process
- Primary competitor and competitive strategy
- Solution Consultant involvement when required
- Prioritized use cases and technical validation success factors
- Renewal value, ROI, risk, and stakeholder alignment
- Stage exit criteria and customer commitments

Never place these under `Confirmed Salesforce blockers` unless Salesforce or a trusted admin source proves that the current backend requires them.

## Deprecated blocker assumptions

Do not treat these as workbook-confirmed hard gates:

- Cumulative inheritance of every earlier stage field
- Next Steps after its Phase 1 removal
- Renewal CRM, Primary Competitor RIP, Renew Term, or SI Migration after Phase 1
- Renewal POV Type after Phase 1
- The previous NDA / MSA / Order Form / DPA / PO completion matrix
- Stage 5.5 inheriting all Stage 5 requirements
- Generic `LeadSource` as an opportunity-creation gate

Current Salesforce backend errors still override this section.
