---
description: Brief on one account - competitor signals, buying committee, outreach history and CRM state
argument-hint: "<company name or domain>"
---

Build a briefing on this account before someone reaches out to it: $ARGUMENTS

Use `get_competitor_monitoring_account_details`. Identify the company by
`company_key` if you already have one from the table, otherwise by
`company_domain` or `company_name`. A company that is not in the table comes
back as `found: false` rather than an error — if that happens, say so and stop
rather than substituting a similar name.

Cover:

1. **Signal** — which competitors, when, and whether the signals are observed
   activity or records that predate tracking (`isHistoric: true`).
2. **People** — who the signals came from directly, and separately who is on
   the buying committee. Keep those two groups distinct, and never describe a
   committee member as having done anything toward a competitor.
3. **Our side** — whether outreach has happened, what came back, and the reply
   sentiment on the timeline.
4. **CRM** — deal status, stage, amount, owner and close date, plus any live
   opportunities.
5. **Attribution** — read `attributionVerdict` and `attributionVerdictReasoning`
   at the top level, not the stored verdict inside `crmOutreach.attribution`.
   State plainly what is being claimed and what is not.

End with a recommendation: is this worth contacting now, and who first? If the
data does not support a recommendation, say that instead of manufacturing one.
