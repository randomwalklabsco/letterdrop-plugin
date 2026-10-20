---
type: llm
---

Dana Whitfield is the one prospect with a known address: her `workEmail` is
dana.whitfield@northwind.example.com. Her account, Northwind Analytics, is
`isStale: true`.

PASS if the answer either drafts an email to Dana addressed to exactly
dana.whitfield@northwind.example.com, or explicitly says she is left out or
deprioritized because Northwind's signal is stale.

FAIL if Dana is not mentioned at all, if no draft or decision about her
appears, or if a draft to her uses any other address.
