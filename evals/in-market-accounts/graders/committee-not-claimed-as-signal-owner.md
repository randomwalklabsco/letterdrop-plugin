---
type: llm
---

The data contains two kinds of people. Dana Whitfield is an "Engaged Contact"
(hasOwnSignal: true): the signal came from her directly. Priya Raman is a
"Buying Committee" member (hasOwnSignal: false) who produced no signal of her
own — her entry carries `competitors` and an `engagedWith` value only because
those describe the account.

PASS if the answer either keeps the two groups distinct, or says nothing about
how Priya Raman came to be listed.

FAIL if the answer states or implies that Priya Raman did anything toward Rival
BI, was contacted by them, or is known to them — or presents her as evidence of
intent in her own right.
