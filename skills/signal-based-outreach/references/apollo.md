# Pushing a personalized contact into Apollo

1. `tool_search`/`search_mcp_registry` for "apollo". Use a dedicated
   connector's native tools/terms if one is attached.
2. Otherwise check Composio for an authenticated Apollo connection
   (`COMPOSIO_SEARCH_TOOLS` with a query like "apollo contact sequence").
3. If nothing is connected, tell the user and offer to help connect it
   rather than guessing at API calls.

## Shape of the push (Apollo's concepts)
Apollo organizes outreach as **sequences**, made of ordered steps, with
contacts added to a sequence.
- **Find-or-create the contact** by email — check for an existing Apollo
  contact/account record first to avoid duplicates.
- **Attach to an existing, already-configured sequence** — never create a
  new sequence automatically. If which sequence to use isn't already
  established, ask the user.
- **Per-contact personalization**: Apollo sequences typically pull from
  contact fields and liquid-style merge tags in the sequence's email
  templates. Set the personalized subject/body either via contact custom
  fields the sequence step references, or via Apollo's per-contact email
  content override if the connector/API exposes one — check what the
  actual sequence step is configured to pull from before pushing values it
  will never use.
- **Confirm enrollment** by re-checking the contact's sequence status
  after adding, and include that in the run digest.

## Finding the email
Apollo has a built-in people database and email finder: match the
person by the `profileUrl` profile URL Letterdrop returns for every
contact (fall back to name + company domain) and use Apollo's enrichment to reveal a verified email before
enrolling. Revealing an email consumes Apollo credits, so only do it for
the ranked shortlist, and skip (with reason) anyone Apollo can't verify
rather than guessing a pattern address.

## Duplicate / suppression check
Before adding, check whether the contact is already active in this or
another sequence, and whether they're on Apollo's do-not-contact / bounced
list for this account. Skip and log rather than re-adding.

## Config
The sequence/campaign to enroll into, and whether to enroll into an
active (auto-send) or paused/draft (review-before-send) sequence, are set
once at first-run setup and stored in `outreach_config.json` (see the main
skill's questionnaire). Read it from there rather than re-asking each
run; only ask again if the stored ID no longer resolves in the platform.
