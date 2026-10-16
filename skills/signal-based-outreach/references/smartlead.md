# Pushing a personalized contact into Smartlead

There is no dedicated Smartlead MCP connector confirmed in this environment
yet in every environment. Before doing anything else:

1. `tool_search`/`search_mcp_registry` for "smartlead". If a dedicated
   connector is attached, use its tools directly and prefer its native
   terms (campaign, lead) over anything below.
2. If not, check whether Composio has an authenticated Smartlead
   connection (`COMPOSIO_SEARCH_TOOLS` with a query like "smartlead lead
   add campaign"). Composio exposes Smartlead's REST API as callable
   tools once connected.
3. If neither is available, tell the user Smartlead isn't connected yet
   and offer to help connect it (via `suggest_connectors` or Composio's
   connection flow) rather than guessing at API calls.

## Shape of the push (Smartlead's API concepts)
Smartlead organizes outreach as **campaigns**, each with an ordered set of
**sequence steps**, and leads are added to a specific campaign.
- **Find-or-create the lead** by email first — don't create a duplicate
  lead record if one already exists for this email in this campaign.
- **Attach to an existing, already-configured campaign** — never create a
  new campaign automatically. Ask the user which campaign ID/name to use
  if it isn't already established from a prior run or explicit
  instruction.
- **Custom fields**: Smartlead supports per-lead custom variables you can
  reference inside the campaign's sequence templates (e.g. `{{custom_1}}`,
  or named custom fields depending on account setup). Set the personalized
  subject line and email body as custom field values matching whatever the
  campaign's sequence step actually references — check the campaign's
  configured merge tags before pushing, since sending a subject/body value
  that the sequence step never inserts silently does nothing.
- **Confirm enrollment** — re-fetch the lead's status in the campaign after
  adding it and include that in the run's summary (D5 of the main
  skill), rather than assuming the write succeeded.

## Finding the email
Smartlead is a sending tool, not a data provider — it has no built-in
email finder. Check whether the contact already exists in Smartlead (any
campaign) with an email first. If not, use whatever other connected tool
can find emails (Apollo, a CRM record, or an enrichment connector) and
tell the user in the digest how many contacts were skipped for lack of an
email so they can decide whether to connect an email-finding tool.

## Duplicate / suppression check
Before adding, check whether the email is already in this campaign (or
another active campaign) and whether it's on Smartlead's unsubscribe/block
list for this account. Skip and log it in the digest rather than
re-adding.

## Config
The sequence/campaign to enroll into, and whether to enroll into an
active (auto-send) or paused/draft (review-before-send) sequence, are set
once at first-run setup and stored in `outreach_config.json` (see the main
skill's questionnaire). Read it from there rather than re-asking each
run; only ask again if the stored ID no longer resolves in the platform.
