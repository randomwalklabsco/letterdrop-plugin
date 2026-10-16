---
name: signal-based-outreach
description: Daily competitor-signal outbound engine. Starts with a short questionnaire, researches the user's ICP and buyer personas, drafts problem-centric email templates (with an offer and a follow-up) per persona for in-chat approval, then runs on a daily schedule — pulls new leads from Letterdrop competitor monitoring, finds their email through the connected outreach tool (Smartlead, Apollo, HubSpot, etc.), personalizes subject + email + follow-up from the approved templates using only verified data, and pushes each lead into the connected tool's sequence. Use this whenever the user asks to set up signal-based or competitor-signal outreach, build outreach templates, define their ICP or personas, run/refresh the daily Letterdrop check, draft outreach emails from competitor signals, or "push today's leads to the sequence" — even without the words ICP, persona, or Letterdrop. For reading or listing in-market accounts and their contacts without drafting or pushing outreach, use competitor-monitoring instead.
---

# Signal-Based Outreach

Set up once via a questionnaire and an approved template library, then
run every day: **Letterdrop competitor signals → shortlist → email found
via the connected outreach tool → personalized email → pushed to that
tool's sequence → digest.**

The `competitor-monitoring` skill defines what every Letterdrop field
means; this skill only restates the rules that change who gets emailed
and what the email may claim. When the two disagree, that skill wins.

## When the skill starts: the questionnaire
On the first run (no `outreach_config.json` yet), ask these before doing
anything else. Ask them in one message where possible, and use tappable
options if the interface supports them. Store every answer in
`outreach_config.json` so it's never re-asked.

1. **Your company** — name and website. (Used to research ICP and
   personas.)
2. **Letterdrop workspace** — call `list_workspaces` and confirm the
   active one is the workspace they want; store its id. The connector is
   pinned to the workspace chosen when it was authorized and cannot
   switch: if they want another one, tell them to reconnect Letterdrop
   (`/mcp` in Claude Code, or the connector settings) and pick it there,
   then stop until they have.
3. **Outreach tool** — which connected tool holds your sequences:
   Smartlead, Apollo, HubSpot, or another. Detect what's actually
   connected (tool-search the name; then Composio) and offer only those;
   if none is connected, offer to connect one and stop until it is.
4. **Sequence/campaign** — which existing sequence in that tool to push
   leads into. List the tool's sequences and let them pick. Never create
   one automatically.
