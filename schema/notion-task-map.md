# 6sense Notion Task Source Map

Use this file for the Notion side of Weekly Command Centre and Customer Task Centre.

## Canonical Pages

- Workspace hub: `6 sense`
- Hub page ID: `2e6ecca2-ea5e-801e-a035-c3ef35345a3e`
- Hub URL: `https://app.notion.com/p/2e6ecca2ea5e801ea035c3ef35345a3e`
- Canonical task page: `Projects & tasks`
- Task page ID: `2e6ecca2-ea5e-8187-a2d4-d05b217c7ec3`
- Task page URL: `https://app.notion.com/p/2e6ecca2ea5e8187a2d4d05b217c7ec3`

Do not use the separate personal `Tasks` page (`abef401a-65e5-4beb-9111-1863aa0bf534`) for 6sense workflows.

## Task Sections

Read open checkboxes and linked task pages from:

1. `High Priority 🚨`
2. `Customer Tasks`
3. `Manager tasks 🚨`
4. `AI OKR Project Tasks`
5. Other active, clearly work-related task groups on the canonical page

Do not surface completed `[x]` items by default. Treat unchecked `[ ]` items as open. A linked child page is open when it contains an unchecked action. Collapsed/toggle content still counts when it is inside an active section.

The generic `Dump` is an inbox, not automatically high priority. Include it only when the user asks for the dump/backlog or when an item has an explicit urgency/due signal.

## Source Ownership

- Airtable Customer Tasks is authoritative for customer/account commitments, customer waiting, support/internal follow-ups tied to an account, due dates, owners, lifecycle state, and completion evidence.
- Notion `Projects & tasks` is authoritative for high-priority internal work, manager actions, administration, enablement, AI/OKR work, and broader 6sense projects.
- A customer item captured only in Notion may be displayed as `Notion only — consider syncing to Customer Tasks`.
- Never silently create a second record or maintain automatic bidirectional sync.

## Unified Deduplication

Before displaying or promoting a Notion customer item, compare it with active Airtable Customer Tasks using:

1. Account
2. Normalized action/object
3. Owner
4. Due date/timing
5. Source context

If a strong Airtable match exists, show one unified item with Airtable as authoritative and label Notion as a mirror/reference. If matching is uncertain, keep both visible under `Needs Source Review`.

## Source-Aware Writes

A read/report request never changes Notion or Airtable.

After an explicit task action:

- Airtable-owned task: update Airtable only.
- Notion-owned task: update the exact checkbox/text on the canonical Notion task page or linked child page only.
- Mirrored task: update the authoritative Airtable task; update Notion only if the user explicitly asks to sync both.
- Ambiguous source or multiple text matches: ask one concise clarification.

To promote a Notion-only customer task into Airtable, require an explicit sync/move/capture request. Deduplicate first, then use Airtable Source Type = `Other`, Last Updated From = `Task Centre`, and include the Notion page URL in Source Summary. Do not create new select options.

After a Notion write, fetch the page again and verify the exact task state.
