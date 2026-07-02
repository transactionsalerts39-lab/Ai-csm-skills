# AI CSM Skills

This repository is the live source of truth for Ranjodh's CSM AI workflow skills.

The goal is to avoid repeatedly downloading and re-uploading project source `.md` files whenever a workflow changes.

## Operating Model

- GitHub stores the canonical live skill files.
- Project instructions should act as a router.
- Uploaded project Sources should be treated as fallback snapshots only.
- Airtable schema and field IDs should live in `schema/airtable-schema-map.md` so workflow skills do not duplicate schema details unnecessarily.

## Repository Structure

```text
registry/
  skill-registry.md

schema/
  airtable-schema-map.md

router/
  live-skill-router.md

skills/
  docs-assistant.md
  update-activity-notes.md
  meeting-summarizer.md
  weekly-command-centre.md
  support-ticket-creator.md
  meeting-follow-up-email.md
  sf-stage-progression-validator.md
  customer-meeting-prep-brief.md
  clari-weekly-forecast.md
  weekly-highlights-report.md
  manager-weekly-recap.md
  weekly-slack-update.md
  lattice-round-up.md
```

## How to Use

When a workflow trigger is used, the assistant should fetch the relevant file from `skills/` and follow the latest instructions there.

Example:

```text
/update notes -> skills/update-activity-notes.md
/docs -> skills/docs-assistant.md
/weekly command centre -> skills/weekly-command-centre.md
```

## Update Rules

When the Airtable workflow changes:

1. Update `schema/airtable-schema-map.md` first.
2. Update only the affected skill files.
3. Update `registry/skill-registry.md` if trigger behavior or canonical paths change.
4. Do not rely on stale uploaded project sources when a live GitHub skill is accessible.

## Privacy

This repository should remain private because it contains internal workflow logic, Airtable field IDs, customer-success operating rules, and 6sense-specific process details.
