# V7 Customer Task Centre Renewal Focus Regression Cases

Run these as dry-run scenarios. Do not modify Airtable or invoke Notion, Gmail, Calendar, Slack, or another unrelated connector.

Assume the execution date is evaluated in `Asia/Kolkata`.

| # | Scenario | Expected result |
|---:|---|---|
| 1 | `/task center renewal focus` | Routes exactly to `skills/customer-task-centre-renewal-focus.md` in Read mode |
| 2 | `/task centre renewal focus` | Routes exactly to the same renewal-focus view |
| 3 | Plain `renewal focus` | Continues to route to Weekly Command Centre, not Customer Task Centre |
| 4 | `/task centre sort: renewal` | Continues to use the normal Customer Task Centre renewal sort, not the dedicated renewal-focus view |
| 5 | Assigned accounts renew today, tomorrow, and in 30 days | All three appear in the main roster in ascending Renewal Date order, with today first |
| 6 | Assigned account has an upcoming Renewal Date but zero active Customer Tasks | Account remains in the main roster and shows `No active Customer Tasks` |
| 7 | Two accounts share the same Renewal Date and have statuses Setup / restore required and Confirmed recurring — fields only | Setup / restore required appears first |
| 8 | Three accounts share the same Renewal Date across all three Cadence Field Status values | Order is Setup / restore required → Needs review → Confirmed recurring — fields only |
| 9 | Two accounts share the same Renewal Date and Cadence Field Status | Account Name ascending breaks the tie |
| 10 | Meeting Sync is affirmative and frequency is Monthly | Cadence Field Status = `Confirmed recurring — fields only` |
| 11 | Meeting Sync is affirmative and frequency is Paused | Cadence Field Status = `Needs review` |
| 12 | Meeting Sync is blank and frequency is Bi-weekly | Cadence Field Status = `Needs review` |
| 13 | Meeting Sync is no and frequency is TBD / not confirmed | Cadence Field Status = `Setup / restore required` |
| 14 | Account has a past Renewal Date and active tasks | Excluded from main upcoming order and shown under Past-dated renewal exceptions |
| 15 | Account has no Renewal Date and active tasks | Excluded from main upcoming order and shown under Missing renewal date / data hygiene |
| 16 | Past-dated or missing-date account has no active tasks | Counted in reconciliation but no empty exception row is added |
| 17 | Active task links to two accounts | Appears once in Multi-account / Needs Review using the base Customer Task Centre rules |
| 18 | Active task has no Account link | Appears under Unmapped / Data Hygiene |
| 19 | Upcoming account has several active tasks | Every task appears with stable account-based references; no `+N more` truncation |
| 20 | View is run with incomplete Airtable pagination | Output is labeled `Incomplete snapshot`; completeness is not claimed |
| 21 | Cadence structured fields look configured but no meeting evidence is retrieved | View does not label the account `Fully active` or `Slipping`; it uses fields-only status |
| 22 | Exact renewal-focus trigger is used | Airtable Accounts and Customer Tasks only; zero Notion and Gmail calls |
| 23 | User says `mark 2.1 done` after viewing the roster | Route the explicit action to normal Customer Task Centre write handling using the internal Airtable record ID |
| 24 | Renewal Date equals the current date in Asia/Kolkata but is yesterday in another timezone | Include the account because Asia/Kolkata is authoritative |

## Pass Criteria

- Both exact renewal-focus triggers route to the specialized Customer Task Centre view.
- Plain `renewal focus` remains a Weekly Command Centre trigger.
- Main roster inclusion is Renewal Date greater than or equal to today in Asia/Kolkata.
- Main sort is `Renewal Date ascending → Cadence Field Status action order → Account Name`.
- Every upcoming assigned account appears, including accounts with no active tasks.
- Past-dated and missing-date records do not contaminate the upcoming order.
- Structured cadence fields are shown without overstating full cadence health.
- Every active task remains represented exactly once across the main roster and exception sections.
- The view remains read-only and Airtable-only.