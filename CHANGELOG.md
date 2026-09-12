# Changelog

How the pipeline developed. Each entry states **what changed** and **what problem drove the change**.

---

## Stage 4 added — Itinerary cross-check

### Added
- Final check of the client-facing itinerary against the reconfirmed booking record, flagging incorrect, unsupported, omitted and ambiguous content with suggested minimum edits.

### Reason
- Even with the booking fully reconfirmed, errors were still reaching clients — introduced during itinerary writing rather than during the booking process. Verifying the booking was not sufficient if the document built from it was inaccurate.

---

## Stage 3 extended — Detection of newly introduced inconsistencies

### Changed
- Supplier replies are now checked against the full booking state, not only against the list of outstanding questions.

### Reason
- Suppliers would answer the question asked while altering a detail that had already been agreed. Checking only the outstanding list missed these entirely.

---

## Stage 2 changed — Itemised confirmation required

### Changed
- Reconfirmation emails now ask the supplier to confirm each numbered item individually, rather than permitting a blanket reply.
- Added explicit weak/strong question examples to the prompt.

### Reason
- Blanket replies of "all confirmed" were not verifiable and triggered further rounds. Open-ended questions invited further vague answers, so drafts had to be constrained toward questions answerable only with a definite statement.

---

## Stages 1–3 established — Initial pipeline

### Added
- Stage 1: reconstruct bookings from unstandardised email exports; separate client expectation from supplier commitment; flag mismatches and vague wording.
- Stage 2: generate a standardised reconfirmation email from the flags.
- Stage 3: analyse the supplier reply, classify items as resolved or still open, propose resolutions for human decision.

### Reason
- Verification depended on an operations team member reading inconsistent correspondence and holding the full booking in mind. Detection was inconsistent and problems surfaced after the client had already been told something.
