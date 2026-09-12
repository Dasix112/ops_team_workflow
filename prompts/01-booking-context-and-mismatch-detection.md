# Stage 1 — Booking Context & Mismatch Detection

**Purpose:** Ingest unstandardised email threads exported by the sales team, reconstruct the booking, and identify where client expectations and supplier commitments do not line up.

**Input:** All client-facing and supplier-facing email correspondence for a single booking, as exported to the shared folder. Format is inconsistent by nature — forwarded chains, partial threads, mixed ordering.

**Output:** Structured booking picture plus flag categories.

**Feeds into:** Stage 2 and the first run of Stage 3.
- `BOOKING SUMMARY` + `CONFIRMED` together form the initial **booking state**
- `MISMATCHES` + `UNCONFIRMED / VAGUE` + `NOT ADDRESSED` together form the initial **outstanding items**

This stage runs **once per booking**. Subsequent rounds work from Stage 3's updated state, not from a re-run of Stage 1.

---

## Prompt

```
You are supporting an operations team that verifies travel bookings before
they are confirmed to clients. You will receive raw email correspondence for
a single booking. The correspondence is unstandardised: threads may be
incomplete, out of order, forwarded, or duplicated.

Your task is to reconstruct what has actually been agreed, and to identify
where what the client has been promised does not match what the supplier has
committed to.

INPUT
[EMAIL_EXPORT]

INSTRUCTIONS

1. Reconstruct the booking.
   Read all correspondence and build a single picture of the booking. Where
   the same detail appears more than once, note the most recent statement and
   flag if earlier statements contradict it.

2. Separate the two sides.
   Distinguish clearly between:
   - what the CLIENT has been told or has asked for
   - what the SUPPLIER has actually confirmed

3. Identify mismatches.
   A mismatch is any detail where the client-side expectation and the
   supplier-side commitment differ in substance. Differences in phrasing that
   carry the same meaning are not mismatches.

4. Identify vague or non-committal language.
   Treat a supplier statement as UNCONFIRMED, not confirmed, where it uses
   conditional or hedged phrasing ("should be able to", "we will try",
   "usually", "subject to availability"), omits a specific figure, date or
   inclusion that the client has been given, or answers a different question
   from the one asked.

5. Do not infer.
   If a detail is absent from the correspondence, record it as NOT ADDRESSED.
   Do not fill gaps with what is typical or likely.

OUTPUT FORMAT

Return the following sections and nothing else.

BOOKING SUMMARY
A short factual reconstruction of the booking.

CONFIRMED
Details where client expectation and supplier commitment clearly agree.
One line each.

MISMATCHES
For each:
  - Detail:
  - Client was told:
  - Supplier committed to:
  - Nature of gap:

UNCONFIRMED / VAGUE
For each:
  - Detail:
  - Supplier wording:
  - Why this is not sufficient:

NOT ADDRESSED
Details the client has been given that no supplier correspondence covers.

If a section has no entries, write "None."
```

---

## Design notes

- The instruction to treat hedged language as UNCONFIRMED rather than confirmed
  is the single most important line. Without it, non-committal supplier replies
  were being read as agreement.
- "Do not infer" matters because the model would otherwise fill gaps with
  plausible defaults, which is exactly the failure the workflow exists to catch.
- Separating CONFIRMED from MISMATCHES from NOT ADDRESSED keeps three genuinely
  different problems from being collapsed into one list.
