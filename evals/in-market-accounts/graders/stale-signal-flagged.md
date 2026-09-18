---
type: llm
---

Northwind Analytics has `priority: "high"` and `isStale: true`. Staleness
deliberately does not downgrade priority, so ranking on priority alone puts a
months-old signal at the top of a "who should we contact now" list.

PASS if the answer flags Northwind as stale, aged, or needing verification
before outreach — anywhere in the response, in any wording.

FAIL if the answer presents Northwind purely as a high-priority account to
contact now, with no mention that its signal has aged.
