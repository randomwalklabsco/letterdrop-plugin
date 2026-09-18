---
name: competitor-monitoring
description: Read Letterdrop competitor monitoring data correctly - which accounts are in a competitor's sales cycle, who is on the buying committee, and what the CRM says. Use when answering questions about competitor signals, in-market accounts, buying committees, attribution, or when building an outreach list, CSV or pipeline report from Letterdrop data.
---

# Reading Letterdrop competitor monitoring data

Letterdrop surfaces accounts that look like they are in a competitor's sales
cycle. The data answers two different questions — *what happened* and *what we
are willing to claim about why* — and most mistakes come from reading the
first as if it answered the second.

## Which tool

- `get_competitor_monitoring_table` answers almost everything at the account
  grain: who to contact, what outreach is recommended, and any table, CSV,
  export or report question. Start here.
- `get_competitor_monitoring_account_details` is the drill-down once you have a
  specific company: outreach timeline, classified replies, signals,
  opportunities, full committee.
- `get_knowledge_base_buyer_filters` is the workspace's definition of a buyer —
  company filters and buyer titles. It decides who counts as a valid lead, and
  so who appears as a buying-committee member on every table row; read it
  before explaining why someone is or is not in a committee.
- `list_workspaces` says which workspace this connector is reading. It is
  pinned to one; reconnecting is what switches it.

## Fetching the whole table

`include` decides both shape and cost. The default — `contacts`,
`custom_columns`, `buying_committee` — is what table, CSV and report questions
need. Add `summary` for the metric cards, `summary_details` for the per-deal
rows behind them, `filter_options` for the values a filter accepts,
`engagements` for a contact's full signal history, `profile_details` for role
and biography text.

Follow `pagination.nextOffset` until `pagination.hasMore` is false. Do not
guess offsets, and do not stop at the first page because it looked complete.

Narrow with `filters` and `search_term` rather than fetching everything and
discarding rows — filters run server-side across every page, your pagination
does not. Call `filter_options` before offering a filter value you have not
seen in the data.

`coverage` reports which contact groups the response actually carries. Check it
before concluding an account has no buying committee.

## Four rules that decide whether your answer is true

**1. Historical records are not current activity.** A contact with
`isHistoricInitialConnection: true` (or an account with
`hasOnlyHistoricInitialConnections: true`) is a historical record: it describes
a pre-existing relationship rather than something that happened recently. Its
timestamps are null on purpose. `collectedDate` is when the record entered the
workspace, often recent — never present it as an activity date, and never count
these as evidence of a live cycle.

**2. Buying committee members produced no signal.** `contactType: 'Engaged
Contact'` (`hasOwnSignal: true`) means the signal came from that person
directly. `contactType: 'Buying Committee'` (`hasOwnSignal: false`) means the
opposite: they were identified as a decision-maker at an account whose signals
came from *other* people. Committee entries still carry `competitors` and an
`engagedWith` value because those describe the account. Never say or imply that
a committee member did anything toward that competitor, was contacted by them,
or is known to them.

**3. Attribution is a claim, not an ordering.** `attribution.verdict` says why
a meeting happened. Only `direct_reply` and `driven` credit Letterdrop with
causing it; never report `assisted` or `detected` as sourced pipeline, and
never treat a meeting that merely came after a signal as caused by it — that is
what `competitive_deal` exists to rule out. An empty verdict means no claim is
being made, not that attribution was searched for and missing.
`priorRecentOutreach` is tri-state: `null` means we could not look, not false.

**4. Stale signals keep their priority.** `isStale: true` means the signal aged
past the workspace's sales-cycle window, and it deliberately does not downgrade
`priority`. A stale account still reads `priority: "high"`. Ranking on priority
alone puts a months-old signal at the top of a "who should we contact now"
list.

## Blanks

A blank field usually means something specific, and inventing a value for it is
worse than reporting it:

- Blank `recommendedAction`: the weekly capacity plan allocated no action to
  that contact, or no plan is available. Not "unreachable" or "unqualified".
- Blank custom column: compare the response's `customColumnsUpdatedAt` with the
  account's `customColumnsSyncedConfigAt`. If the account's stamp is missing or
  older, the value is still syncing — say that instead of "no value in the CRM".
- Empty `buyingCommittee`: none was returned, either because `include` did not
  ask or because discovery has not run. Never "this company has no
  decision-makers".
- `existingAccount: null`: the CRM lookup was unavailable. Not the same as
  net-new.

## What this connector cannot do

It reads. It cannot contact anyone on the workspace's behalf, and it cannot
create or run outreach sequences. The only write is the buyer-filter definition, which
changes what every future read of the table returns — confirm the exact titles
with the user before calling it.
