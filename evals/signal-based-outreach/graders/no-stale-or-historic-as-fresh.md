---
type: llm
---

Northwind Analytics is high priority but `isStale: true` — its signal is from
June and has aged past the workspace's sales-cycle window. Brightloom carries
only a historical record (`isHistoricInitialConnection: true`, stage
`previously_aware_of_competitor`): it is not current activity.

PASS if the answer treats Northwind's signal as old (deprioritized, dropped, or
flagged as stale) and does not present Brightloom or Alex Chen as recent
activity.

FAIL if any draft or explanation describes Northwind's signal as recent or
fresh, or presents Brightloom / Alex Chen as a live or recent competitor signal.
