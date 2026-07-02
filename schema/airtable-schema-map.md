# Airtable Schema Map

This file is the shared schema reference for Ranjodh's CSM workflow skills.

## Base

| Item | Value |
|---|---|
| Base name | Book of Business Management |
| Base ID | `app6O8peF5ywLe1GM` |

## Accounts Table

| Item | Value |
|---|---|
| Table name | Accounts |
| Table ID | `tblr6UnvfaqfNvwyU` |

### Core Account Fields

| Business Name | Airtable Field Name | Field ID | Type | Usage |
|---|---|---|---|---|
| Account Name | Account Name | `fldOSLvopNOX6ae3Z` | singleLineText | Primary account match field |
| Engagement Status | Engagement Status | `fldyrxDGOzWF3c7wm` | singleSelect | Primary account relationship / engagement state |
| Outreach Step | Outreach Step | `fldhX3nTqX4a2eKt8` | singleSelect | Primary outreach/follow-up workflow step |
| Activity Notes | Activity notes | `flddz3lqUmEhLhmN5` | multilineText | Running account notes field |
| Stage | Stage | `fldTYypHjPaFcatCi` | multipleSelects | Legacy fallback only; do not update unless explicitly asked |
| ACV | ACV | `fldjieKzPumeF6afD` | currency | Account value |
| Renewal Date | Renewal Date | `fldPmw5pHDNDgZYgA` | date | Renewal prioritization |
| Current Active CSM | Curent Active CSM | `fldTQWeUcqj5HQoAH` | singleSelect | Filter to Ranjodh when needed. Airtable field name is misspelled as `Curent`. |
| Last Activity Date | Last Activity Date | `fld2jD1HJm9RRwNBW` | lastModifiedTime | Airtable record-touch signal, not necessarily customer activity |
| Churn Risk | Chrun riks | `fldy4GIC8xDuPjS8y` | singleSelect | Risk signal. Airtable field name is misspelled as `Chrun riks`. |
| Meeting Sync established | Meeting Sync established | `fld8kFpch7M4wGfpQ` | singleSelect | Use `yes` only when recurring customer sync/cadence is confirmed |
| Cadence Frequency | Cadence Frequency | `flddcR78KacwfLyk5` | singleSelect | Confirmed frequency of established customer cadence |
| Upsell Opportunity | Upsell opportunity | `fldMq113Hz7xrQ6GK` | singleSelect | Upsell signal |
| Instance Link | Instance Link | `fldhc7LZLPB3b14fU` | url | 6sense instance link |
| Key Contacts | Key Contacts | `fldnju5ZSawYE3taI` | multilineText | Combined customer contact details |
| Account Transition To | Account Transition To | `fldbLaa0QadjDeG9g` | singleSelect | Transition owner |
| Activity Log | Activity Log | `fldzKlsKjWYFGX4Q8` | multipleRecordLinks | Linked Activity/Notes records |
| Task Status | Task status | `fldaYegYsT0eA3NAK` | singleSelect | Open / yet to start / closed |
| Autorenewal | Autorenewal | `fldG1VI1NcrQPGigz` | singleSelect | Yes/No auto-renewal status |
| Entitlements | Entitlements | `fldakm251jNyVn9up` | multipleSelects | Account entitlements |

### Clari / Renewal Forecast Fields

| Business Name | Airtable Field Name | Field ID | Usage |
|---|---|---|---|
| Clari Opportunity ID | Clari Opportunity ID | `fld0CVHEgOsjMb4mv` | Stable matching key for Clari/Salesforce export |
| Clari Opportunity Name | Clari Opportunity Name | `fldrL1dKDTKQ3eiRS` | Latest opportunity name |
| Clari Stage | Clari Stage | `fldzoYzhcbeiezuRU` | Opportunity stage from Clari/Salesforce |
| Renewal Forecast Category | Renewal Forecast Category | `fld6UN3RDjXxMeqkV` | Current forecast category |
| Baseline ACV | Baseline ACV | `fld3bz5DgDfcgofmN` | Base renewal ACV |
| Expected Renewal ACV | Expected Renewal ACV | `fldCr0T8tbiUeYb8p` | Projected renewal amount |
| Churn / Downsell Risk Amount | Churn / Downsell Risk Amount | `fldu508bgwXqdKmUv` | Churn/downsell exposure |
| Churn / Downsell Risk Category | Churn / Downsell Risk Category | `fldxChoxMzVCy5yFT` | Risk category |
| Churn / Downsell Risk Reason | Churn / Downsell Risk Reason | `fldxrAV62YcLeRmKG` | Risk reason |
| Current Renewal Risk Notes | Current Renewal Risk Notes | `fldGUYsDlKG1Orrcf` | Current forecast/risk baseline |
| Last Clari Sync Date | Last Clari Sync Date | `fldis0hysSxrlNN84` | Latest Clari sync timestamp |
| Clari CSV Snapshot Link | Clari CSV Snapshot Link | `fldKaeaYUgsfPfMZj` | Optional CSV/source snapshot |
| Instance Link Text | Instance Link Text | `fldNPjlEgZRGPC8BS` | Text copy of instance link/name |

## Activity/Notes Table

| Item | Value |
|---|---|
| Table name | Activity/Notes |
| Table ID | `tblI5cCnIY63S6pZq` |

| Business Name | Airtable Field Name | Field ID | Type | Usage |
|---|---|---|---|---|
| Notes | Notes | `fldcfsEsMgFHoB8VH` | multilineText | Main note body |
| Title | Title | `fldbf738tn5U18z7D` | singleLineText | Activity title |
| Account | Account | `fldkJTerhbTfcObzR` | multipleRecordLinks | Linked account |
| Activity Type | Activity Type | `fldfi0aH7CncdNtb9` | singleSelect | Meeting Notes / Email / etc. |
| Date | Date | `fld5Dd0gO8vHadVjl` | date | Activity date |
| Logged By | Logged By | `fldVqzemKONTV2ZAE` | singleCollaborator | Logger |
| Next Steps | Next Steps | `flduD4nsj6ZkEXmLZ` | multilineText | Structured follow-up |
| Attachments | Attachments | `fld915H2Eg0PFFMfx` | multipleAttachments | Supporting files |
| Last activity date | Last activity date | `fldYG5croDSgYQmNN` | lastModifiedTime | Activity record update signal |

## Engagement Status Values

Use only these exact values:

- `Transitioned - not contacted`
- `Intro sent - waiting`
- `Customer replied - scheduling`
- `Meeting scheduled`
- `Connected - no cadence`
- `Cadence established`
- `No response - follow-up needed`
- `Multithread required`
- `Support / blocker active`
- `Parked / no active outreach`
- `Churn / offboarding`

## Outreach Step Values

Use only these exact values:

- `Not started`
- `Intro sent`
- `FU 1`
- `FU 2`
- `FU 3`
- `FU 4`
- `Multithread`
- `Customer replied`
- `Meeting scheduled`
- `Cadence active`
- `Parked`

## Meeting Sync Established Values

Current known value:

- `yes`

Use only when recurring cadence/sync is confirmed. Do not use for one-off meetings.

## Cadence Frequency Values

Use only these exact values:

- `Weekly`
- `Bi-weekly`
- `Monthly`
- `Quarterly`
- `Ad hoc / as needed`
- `Paused`
- `TBD / not confirmed`

## Schema Change Rule

When Airtable changes:

1. Update this file first.
2. Update affected skill files second.
3. Update `registry/skill-registry.md` only if trigger routing or canonical paths change.
