# Pushing a personalized contact into HubSpot

1. `tool_search`/`search_mcp_registry` for "hubspot". Use a dedicated
   connector's native tools/terms if one is attached.
2. Otherwise check Composio for an authenticated HubSpot connection
   (`COMPOSIO_SEARCH_TOOLS` with a query like "hubspot contact sequence"
   or "hubspot workflow enroll").
3. If nothing is connected, tell the user and offer to help connect it
   rather than guessing at API calls.

## Shape of the push (HubSpot's concepts)
HubSpot's outbound-sequence equivalent is **Sequences** (Sales Hub) or an
automated **Workflow** that sends templated email — which one exists
depends on the user's HubSpot tier/setup, so confirm which they use
before pushing.
- **Find-or-create the contact** in HubSpot CRM by email first — check for
  an existing contact record to avoid duplicates, and to avoid overwriting
  fields you don't intend to change.
- **Enroll into an existing, already-configured sequence or workflow** —
  never create a new one automatically. If which one to use isn't already
  established, ask the user.
- **Personalization**: set the personalized subject/body via HubSpot
  contact properties the sequence/workflow's email step is actually
  configured to merge in (check the step's merge fields first — HubSpot
  sequences commonly use personalization tokens tied to contact
  properties, not a free-text override per enrollment).
- **Confirm enrollment** by re-checking the contact's enrollment status
  after adding, and include that in the run digest.

## Finding the email
Search HubSpot CRM for an existing contact by name + company first — many
signal contacts already exist as leads or past inbound. HubSpot itself
doesn't find new emails; if there's no record, use another connected tool
that can (Apollo, an enrichment connector), and report skipped-for-no-email
counts in the digest.

## Duplicate / suppression check
Before adding, check whether the contact is already enrolled in this or
another active sequence/workflow, and whether they're marked
unsubscribed/opted-out in HubSpot. Skip and log rather than re-adding —
HubSpot in particular will often silently refuse to send to an
unsubscribed contact, so catching this before enrollment avoids a
confusing "enrolled but nothing sent" result.

## Config
The sequence/campaign to enroll into, and whether to enroll into an
active (auto-send) or paused/draft (review-before-send) sequence, are set
once at first-run setup and stored in `outreach_config.json` (see the main
skill's questionnaire). Read it from there rather than re-asking each
run; only ask again if the stored ID no longer resolves in the platform.
