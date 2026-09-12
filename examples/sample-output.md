# Sample Output — Stage 1 (illustrative)

The structured response produced by `prompts/01-booking-context-and-mismatch-detection.md` when run against `sample-input.md`.

---

```
BOOKING SUMMARY
Four-night lodge stay, two rooms. Client has been told the booking includes
return airport transfers by private vehicle, a full-day guided walk on day 2,
and breakfast and dinner throughout. Supplier correspondence confirms room
availability only; several inclusions are either offered on different terms
or not addressed.

CONFIRMED
- Two rooms held for the requested dates.
- Breakfast included in the quoted rate.

MISMATCHES
- Detail: Dinner
  Client was told: Dinner included throughout
  Supplier committed to: Rates include breakfast; dinner available as an addition
  Nature of gap: An inclusion presented to the client is a chargeable extra
  to the supplier. Cost and rate implication.

- Detail: Guided walk duration
  Client was told: Full-day guided walk on day 2
  Supplier committed to: Half day is standard; full day subject to guide availability
  Nature of gap: Client has been promised an upgrade the supplier has not
  confirmed it can provide.

UNCONFIRMED / VAGUE
- Detail: Airport transfers
  Supplier wording: "we can usually arrange, will confirm vehicle closer to the time"
  Why this is not sufficient: No commitment to provide the transfer, and no
  vehicle type stated. "Usually" describes normal practice, not this booking.

- Detail: Full-day walk availability
  Supplier wording: "subject to guide availability"
  Why this is not sufficient: Conditional. Cannot be stated to the client as
  confirmed.

NOT ADDRESSED
- Private vehicle specifically, as distinct from a shared transfer.
- Return transfer leg. Supplier references transfers generally without
  confirming both directions.
```

---

## Action taken

Findings under MISMATCHES, UNCONFIRMED / VAGUE and NOT ADDRESSED pass to Stage 2, which drafts a single reconfirmation email covering all six items and asks the supplier to confirm each individually.

The dinner mismatch would additionally be flagged internally, as it carries a cost implication rather than being purely a clarification.
