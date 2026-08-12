# V8 Account Follow-Up Active Task Cross-Check Regression Cases — V10 Compatibility

Run these as dry-run scenarios. Do not modify Airtable, Notion, Gmail, or another customer system.

Assume dates are evaluated in `Asia/Kolkata`. Apply `contracts/account-activity-recency.md` before the active-task cross-check.

| # | Scenario | Expected result |
|---:|---|---|
| 1 | `/account follow-up Boostlingo` with four active Airtable tasks | Workspace begins with `Account Activity Check`, then `All Active Associated Tasks`, and displays all four active tasks exactly once |
| 2 | Account has active tasks in Open, In Progress, Waiting on Internal Team, Waiting on Customer, Blocked, and Needs Review | Every task appears; no active lifecycle state is excluded |
| 3 | Account also has Done and Cancelled tasks | Done and Cancelled tasks do not appear in the active cross-check, but their dated evidence may qualify for Last Verified Activity under the activity contract |
| 4 | Ten active tasks exist but only two belong in the customer communication delta | All ten appear in `All Active Associated Tasks`; only the two useful updates may appear in the email |
| 5 | An active task is stale, already communicated, internal-only, or unsuitable for the email | It remains visible in the active cross-check and may be categorized elsewhere in the workspace |
| 6 | No active Airtable Customer Tasks exist | Mandatory block says `None — no active Airtable Customer Tasks found for [Account].`; Account Activity Check still appears |
| 7 | Priorities are stored as P1 - Must do, P2 - Should do, and P3 - Monitor | Display normalizes them to P1, P2, and P3 without changing Airtable |
| 8 | Due Date is before today | Task marker says `overdue Mon DD`, including year when outside the current year; Due Date does not affect Last Verified Activity |
| 9 | Due Date is today | Task marker says `due today`; it does not become activity evidence |
| 10 | Due Date is in the future | Task marker says `due Mon DD`, including year when outside the current year; it does not become activity evidence |
| 11 | Due Date is blank | Task marker says `no due date` |
| 12 | Customer Waiting? is true while Status is In Progress | Display includes a separate `customer waiting` marker and does not relabel Status as Waiting on Customer |
| 13 | Task Status is Waiting on Customer | Display shows the exact lifecycle status `Waiting on Customer` |
| 14 | Strong evidence suggests an active task may be complete | Task remains in the active list with `likely needs check-off review` and is referenced under Possible task check-offs; no write occurs |
| 15 | One Airtable task is linked to the named account and another account | It appears once with `multi-account / needs review` and retains one Airtable record mapping |
| 16 | Notion contains a strong mirror of an active Airtable task | Airtable task appears once; Notion does not create a duplicate active-task line and does not become Last Verified Activity evidence |
| 17 | Notion contains a customer item with no Airtable match | It may be shown separately as `Notion only — consider syncing to Customer Tasks`; it is not counted as an active Airtable task or persisted activity candidate |
| 18 | Airtable pagination or retrieval is incomplete | Relevant block is labeled incomplete; completeness is not claimed |
| 19 | Active Airtable tasks retrieved count differs from displayed lines | Output stops claiming task completeness and identifies unresolved records |
| 20 | Tasks differ in overdue/customer-waiting urgency, priority, due date, and title | Order follows Customer Task Centre within-account ordering |
| 21 | `/account follow-up Kentik and draft the email` | Account Activity Check and complete active-task cross-check appear before the editable customer email |
| 22 | `Draft the email now` after an older workspace and evidence may have changed | Activity recency and active tasks are refreshed and re-displayed unless the user explicitly requests only the email |
| 23 | Explicit `only email` request | Activity calculation plus active-task retrieval/reconciliation happen internally; final output contains only the editable email |
| 24 | User later says `mark task 3 done` | Display number resolves to the internal Airtable record ID and routes to normal Customer Task Centre write handling |
| 25 | Account Follow-Up Builder loads Meeting Follow-Up Email for tone | Supporting skill does not suppress or replace the activity check or active-task cross-check |
| 26 | Normal Account Follow-Up request contains the word email, reply, activity, or latest context | Zero Gmail calls unless the current turn separately and explicitly requests Gmail access |
| 27 | Detailed Note on Aug 8 and Accounts Last Activity Date metadata on Aug 11 | Account Activity Check shows Aug 8 as persisted Last Verified Activity; Aug 11 is only a retrieval signal |
| 28 | Latest qualifying persisted activity is today | Account Activity Check shows `today` and canonical Days Since Activity = 0 |
| 29 | No qualifying persisted dated activity exists | Account Activity Check says `No verified dated activity found`; never substitutes Last Activity Date |
| 30 | Current turn says `I sent the customer email today` and persisted activity is Aug 8 | Persisted activity remains Aug 8; separate `Newer unlogged activity` shows today as current conversation, not yet logged |
| 31 | Current turn contains a draft saying `I will send the customer email` | No newer unlogged activity is shown |
| 32 | Current turn quotes an older email saying `I sent it` | Quoted history does not become newer unlogged activity |
| 33 | Same Airtable snapshot is used in Renewal Focus and Account Follow-Up | Persisted Last Verified Activity Date, Days Since Activity, source choice, and no-evidence result match exactly |

## Required Display Pattern

```text
Account Activity Check
- Last verified Airtable activity: 08 Aug 2026 — 4 days ago
- Source: Detailed Note
- Activity: Customer working session
- Newer unlogged activity: None

All Active Associated Tasks

1. **P1 · Waiting on Customer:** Confirm 18-month DocuSign completion.
2. **P1 · Waiting on Customer:** Publish Campaign 331058 audience workflow.
3. **P1 · Open · overdue Jul 22:** Cancel renewal meeting — likely needs check-off review.
4. **P2 · In Progress · overdue Jul 25:** Deliver customized SBR report and assess recurring scope.
```

The exact task and activity text is data-dependent, but activity evidence, task priority/status/due timing, numbering, completeness, and ordering must follow the canonical contracts and skill.

## Pass Criteria

- Review-first and review-and-draft workspaces begin with the mandatory Account Activity Check followed by the active-task cross-check.
- Persisted Last Verified Activity follows `contracts/account-activity-recency.md` and remains Airtable-derived.
- Current-conversation evidence may appear only as a separately labeled newer unlogged overlay.
- Every active Airtable Customer Task linked to the named account appears exactly once.
- Communication-delta filtering never removes a task from the cross-check.
- Done and Cancelled tasks are excluded from the active cross-check but may provide qualifying historical activity evidence.
- Notion references never inflate the Airtable task count or persisted activity calculation.
- Task numbers remain mapped internally for later natural-language actions.
- `Active Airtable Customer Tasks retrieved = All Active Associated Task lines displayed` before task completeness is claimed.
- Account Follow-Up remains Read / Draft only and does not access Gmail by default.