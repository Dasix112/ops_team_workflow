# Sample Input — Stage 1 (illustrative)

> ⚠️ Entirely invented. Not real client, supplier or booking data.

A shortened example of the kind of correspondence Stage 1 receives. Real exports were longer, more fragmented and frequently out of order.

---

## Client thread (extract)

```
From: Sales
To: Client
Subject: Re: Lodge stay — 4 nights

Hi,

Confirming the details we discussed:

- 4 nights at the lodge, two rooms
- Airport transfer both ways, private vehicle
- Guided walk included on day 2, full day
- Breakfast and dinner included throughout

Looking forward to it.
```

---

## Supplier thread (extract)

```
From: Supplier
To: Operations
Subject: RE: Availability request — 4 nights

Hello,

Yes we can hold two rooms for those dates.

Transfers we can usually arrange, will confirm vehicle closer to the time.

The guided walk is available — we generally run the half day, the full day
is subject to guide availability.

Rates include breakfast. Dinner can be added.

Best regards
```

---

## Why this example

It contains one of each category the prompt is designed to separate:

- **Confirmed** — two rooms for the dates
- **Mismatch** — dinner presented to the client as included, offered by the supplier as an addition
- **Mismatch** — full-day walk promised, half day is the supplier's standard
- **Vague** — "usually arrange", "will confirm closer to the time" on transfers
- **Vague** — "subject to guide availability" on the full-day upgrade
- **Not addressed** — private vehicle specifically, and the return leg