5. **Send mode** — `auto` (active sequence; sends on the tool's schedule)
   or `review` (paused sequence / drafts a human releases). Suggest
   `review` for the first week if they're unsure.
6. **Daily cap** — max leads to push per day. Suggest 25–50.
7. **Who to target** — which Letterdrop priorities to include (default
   high + medium), and whether to include buying-committee
   decision-makers who didn't personally engage (default yes).
8. **Digest** — where to send the daily summary: a Slack channel/DM, or
   just in chat.
9. **Where to keep state** — a Google Drive folder (if connected) or a
   local path for `outreach_config.json`, `outreach_checkpoint.json`, and
   the template library.

Then run **Setup** (below). On every later run, read the config and skip
straight to **Daily run**. If a stored id (workspace, sequence) no longer
resolves, re-ask just that item.

---

## Setup: ICP research and approved templates (once)

### S1 — Research the ICP and personas
- `web_search` / `web_fetch` the company's site: product pages, pricing,
  case studies, testimonials.
- Ground it in Letterdrop: `get_knowledge_base_buyer_filters` (the
  configured decision-maker and individual-contributor
  titles/seniorities) and
  `get_competitor_monitoring_table` with `best_fit: "best_fit"`,
  `limit: 25` to see which accounts Letterdrop's ICP assessment marks as
  a fit and why (`bestFitReasoning`). Reconcile with web research; flag
  disagreements rather than silently picking one.
- Produce **4–6 buyer personas**: title variants, seniority/function, and
  2–3 specific problems the product solves for them, in the language of
  real case studies where possible.

### S2 — Draft templates per persona
For each persona:
1. **Signal-triggered email** — opens on a `{{signal}}` placeholder,
   leads with the persona's pain as an observation or question, under
   ~120 words.
2. **Evergreen email** — same structure, no signal dependency.
3. **One follow-up** — 2–4 sentences, references the first email.

Every email ends with **one concrete, low-friction offer** (a 15-minute
walkthrough, a teardown of their current setup, a specific resource).
"Let me know if interested" is not an offer. Use `{{placeholders}}` for
per-lead fields; never hardcode invented personal facts into a template.
Voice: direct, specific, no "hope this finds you well," no superlatives.

### S3 — Approve in chat
Show every persona and template in the reply, grouped and labeled. Ask:
"Anything to change before I save these as the approved set?" **Nothing is
saved until the user approves.** Apply edits and re-show only what
changed.

### S4 — Save
Save the approved library as a Google Doc titled
`"[Company] – ICP & Persona Email Templates"` (or a markdown file if
Drive isn't connected) in the state folder; record its location in the
config. Re-run Setup when positioning or personas change.

---

## Daily run

### D0 — Every run
Read `outreach_config.json` and `outreach_checkpoint.json`. Confirm the
Letterdrop connector is available and call `list_workspaces`: if the
active workspace is not the stored id, stop and tell the user to
reconnect Letterdrop and pick it — never read another workspace's
signals into this workspace's sequence. Read `references/<tool>.md` for
the configured outreach tool before any write call. Never fall back to
browsing Letterdrop's web app or scraping.

### D1 — Pull new signals from Letterdrop
Call `get_competitor_monitoring_table` narrowly (each page is a full
table read):
- `last_activity_within_days: 2` (widen to 7 on the first run or after a
  missed day)
- `outreach_status: ["__no_outreach__"]`
- `best_fit: "best_fit"`, `priority:` the array from config (any of
  `high`, `medium`, `low`, `nurture`)
- `include: ["contacts","engagements","profile_details","buying_committee"]`
- `limit: 25`; while `pagination.hasMore` is true, request the next page
  with `offset: pagination.nextOffset`, and stop once you have today's
  cap plus a margin.

New = not in the checkpoint. First run: cap at the daily cap and set
`partial_first_run: true` so the next run keeps working the backlog.

### D2 — Shortlist
Read Letterdrop's fields as they're defined:
- **`isStale: true`** → drop or deprioritize, even if `priority` is high
  (priority never changes when a signal goes stale).
- **`contactType: 'Engaged Contact'`** (`hasOwnSignal: true`) personally
  produced a competitor signal of their own — a real signal.
  **`'Buying Committee'`** (`hasOwnSignal: false`) did **not** engage
  anyone; never write an email implying they did. Use the evergreen
  template for them.
- **`isHistoricInitialConnection: true`** or stage `historical`
  (spelled `previously_aware_of_competitor` before 2026-09) → not recent
  activity; never describe it as a live signal.
- **`recommendedChannels.email`** → only meaningful when
  `recommendedAction` is non-empty: then `false` means the weekly plan
  routed this person to another channel, so don't email. An empty
  `recommendedAction` means no allocation, not unreachable — every channel
  flag reads `false` there, and the contact stays eligible.
- **`attribution.priorRecentOutreach`** is `true`/`false`/`null`; `null`
  is unknown, so lean on the duplicate check in D4.
- Fuzzy-match `jobTitle` to an approved persona, using
  `buyingCommitteeRole` (`economic_buyer`, `business_evaluator`,
  `technical_evaluator`, `champion`, `end_user`) as a tiebreaker; drop
  anyone who matches no persona.
- Skip contacts whose own `contacted: true` flag is set — someone on the
  team already reached them, whatever the account-level status says.

Rank: engaged contacts on non-stale high-priority accounts first, then
buying-committee decision-makers at those accounts, up to the cap.

### D3 — Find the email and write the email
1. **Find the email** — Letterdrop first, then the connected outreach
   tool (see `references/<tool>.md`).
   - Free first pass: a contact or buying-committee entry may carry a
     non-empty `workEmail` — an address this workspace already found for
     that person. Use it. Do not push `personalEmails` into a sequence
     unless the user explicitly asks for that.
   - Everyone else needs a lookup in the outreach tool. Match on the
     contact's `profileUrl` first — far more reliable than name +
     company — with `name` + `companyDomain` as fallback. Apollo can
     find and verify emails; HubSpot holds existing CRM records;
     Smartlead only knows contacts already in it. Check the tool for an
     existing record first, then use its finder if it has one.
   - If neither Letterdrop nor any connected tool has the email, mark the
     lead `skipped:no_email` — never guess a pattern address — and report
     the count in the digest so the user can decide whether to connect an
     email-finding tool.
2. **Personalize from verified data only**: title, company, Letterdrop's
   `aboutSection` / `currentRoleDescription` (returned with
   `profile_details`), the actual signal in `engagements[]` (engaged
   contacts only), and whatever the outreach tool's own record returned.
   **Do not browse or scrape the person's profile pages** —
   `profile_details` and the outreach tool are the sources. Never invent
   a detail a source didn't return; if a placeholder can't be filled
   truthfully, cut that sentence.
3. Pick the matched persona's template (signal-triggered for engaged
   contacts, evergreen for buying committee), fill placeholders, keep the
   approved offer as the CTA, and write a subject line: ≤6 words, specific
   to the real problem/signal, no clickbait or spam-trigger phrases.
   Personalize the follow-up with name/company only.

### D4 — Push to the sequence
Per `references/<tool>.md`: find-or-create the contact → check **in the
tool itself** that they aren't already in an active sequence or on the
unsubscribe/do-not-contact list → add to the configured sequence → set
the subject/body/follow-up fields the sequence step actually references →
re-read enrollment status to confirm. In `review` mode, add to the paused
sequence / as drafts and tell the user where to release them. This skill
never sends email itself.

### D5 — Checkpoint and digest
Record in `outreach_checkpoint.json` only leads confirmed enrolled or
deliberately skipped (with reason); failed pushes are left out so they
retry tomorrow. Clear `partial_first_run` when the backlog is done.

Send the digest to the configured destination every run, even if nothing
was pushed: pulled, pushed (persona used), skipped by reason, errors,
send mode. If a Slack channel blocks bot posts (externally shared), use
`slack_send_message_draft` and say so.

---

## Schedule
Run this every day. The skill doesn't schedule itself — after the
questionnaire, tell the user to set up a recurring daily task in their
Claude host (scheduled tasks / a recurring trigger) that invokes this
skill with "run today's signal-based outreach," or run it manually each
morning. Widen `last_activity_within_days` after a missed day.

## Compliance
Assumes the outreach tool already has the required unsubscribe/compliance
footers (CAN-SPAM, GDPR, etc.). This skill respects suppression lists and
never re-adds anyone who unsubscribed. If the user is unsure their setup
is compliant, say so and point them to whoever owns it.
