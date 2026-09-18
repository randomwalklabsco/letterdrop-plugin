---
description: List the accounts currently showing competitor buying signals, ranked for outreach
argument-hint: "[filter, e.g. 'high priority' or 'signals this month']"
---

List the accounts in the Letterdrop workspace that are showing competitor
buying signals right now.

Filter request from the user: $ARGUMENTS

Use `get_competitor_monitoring_table`. Translate the request above into
`filters` and `search_term` rather than fetching everything and narrowing
afterwards — the filters run server-side across every page. If the request
names a filter value you have not seen in this workspace's data, check
`include: ["filter_options"]` first rather than guessing at it.

Present a compact table: company, competitors, priority, guessed stage, last
activity, CRM deal status, and how many contacts produced signals versus how
many are on the buying committee.

Then call out, in a sentence each:

- any account where `isStale: true` despite a high priority, since the signal
  has aged past the workspace's sales-cycle window
- any account whose records all predate tracking
  (`hasOnlyHistoricInitialConnections: true`), which is not new activity
- how many accounts have had no outreach since the signal landed

Say how many accounts matched in total and whether you are showing all of them
or a first page.
