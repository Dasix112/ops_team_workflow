# Supplier Commitment Check

A four-stage prompt pipeline used by a travel operations team to verify that what a client has been promised matches what a supplier has actually confirmed — before the itinerary is sent.

Built and used in a live operations role coordinating bookings across 20+ external suppliers.

---

## The problem

Booking correspondence arrived as unstandardised email exports: client threads and supplier threads dropped into a shared folder by the sales team, in no consistent format. Someone in operations had to read all of it and work out whether the product promised to the client was the product the supplier had committed to.

Two failure modes recurred. First, detail was lost between the client conversation and the supplier conversation. Second, suppliers frequently replied in non-committal language — "should be able to", "subject to availability" — which was easy to read as confirmation and was not. Both surfaced late, usually after the client had already been told something.

This pipeline moved that verification from memory and reading attention to a structured, repeatable process.

---

## How it runs

```
┌─ Stage 1 ── Booking context & mismatch detection
│             Ingest raw email exports, reconstruct the booking,
│             flag mismatches and vague supplier wording
│
├─ Stage 2 ── Reconfirmation email generation
│             Draft a standardised email asking the supplier to
│             reconfirm every unclear item explicitly
│
├─ Stage 3 ── Supplier response analysis          ◄──┐
│             Classify what is resolved, what is still open,   │
│             and what the reply has newly contradicted        │
│             Propose resolutions; flag DECISION REQUIRED      │
│                                                              │
│             └── if items remain open, return to Stage 2 ─────┘
│
└─ Stage 4 ── Itinerary cross-check
              Check the finished itinerary against the reconfirmed
              record, catching transcription and human error
              before it reaches the client
```

Stages 2 and 3 loop until nothing remains outstanding. Stage 4 is the final gate.

---

## Data flow between stages

Each stage's output is the next stage's input. Nothing is reconstructed by hand between rounds.

| Stage | Takes | Produces |
|---|---|---|
| **1** | Raw email exports | Booking state, outstanding items |
| **2** | Outstanding items | Reconfirmation email draft |
| **3** | Booking state, outstanding items, supplier reply | **Updated** booking state, **updated** outstanding items, resolution proposals |
| **4** | Final booking state, draft itinerary | Discrepancy list with suggested edits |

**Stage 1 runs once per booking.** It establishes the initial state from the raw correspondence and is never re-run.

**Stages 2 and 3 loop.** On the first round, Stage 3 takes its booking state and outstanding items from Stage 1. On every round after that, it takes them from its own previous output. This is why Stage 3 emits an updated state rather than only reporting what changed — without that, each round would require rebuilding the inputs manually, reintroducing precisely the transcription error the pipeline exists to eliminate.

The loop exits when Stage 3 returns `READY TO CLOSE: yes`.

**Stage 4 runs once, at the end.** Its booking record is the final updated booking state from the closing Stage 3 run.

---

## Contents

| Path | What it holds |
|---|---|
| `prompts/01-…` | Booking reconstruction and mismatch detection |
| `prompts/02-…` | Reconfirmation email generation |
| `prompts/03-…` | Supplier response analysis (loop stage) |
| `prompts/04-…` | Final itinerary cross-check |
| `examples/` | One illustrative input set and its corresponding output |
| `CHANGELOG.md` | How the pipeline developed, and what drove each change |

Each prompt file includes the prompt itself, its input and output contract, and design notes explaining why specific constraints exist.

---

## Why this repository exists

The written process documentation — how the workflow runs day to day, who acts on what — lives in Confluence.

The **prompts are versioned here** because prompt changes benefit from diffs. When a constraint is added, what matters is exactly which line changed and which failure prompted it. That history is not well served by a wiki page.

📄 **Full process documentation:** Maintained in Confluence — https://dancmilitaru.atlassian.net/wiki/spaces/AUC/pages/1114113/AI+Use+Case+Supplier+Commitment+Verification?atlOrigin=eyJpIjoiOTBlMWRkOTYxMzc5NGZjNDk5NGU2ZDc3MWQ1OTE5ZGYiLCJwIjoiYyJ9

---

## Design principles

Three constraints recur across all four stages, because all three address failures observed in use:

**Hedged language is not confirmation.** Conditional or non-committal supplier wording is classified as unconfirmed, never as agreement. This single rule addressed the most common source of late-surfacing problems.

**Do not infer.** Where a detail is absent, it is recorded as absent. The model filling gaps with plausible defaults is precisely the failure the pipeline exists to catch.

**The tool proposes, a human decides.** Anything touching cost, or anything that would change what a client has already been promised, is marked DECISION REQUIRED and stops.

---

## Known limitations

See the open [Issues](../../issues) in this repository.

---

## A note on scope

All examples are **illustrative and genericised**. No client names, supplier names, booking references or commercially specific content are included. Prompt text is reconstructed to match the workflow as operated; exact original wording was not retained.
