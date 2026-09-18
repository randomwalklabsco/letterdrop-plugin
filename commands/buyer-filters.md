---
description: Show the workspace's definition of a buyer (company filters, buyer titles) that decides who counts as a valid lead, and update it only on explicit confirmation
argument-hint: "[change to make, e.g. 'add VP Engineering to decision makers']"
---

Requested change (may be empty, meaning show only): $ARGUMENTS

Start with `get_knowledge_base_buyer_filters` and show the current definition:
the company filters, the decision-maker titles and the individual-contributor
titles. Explain in one line that this is what decides who counts as a valid
lead, and so who appears as a buying committee member on every competitor
monitoring row.

If no change was requested, stop here.

If a change was requested:

1. Show exactly what the definition would become — the full resulting title
   lists, not just the delta.
2. Say plainly that `update_knowledge_base_buyer_filters` **replaces** the
   stored definition rather than merging into it, and that it changes what
   every future read of the table returns.
3. Ask the user to confirm, and wait. Do not call the update tool on the
   strength of the original request alone.
4. After updating, read the filters back and show what is now stored.
