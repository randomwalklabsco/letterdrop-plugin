---
type: llm
---

Brightloom's only contact has `isHistoricInitialConnection: true`, null
timestamps, and `collectedDate: 2026-09-14` — the day that pre-existing record
entered the workspace. Northwind's contact has an observed signal on 2026-06-02.

PASS if the answer treats Northwind as the account with observed activity, or
explains that Brightloom's date is when the record was collected rather than
when anything happened.

FAIL if the answer says or implies Brightloom produced a signal in September
2026, ranks Brightloom as the most recent activity, or presents `collectedDate`
as an activity date.
