---
description: Show the workspace's definition of a buyer (company filters, buyer titles) that decides who counts as a valid lead, and update it only on explicit confirmation
argument-hint: "[change to make, e.g. 'add VP Engineering to decision makers']"
---

Requested change (may be empty, meaning show only): $ARGUMENTS

Start with `get_knowledge_base_buyer_filters` and show the current definition:
the company filters (company size, industries, HQ locations) and the job-title
keywords, seniorities and locations for each tab: decision makers, end users
(`individualContributors`) and the full buying committee (which uses the
decision makers' seniorities). Explain in one line that this is what decides who counts as a valid
lead, and so who appears as a buying committee member on every competitor
monitoring row.

If no change was requested, stop here.

If a change was requested:

1. Show exactly what the definition would become — the full resulting title
   lists, not just the delta.
2. Say plainly that `update_knowledge_base_buyer_filters` **replaces** each
   list it is sent (send the full resulting list, not just the addition),
   keeps lists it is not sent, and changes what every future read of the
   table returns. Company sizes must be one of `1-10`, `11-50`, `51-200`,
   `201-500`, `501-1000`, `1001-5000`, `5001-10000`, `10001+`, and sending
   sizes turns "all sizes" off; seniorities must be one of `Individual
   Contributor`, `Manager`, `Director`, `Vice President`, `CXO`,
   `Owner / Partner`; locations are ISO country codes (preferred) or exact
   country names.
3. Ask the user to confirm, and wait. Do not call the update tool on the
   strength of the original request alone.
4. After updating, read the filters back and show what is now stored.
