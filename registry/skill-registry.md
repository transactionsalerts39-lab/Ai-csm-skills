# Skill Registry

This registry maps Ranjodh's workflow triggers to canonical live skill files in this repository.

## Source Priority

1. Use the canonical GitHub skill file listed below.
2. Use uploaded project Sources only as fallback snapshots if GitHub cannot be fetched.
3. If a live skill file cannot be fetched, say so clearly before using a fallback.

## Canonical Skills

| Workflow | Primary triggers | Canonical path | Notes |
|---|---|---|---|
| Docs / RevCity Assistant | `/docs`, `/support docs`, `/revcity`, `/product updates`, `/customer-safe doc answer` | `skills/docs-assistant.md` | Uses official 6sense Support Docs first for product behavior and RevCity for product updates/use cases. |
| Update Activity Notes | `/update notes`, `update notes`, `log this`, `append activity` | `skills/update-activity-notes.md` | Appends Airtable activity notes and updates Engagement Status, Outreach Step, Meeting Sync established, and Cadence Frequency when supported. |
| Meeting Summarizer | `/meeting summarizer`, `/meeting notes`, `/call summary`, `/transcript summary` | `skills/meeting-summarizer.md` | Creates CRM note, Activity/Notes record, customer follow-up email, and account status/cadence updates. |
| Weekly Command Centre | `/weekly command centre`, `/weekly`, `accounts to focus on`, `stale accounts`, `renewal focus` | `skills/weekly-command-centre.md` | Produces weekly task catch-up, renewal focus window, status hygiene, and cadence hygiene. |
| Support Ticket Creator | `/support ticket`, `/ticket`, `/case creator`, `/bug ticket` | `skills/support-ticket-creator.md` | Creates clean support tickets inline only. |
| Meeting Follow-Up Email | `/meeting follow-up`, `/follow-up email`, `/recap email`, `only email` | `skills/meeting-follow-up-email.md` | Creates warm customer-facing meeting recap emails. |
| SF Stage Progression Validator | `/sf stage progression`, `/stage check`, `/validate stage`, `/move opportunity` | `skills/sf-stage-progression-validator.md` | Validates Salesforce opportunity stage readiness; Salesforce is system of record. |
| Customer Meeting Prep Brief | `/meeting prep`, `/prep call`, `/customer prep`, `/pre-call brief` | `skills/customer-meeting-prep-brief.md` | Pulls Airtable account context into a short pre-meeting brief. |
| Clari Weekly Forecast | `/clari forecast`, `/forecast prep`, `/weekly clari update` | `skills/clari-weekly-forecast.md` | Generates Clari forecast update recommendations from CSV + Airtable. |
| Weekly Highlights Report | `/weekly highlights`, `/weekly report`, `/highlights report` | `skills/weekly-highlights-report.md` | Generates weekly highlights/lowlights report. |
| Manager Weekly Recap | `/manager weekly recap`, `/manager recap`, `/manager update`, `Emily update` | `skills/manager-weekly-recap.md` | Curated manager-facing weekly account recap. |
| Weekly Slack Update | `/weekly slack update`, `/slack update` | `skills/weekly-slack-update.md` | Generates weekly Slack-ready update. |
| Lattice Round-Up | `/lattice`, `/lattice round up`, `/performance roundup` | `skills/lattice-round-up.md` | Performance review / Lattice summary workflow. |

## Shared Schema

Airtable field IDs, account table IDs, and cadence fields are tracked in:

```text
schema/airtable-schema-map.md
```

When Airtable structure changes, update the schema map first, then update only the affected skills.
