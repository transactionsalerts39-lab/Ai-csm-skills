# AI CSM Skills

This repository is the live source of truth for Ranjodh's CSM AI workflow skills.

- `skills/` contains canonical workflow files.
- `contracts/` contains shared lifecycle, fiscal-calendar, write-safety, portfolio-scope, and external-input rules.
- `schema/airtable-schema-map.md` contains shared Airtable schema references.
- `schema/notion-task-map.md` defines the canonical 6sense Notion task source and Airtable/Notion ownership boundary.
- `registry/skill-registry.md` maps triggers, paths, and default operating modes.
- `routing/skill-router.md` defines routing precedence and write boundaries.
- `project/chatgpt-project-instructions.md` is the version-controlled mirror of the ChatGPT Project Settings instructions.
- `tests/` contains dry-run regression cases for high-risk workflows.

When a skill conflicts with a shared contract, the shared contract wins.
