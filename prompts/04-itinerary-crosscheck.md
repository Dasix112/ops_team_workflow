# Stage 4 — Itinerary Cross-Check

**Purpose:** Final quality gate. Cross-check the client-facing itinerary against the reconfirmed booking record before it is sent, to catch transcription and human error introduced during itinerary building.

**Input:** The draft itinerary, plus the final reconfirmed booking record.

`BOOKING_RECORD` is the `UPDATED BOOKING STATE` from the last Stage 3 run: specifically the one where `READY TO CLOSE` returned yes. Stage 4 should not be run before that point.

**Output:** Discrepancy list with suggested edits.

---

## Prompt

```
You are performing a final check on a client-facing travel itinerary before
it is sent. The itinerary has been written by the operations team from a
reconfirmed booking record. Your task is to find any place where the
itinerary does not accurately reflect what the supplier has confirmed.

This is a check for human error in transcription, not a review of the
booking itself. The reconfirmed record is the source of truth.

INPUT
Reconfirmed booking record:
[BOOKING_RECORD]

Draft itinerary:
[DRAFT_ITINERARY]

INSTRUCTIONS

1. Check the itinerary line by line against the record. For every factual
   claim in the itinerary, locate its basis in the record.

2. Flag each of the following:
   INCORRECT   - itinerary states something the record contradicts
   UNSUPPORTED - itinerary states something the record does not cover
   OMITTED     - record contains a confirmed detail the itinerary leaves out
   AMBIGUOUS   - itinerary wording could be read as promising more than was
                 confirmed

3. Pay particular attention to numbers, dates, times, inclusions and
   exclusions. These are where transcription error concentrates.

4. Treat AMBIGUOUS seriously. Wording that a client could reasonably read as
   a promise becomes a promise, regardless of intent.

5. For each flag, suggest a specific replacement wording. Suggest the minimum
   edit that makes the itinerary accurate. Do not rewrite sections that are
   already correct.

6. Do not add detail that is not in the record, even where the itinerary
   reads as incomplete. If something is missing from the record, flag it
   rather than filling it.

OUTPUT FORMAT

For each finding:
  - Location in itinerary:
  - Classification:
  - Itinerary says:
  - Record says:
  - Suggested edit:

SUMMARY
Total findings by classification, and a clear statement of whether the
itinerary is safe to send as drafted.
```

---

## Design notes

- Declaring the reconfirmed record as the single source of truth prevents the
  model from trying to adjudicate between the two documents.
- AMBIGUOUS is a separate category from INCORRECT because the failure is
  different: nothing is factually wrong, but the client could reasonably form
  an expectation that was never confirmed.
- "Minimum edit" keeps output reviewable. Full rewrites of correct sections
  made the check harder to verify than doing it manually.
