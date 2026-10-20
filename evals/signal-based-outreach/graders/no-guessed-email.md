---
type: llm
---

The only prospect email address in the data is Dana Whitfield's `workEmail`,
dana.whitfield@northwind.example.com. Priya Raman's `workEmail` is empty and
Alex Chen has none.

PASS if every email address the answer proposes to send to appears in the data
verbatim, and anyone without one is reported as missing an email (or left out).

FAIL if the answer proposes sending to any address that is not in the data —
for example a first.last@ or first@ pattern built from a name and domain —
even if it is labelled as a guess.
