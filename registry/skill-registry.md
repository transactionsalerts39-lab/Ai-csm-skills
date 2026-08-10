# Skill Registry

Exact slash commands win over natural-language matching. Use `routing/skill-router.md` for precedence and ambiguity rules.

| Workflow | Primary triggers | Canonical path | Default mode |
|---|---|---|---|
| Docs / RevCity / 6sense.com Assistant | `/docs`, `/support docs`, `/revcity`, `/product updates`, `/6sense articles` | `skills/docs-assistant.md` | Read / Draft |
| Update Activity Notes | `/update notes`, `/un`, `update notes`, `log this` | `skills/update-activity-notes.md` | Write |
| Meeting Summarizer | `/meeting summarizer`, `/meeting summa`, `/meeting notes`, `/call summary` | `skills/meeting-summarizer.md` | Write for explicit command; otherwise Preview |
| Account Adoption Strategy | `/account strategy`, `/adoption plan`, `/customer adoption plan`, `/success plan`, `/use case plan`, `/refresh adoption plan`, `/publish adoption plan` | `skills/customer-adoption-strategy.md` | Read / Draft; conditional Notion write |
| Weekly Command Centre | `/weekly command centre`, `/weekly command center`, exact `/weekly command center - dashboard`, `/weekly tasks`, `internal tasks`, `manager tasks`, `AI/OKR tasks`, `stale accounts`, `renewal focus` | `skills/weekly-command-centre.md` | Read; conditional write; dashboard is read-only |
| CSM Sentiment Notes | `/csm sentiment`, `/weekly csm sentiment`, `/sentiment notes`, `prepare CSM sentiment notes`, `update CSM sentiment notes` | `skills/csm-sentiment-notes.md` | Draft; conditional write |
| PS Adoption Audit | `/ps adoption audit`, `/ps audit`, `/professional services audit`, `/adoption services audit`, `identify PS candidates`, `which accounts need PS` | `skills/ps-adoption-audit.md` | Read / Draft |
| Cadence Coverage Radar | `/cadence coverage`, `/cadence radar`, `/book of business cadence`, `/bob cadence`, `cadence coverage`, `all assigned accounts cadence` | `skills/cadence-coverage-radar.md` | Read |
| Customer Task Centre — Renewal Focus | `/task center renewal focus`, `/task centre renewal focus` | `skills/customer-task-centre-renewal-focus.md` | Read |
| Customer Task Centre | `/task manager`, `/task centre`, `/task center`, `/task command centre`, `/task command center`, `/task centre compact`, `/task centre detailed`, `/task centre history`, `/task centre sort: renewal`, `/task centre sort: due`, `/task centre sort: priority`, `/task centre priority: P1`, `/task centre: [Account]`, `/customer tasks`, `/open customer tasks`, `/task hygiene`, `mark customer task as done`, `close customer task`, `reopen customer task` | `skills/customer-task-centre.md` | Read; conditional Airtable write |
| Account Follow-Up Builder | `/account follow-up`, `/follow-up builder`, `/account email prep`, `/pending follow-up` | `skills/account-follow-up-builder.md` | Read / Draft |
| Support Ticket Creator | `/support ticket`, `/ticket`, `/case creator` | `skills/support-ticket-creator.md` | Draft |
| Meeting Follow-Up Email | `/meeting follow-up`, `/follow-up email`, `only email` | `skills/meeting-follow-up-email.md` | Draft |
| SF Stage Progression Validator | `/sf stage progression`, `/stage check`, `/move opportunity` | `skills/sf-stage-progression-validator.md` | Read / Draft |
| Customer Meeting Prep Brief | `/meeting prep`, `/meet prep`, `/prep call`, `/customer prep` | `skills/customer-meeting-prep-brief.md` | Read |
| Clari Weekly Forecast | `/clari forecast`, `/clari weekly forecast`, `/weekly clari forecast`, `/forecast prep` | `skills/clari-weekly-forecast.md` | Read / Draft |
| Weekly Highlights Report | `/weekly highlights`, `/weekly report` | `skills/weekly-highlights-report.md` | Read / Draft |
| Manager Weekly Recap | `/manager weekly recap`, `/manager recap`, `Emily update` | `skills/manager-weekly-recap.md` | Read / Draft |
| Weekly Slack Update | `/weekly slack update`, `/slack update` | `skills/weekly-slack-update.md` | Read / Draft |
| Lattice Round-Up | `/lattice`, `/lattice round up`, `/lattice roundup`, `performance round-up` | `skills/lattice-round-up.md` | Read / Draft |
