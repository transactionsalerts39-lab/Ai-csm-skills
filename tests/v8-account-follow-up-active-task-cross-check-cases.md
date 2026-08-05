# V8 Account Follow-Up Active Task Cross-Check Regression Cases

Run these as dry-run scenarios. Do not modify Airtable, Notion, Gmail, or another customer system.

Assume dates are evaluated in `Asia/Kolkata`.

| # | Scenario | Expected result |
|---:|---|---|
| 1 | `/account follow-up Boostlingo` with four active Airtable tasks | Workspace begins with `All Active Associated Tasks` and displays all four tasks exactly once |
| 2 | Account has active tasks in Open, In Progress, Waiting on Internal Team, Waiting on Customer, Blocked, and Needs Review | Every task appears; no active lifecycle state is excluded |
| 3 | Account also has Done and Cancelled tasks | Done and Cancelled tasks do not appear in the active cross-check |
| 4 | Ten active tasks exist but only two belong in the customer communication delta | All ten appear in `All Active Associated Tasks`; only the two useful updates may appear in the email |
| 5 | An active task is stale, already communicated, internal-only, or unsuitable for the email | It remains visible in the active cross-check and may be categorized elsewhere in the workspace |
| 6 | No active Airtable Customer Tasks exist | Mandatory block says `None — no active Airtable Customer Tasks found for [Account].` |
| 7 | Priorities are stored as P1 - Must do, P2 - Should do, and P3 - Monitor | Display normalizes them to P1, P2, and P3 without changing Airtable |
| 8 | Due Date is before today | Task marker says `overdue Mon DD`, including year when outside the current year |
| 9 | Due Date is today | Task marker says `due today` |
| 10 | Due Date is in the future | Task marker says `due Mon DD`, including year when outside the current year |
| 11 | Due Date is blank | Task marker says `no due date` |
| 12 | Customer Waiting? is true while Status is In Progress | Display includes a separate `customer waiting` marker and does not relabel Status as Waiting on Customer |
| 13 | Task Status is Waiting on Customer | Display shows the exact lifecycle status `Waiting on Customer` |
| 14 | Strong evidence suggests an active task may be complete | Task remains in the active list with `likely needs check-off review` and is referenced under Possible task check-offs; no write occurs |
| 15 | One Airtable task is linked to the named account and another account | It appears once with `multi-account / needs review` and retains one Airtable record mapping |
| 16 | Notion contains a strong mirror of an active Airtable task | Airtable task appears once; Notion does not create a duplicate active-task line |
| 17 | Notion contains a customer item with no Airtable match | It may be shown separately as `Notion only — consider syncing to Customer Tasks`; it is not counted as an active Airtable task |
| 18 | Airtable pagination or retrieval is incomplete | Block is labeled `Incomplete task cross-check`; completeness is not claimed |
| 19 | Active Airtable tasks retrieved count differs from displayed lines | Output stops claiming completeness and identifies the unresolved records |
| 20 | Tasks differ in overdue/customer-waiting urgency, priority, due date, and title | Order follows Customer Task Centre within-account ordering |
| 21 | `/account follow-up Kentik and draft the email` | Complete active-task cross-check appears before the editable customer email |
| 22 | `Draft the email now` after an older workspace and task evidence may have changed | Active tasks are refreshed and re-displayed before drafting unless the user explicitly requests only the email |
| 23 | Explicit `only email` request | Active-task retrieval and reconciliation happen internally; final output contains only the editable email |
| 24 | User later says `mark task 3 done` | Display number resolves to the internal Airtable record ID and routes to normal Customer Task Centre write handling |
| 25 | Account Follow-Up Builder loads Meeting Follow-Up Email for tone | Supporting skill does not suppress or replace the active-task cross-check |
| 26 | Normal Account Follow-Up request contains the word email or reply | Zero Gmail calls unless the current turn separately and explicitly requests Gmail access |

## Required Display Pattern

```text
All Active Associated Tasks

1. **P1 · Waiting on Customer:** Confirm 18-month DocuSign completion.
2. **P1 · Waiting on Customer:** Publish Campaign 331058 audience workflow.
3. **P1 · Open · overdue Jul 22:** Cancel renewal meeting — likely needs check-off review.
4. **P2 · In Progress · overdue Jul 25:** Deliver customized SBR report and assess recurring scope.
```

The exact task text is data-dependent, but priority, status, due timing, numbering, completeness, and ordering must follow the canonical skill.

## Pass Criteria

- Review-first and review-and-draft workspaces always begin with the mandatory active-task cross-check.
- Every active Airtable Customer Task linked to the named account appears exactly once.
- Communication-delta filtering never removes a task from the cross-check.
- Done and Cancelled tasks are excluded.
- Notion references never inflate the Airtable task count.
- Task numbers remain mapped internally for later natural-language actions.
- `Active Airtable Customer Tasks retrieved = All Active Associated Task lines displayed` before completeness is claimed.
- Account Follow-Up remains Read / Draft only and does not access Gmail by default.
