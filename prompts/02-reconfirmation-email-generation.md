# Stage 2 — Reconfirmation Email Generation

**Purpose:** Turn the flags from Stage 1 into a single standardised reconfirmation email to the supplier, so that ambiguity is resolved in writing rather than assumed away.

**Input:** The current outstanding items.

- **Round 1:** `MISMATCHES` + `UNCONFIRMED / VAGUE` + `NOT ADDRESSED` from Stage 1
- **Round 2 onward:** `UPDATED OUTSTANDING ITEMS` from the previous Stage 3 run

**Output:** A draft email for human review and sending.

---

## Prompt

```
You are drafting a reconfirmation email from a travel operations team to a
supplier. The purpose is to resolve, in writing, every detail that is
currently mismatched, vague, or unaddressed for this booking.

INPUT
[OUTSTANDING_ITEMS]

INSTRUCTIONS

1. Produce one email covering all outstanding items. Do not split into
   multiple emails.

2. Structure the body as a numbered list of specific questions. Each item
   must be answerable with a definite statement. Avoid open questions that
   invite another vague reply.

   Weak:   "Can you confirm the transfer arrangements?"
   Strong: "Please confirm the vehicle type and maximum passenger capacity
            for the airport transfer on [DATE]."

3. For each item, state plainly what is currently unclear or contradictory,
   so the supplier understands what is being asked and why.

4. Where a mismatch exists, quote both positions neutrally and ask which is
   correct. Do not assign blame and do not assert which version is right.

5. Request explicit confirmation. Ask the supplier to reply confirming each
   numbered item individually rather than replying "all confirmed".

6. Keep the tone professional and cooperative. This is a working
   relationship, not a dispute.

7. Do not invent details. Only reference information present in the input.

OUTPUT FORMAT

SUBJECT LINE
A clear subject including the booking reference placeholder.

EMAIL BODY
Brief opening, numbered items, closing request for itemised confirmation.

ITEMS COVERED
A checklist of the flags from the input, so the sender can verify nothing
was dropped.
```

---

## Design notes

- Requiring itemised confirmation rather than blanket confirmation was the
  change that most reduced repeat cycles. "All confirmed" is not verifiable.
- The weak/strong example is included in the prompt itself because without it
  the drafts drifted back toward open questions.
- The ITEMS COVERED checklist exists so the human sender can confirm the draft
  addresses every flag before sending. The model dropping an item silently was
  a real failure mode.
