# V7 Customer Task Centre Renewal Focus Regression Cases — V10 Compatibility

Run these as dry-run scenarios. Do not modify Airtable or invoke Notion, Gmail, Calendar, Slack, or another unrelated connector.

Assume the execution date is evaluated in `Asia/Kolkata`. Apply `contracts/account-activity-recency.md` for activity calculations.

| # | Scenario | Expected result |
|---:|---|---|
| 1 | `/task center renewal focus` | Routes exactly to `skills/customer-task-centre-renewal-focus.md` in Read mode |
| 2 | `/task centre renewal focus` | Routes exactly to the same renewal-focus view |
| 3 | Plain `renewal focus` | Continues to route to Weekly Command Centre, not Customer Task Centre |
| 4 | `/task centre sort: renewal` | Continues to use the normal Customer Task Centre renewal sort, not the dedicated renewal-focus view |
| 5 | Assigned accounts renew today, tomorrow, and in 30 days | All three appear in the main roster in ascending Renewal Date order, with today first |
| 6 | Assigned account has an upcoming Renewal Date but zero active Customer Tasks | Account remains in the main roster, shows `No active Customer Tasks`, and still receives a Last Verified Activity result |
| 7 | Two accounts share the same Renewal Date and have statuses Setup / restore required and Confirmed recurring — fields only | Setup / restore required appears first regardless of activity recency |
| 8 | Three accounts share the same Renewal Date across all three Cadence Field Status values | Order is Setup / restore required → Needs review → Confirmed recurring — fields only |
| 9 | Two accounts share the same Renewal Date and Cadence Field Status | Account Name ascending breaks the tie; Last Verified Activity does not change sort |
| 10 | Meeting Sync is affirmative and frequency is Monthly | Cadence Field Status = `Confirmed recurring — fields only` |
| 11 | Meeting Sync is affirmative and frequency is Paused | Cadence Field Status = `Needs review` |
| 12 | Meeting Sync is blank and frequency is Bi-weekly | Cadence Field Status = `Needs review` |
| 13 | Meeting Sync is no and frequency is TBD / not confirmed | Cadence Field Status = `Setup / restore required` |
| 14 | Account has a past Renewal Date and active tasks | Excluded from main upcoming order and shown under Past-dated renewal exceptions with activity recency calculated |
| 15 | Account has no Renewal Date and active tasks | Excluded from main upcoming order and shown under Missing renewal date / data hygiene with activity recency calculated |
| 16 | Past-dated or missing-date account has no active tasks | Counted in reconciliation but no empty exception row is added |
| 17 | Active task links to two accounts | Appears once in Multi-account / Needs Review using the base Customer Task Centre rules |
| 18 | Active task has no Account link | Appears under Unmapped / Data Hygiene |
| 19 | Upcoming account has several active tasks | Every active task appears with stable account-based references; no `+N more` truncation |
| 20 | View is run with incomplete Airtable pagination | Output is labeled `Incomplete snapshot`; completeness is not claimed |
| 21 | Cadence structured fields look configured and Detailed Notes are retrieved for activity recency | View does not label the account `Fully active` or `Slipping`; Detailed Notes are not reused to upgrade cadence health |
| 22 | Exact renewal-focus trigger is used | Airtable Accounts, Customer Tasks, and Detailed Notes are allowed; Activity notes may be read only through the recency retrieval gate; zero Notion and Gmail calls |
| 23 | User says `mark 2.1 done` after viewing the roster | Route the explicit action to normal Customer Task Centre write handling using the internal Airtable record ID |
| 24 | Renewal Date equals the current date in Asia/Kolkata but is yesterday in another timezone | Include the account because Asia/Kolkata is authoritative |
| 25 | Detailed Note on Aug 8 and Accounts Last Activity Date metadata on Aug 11 | Last Verified Activity = Aug 8 from Detailed Notes; Aug 11 is only a retrieval signal |
| 26 | Task Due Date is Aug 12 but latest qualifying meeting was Aug 8 | Last Verified Activity remains Aug 8; Due Date never counts as activity |
| 27 | Latest qualifying activity is today | Days Since Activity = 0 |
| 28 | No qualifying Detailed Note, task source/completion, or dated Activity-notes event exists | Show `No verified dated activity found` and Days Since Activity = `N/A` |
| 29 | Detailed Note and qualifying task source share the latest date | Detailed Note wins displayed Activity Source by canonical tie-break |
| 30 | Last Activity Date is newer than the best structured candidate | Activity notes may be inspected for a newer explicitly dated qualifying entry; metadata itself still cannot win |
| 31 | Last Activity Date is not newer than the best structured candidate | Do not read full Activity notes merely for recency |
| 32 | Future-dated source claims completed activity | Exclude it from Last Verified Activity and flag Needs review; never show negative Days Since Activity |

## Pass Criteria

- Both exact renewal-focus triggers route to the specialized Customer Task Centre view.
- Plain `renewal focus` remains a Weekly Command Centre trigger.
- Main roster inclusion is Renewal Date greater than or equal to today in Asia/Kolkata.
- Main sort remains `Renewal Date ascending → Cadence Field Status action order → Account Name`.
- Every upcoming assigned account appears, including accounts with no active tasks.
- Every displayed account receives exactly one canonical persisted Last Verified Activity result.
- Last Verified Activity follows `contracts/account-activity-recency.md`; Airtable metadata timestamps and task Due Dates never substitute for activity.
- Past-dated and missing-date records do not contaminate the upcoming order.
- Structured cadence fields are shown without overstating full cadence health.
- Every active task remains represented exactly once across the main roster and exception sections.
- The view remains read-only and Airtable-only; Notion and Gmail remain prohibited.