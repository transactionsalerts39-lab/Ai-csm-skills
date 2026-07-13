# Skill Registry

Exact slash commands win over natural-language matching. Use `routing/skill-router.md` for precedence and ambiguity rules.

| Workflow | Primary triggers | Canonical path | Default mode |
|---|---|---|---|
| Docs / RevCity Assistant | `/docs`, `/support docs`, `/revcity`, `/product updates` | `skills/docs-assistant.md` | Read / Draft |
| Update Activity Notes | `/update notes`, `/un`, `update notes`, `log this` | `skills/update-activity-notes.md` | Write |
| Meeting Summarizer | `/meeting summarizer`, `/meeting summa`, `/meeting notes`, `/call summary` | `skills/meeting-summarizer.md` | Write for explicit command; otherwise Preview |
| Weekly Command Centre | `/weekly command centre`, `/weekly tasks`, `stale accounts`, `renewal focus` | `skills/weekly-command-centre.md` | Read; conditional write |
| Customer Task Centre | `/task manager`, `/task centre`, `/task center`, `/task command centre`, `/task command center`, `/tasks`, `/high priority tasks`, `mark as done`, `close task`, `reopen task` | `skills/customer-task-centre.md` | Read; conditional write |
| Support Ticket Creator | `/support ticket`, `/ticket`, `/case creator` | `skills/support-ticket-creator.md` | Draft |
| Meeting Follow-Up Email | `/meeting follow-up`, `/follow-up email`, `only email` | `skills/meeting-follow-up-email.md` | Draft |
| SF Stage Progression Validator | `/sf stage progression`, `/stage check`, `/move opportunity` | `skills/sf-stage-progression-validator.md` | Read / Draft |
| Customer Meeting Prep Brief | `/meeting prep`, `/meet prep`, `/prep call`, `/customer prep` | `skills/customer-meeting-prep-brief.md` | Read |
| Clari Weekly Forecast | `/clari forecast`, `/clari weekly forecast`, `/forecast prep` | `skills/clari-weekly-forecast.md` | Read / Draft |
| Weekly Highlights Report | `/weekly highlights`, `/weekly report` | `skills/weekly-highlights-report.md` | Read / Draft |
| Manager Weekly Recap | `/manager weekly recap`, `/manager recap`, `Emily update` | `skills/manager-weekly-recap.md` | Read / Draft |
| Weekly Slack Update | `/weekly slack update`, `/slack update` | `skills/weekly-slack-update.md` | Read / Draft |
| Lattice Round-Up | `/lattice`, `/lattice round up`, `/lattice roundup`, `performance round-up` | `skills/lattice-round-up.md` | Read / Draft |
