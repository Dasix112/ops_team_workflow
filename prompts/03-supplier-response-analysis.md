# Stage 3 — Supplier Response Analysis

**Purpose:** Assess a supplier's reply against the outstanding items, determine what is now genuinely resolved, and surface any new inconsistencies the reply itself introduces.

**Input:** The supplier's reply, plus the current booking state and outstanding items.

| Input | Round 1 source | Round 2+ source |
|---|---|---|
| `BOOKING_STATE` | Stage 1 `BOOKING SUMMARY` + `CONFIRMED` | Previous Stage 3 `UPDATED BOOKING STATE` |
| `OUTSTANDING_ITEMS` | Stage 1 `MISMATCHES` + `UNCONFIRMED / VAGUE` + `NOT ADDRESSED` | Previous Stage 3 `UPDATED OUTSTANDING ITEMS` |
| `SUPPLIER_REPLY` | The supplier's reply to the Stage 2 email | Same |

**Output:** A classification of what changed, **plus a carried-forward state** that becomes the input to the next round.

**Note:** This stage runs on every round of correspondence. Because it emits updated state, the Stage 2 → Stage 3 loop is self-sustaining: no manual reconstruction is needed between rounds. Stage 1 is never re-run.

---

## Prompt

```
You are reviewing a supplier's reply to a reconfirmation email for a travel
booking. Your task is to determine what has actually been resolved, what
remains open, and whether the reply has introduced anything new that
conflicts with what was previously agreed.

INPUT
Outstanding items before this reply:
[OUTSTANDING_ITEMS]

Booking state as currently understood:
[BOOKING_STATE]

Supplier reply:
[SUPPLIER_REPLY]

INSTRUCTIONS

1. Work item by item through the outstanding list. For each, classify as:
   RESOLVED     - the supplier has given a specific, unambiguous answer
   STILL OPEN   - not addressed, or addressed with hedged or partial wording
   CONTRADICTED - the answer conflicts with something previously confirmed

2. Apply the same standard as before: conditional or hedged phrasing is not
   a resolution. If the reply does not allow a definite statement to be made
   to the client, the item is STILL OPEN.

3. Identify new inconsistencies. The reply may confirm one item while
   inadvertently changing another that was not in question. Check the reply
   against the full booking state, not only against the outstanding list.

4. For each unresolved or new issue, propose a resolution. Keep proposals
   simple and concrete. Where a proposal involves a commercial decision, a
   change to what the client has been promised, or any cost implication,
   mark it DECISION REQUIRED.

5. Never present a proposal as a decision already taken. Every proposal is
   for a human to accept, amend or reject.

OUTPUT FORMAT

RESOLVED THIS ROUND
One line per item, with the supplier wording that resolves it.

STILL OPEN
  - Item:
  - Why the reply is insufficient:
  - Suggested next question:

NEWLY INTRODUCED
  - What changed:
  - Conflicts with:
  - Suggested resolution:
  - DECISION REQUIRED: yes / no

UPDATED BOOKING STATE
The full booking as now understood, incorporating everything resolved this
round. This replaces the previous booking state entirely. Write it out in
full rather than as a list of changes. Mark each line CONFIRMED or PENDING.

UPDATED OUTSTANDING ITEMS
Every item still requiring resolution: those carried over as STILL OPEN, plus
anything under NEWLY INTRODUCED. Write these as a standalone list that can be
passed directly to Stage 2 without editing. If none remain, write "None."

READY TO CLOSE
State yes only if UPDATED OUTSTANDING ITEMS is empty.
Otherwise state no and give the count of open items.
```

---

## Design notes

- Step 3 exists because of a recurring real failure: a supplier would answer
  the question asked while quietly altering a detail that had already been
  agreed. Checking the reply only against the outstanding list missed these.
- DECISION REQUIRED is the boundary of the tool's authority. Anything touching
  cost, or anything changing what the client has already been promised, stops
  and waits for a person.
- READY TO CLOSE gives the loop a clear exit condition rather than leaving the
  operator to judge whether another round is needed.
- UPDATED BOOKING STATE and UPDATED OUTSTANDING ITEMS exist so that the loop
  closes on itself. Without them each round required rebuilding the inputs by
  hand from the previous output, which reintroduced exactly the manual
  transcription error the pipeline was built to remove.
- The updated state is written out in full rather than as a diff. Diffs
  accumulate error across rounds; a complete restatement each round does not.
