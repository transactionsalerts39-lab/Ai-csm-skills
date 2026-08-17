# V12 Outbound Copy No-Em-Dash Regression Cases

Run these as dry-run scenarios. Do not send, post, submit, or modify any external system while evaluating them.

Canonical rule:

- `contracts/write-safety.md` → Outbound Copy Typography
- `project/chatgpt-project-instructions.md` → Editable Draft Rule → Outbound no-em-dash rule

The forbidden character inside a sendable artifact is the Unicode em dash `—` (U+2014).

| # | Scenario | Expected result |
|---:|---|---|
| 1 | Draft an external customer email whose natural first draft would say `Thanks for the update — I will follow up tomorrow.` | Rewrite without U+2014, for example with a period, comma, or colon; email subject and body both contain zero em dashes |
| 2 | Reply to a customer email that itself contains em dashes | Source punctuation may inform meaning, but the outbound reply contains zero em dashes |
| 3 | Draft a Slack message for an internal account channel | Entire `chat_message` artifact contains zero em dashes |
| 4 | Draft a Teams message | Entire sendable message contains zero em dashes |
| 5 | Draft an SMS or WhatsApp-style message | Entire sendable message contains zero em dashes |
| 6 | Draft a LinkedIn DM | Entire sendable message contains zero em dashes |
| 7 | Draft a LinkedIn post or comment | Entire sendable artifact contains zero em dashes |
| 8 | `/support ticket` generates Subject and Description | Subject and Description contain zero em dashes even if source evidence used them |
| 9 | `/meeting follow-up` generates an email | Editable email block contains zero em dashes |
| 10 | `/account follow-up [Account] and draft the email` | Workspace analysis may use normal internal punctuation, but the final editable customer email contains zero em dashes |
| 11 | `/weekly slack update` generates a Slack draft | Final editable Slack artifact contains zero em dashes |
| 12 | `/manager recap` generates sendable manager copy | Final sendable draft contains zero em dashes |
| 13 | User asks for three email options | Every option inside the same editable email block contains zero em dashes, including all option subjects |
| 14 | User asks to preserve the tone of pasted text containing `—` | Preserve tone and meaning, but normalize punctuation in the outbound artifact |
| 15 | Supporting skill example contains an em dash | Shared Outbound Copy Typography rule wins; final sendable copy contains zero em dashes |
| 16 | Email subject would naturally contain an em dash | Rewrite the subject with a colon, hyphen, comma, or other natural punctuation |
| 17 | Bullet list inside an outbound email uses em dashes as separators | Rewrite every bullet so no U+2014 remains |
| 18 | Closing sentence contains an em dash | Rewrite before rendering the writing block |
| 19 | Internal analysis or task table contains an em dash but is not sendable copy | No failure solely from internal/non-sendable analysis; the prohibition is scoped to outbound artifacts |
| 20 | User explicitly asks to include an em dash in an outbound message | Current project rule still forbids U+2014 unless the user explicitly changes or revokes the project rule itself |
| 21 | En dash appears in a genuine range such as `Aug 10–12` | Allowed by this rule because U+2013 is not U+2014 and the punctuation is a genuine range |
| 22 | Hyphen appears in a compound such as `follow-up` | Allowed |
| 23 | Model tries to replace an em dash stylistically with an en dash between clauses | Rewrite with normal sentence punctuation instead; do not use an en dash as an em-dash substitute |
| 24 | Exact technical source string contains an em dash but does not need verbatim preservation | Normalize it before including it in the sendable artifact |
| 25 | Exact technical string containing an em dash must remain verbatim for evidence | Keep the literal evidence outside the sendable draft when possible; the outbound artifact itself still contains zero U+2014 characters |
| 26 | Final artifact is rendered in an editable writing block | Perform a final character-level scan after all drafting/rewrite steps and before display |

## Required Final Gate

For every complete sendable artifact:

1. Generate or revise the content.
2. Inspect subject, body, bullets, greeting, closing, quoted/rephrased text, and every option when multiple options are present.
3. Search for Unicode U+2014.
4. If count is greater than zero, rewrite the affected sentence and scan again.
5. Render only when U+2014 count = 0.

## Pass Criteria

- Every email, Slack, Teams, SMS, LinkedIn, support-ticket, calendar-response, and other sendable draft contains zero U+2014 characters.
- The rule applies to subjects as well as bodies.
- Source text and supporting-skill examples cannot introduce an em dash into outbound copy.
- Multiple options are all independently clean.
- En dashes remain permitted only for legitimate range usage and are not used as stylistic substitutes.
- Normal hyphens remain permitted.
- Editable writing-block behavior remains unchanged for supported messaging drafts.
- No external system is written during these regression tests.
