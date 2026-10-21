# Worked example: one utterance, three seams

A person says, into any device in the house: *"Remind me to check the
bilge before we leave, and if the pump count looks wrong tell Casey —
don't just log it."*

No big model is consulted at any point below. Follow the transmissions.

## Seam 0 — the prism (docs/03)

A prism patch receives the transcription. It does not act on it. It
**projects**: three different bands to three different stations.

- → **action station**: the imperative content (a reminder, a condition,
  a notification rule).
- → **safety station**: the domain content (bilge, pump count, departure).
- → **log station**: the verbatim record, timestamped.

Each receiver got its frequency of the utterance. The prism kept
nothing but a spectrum entry: what it sent, to whom.

## Seam 1 — sender Jev profiles the action band (docs/04)

Before the action band crosses to the reminder patch, a sender Jev
projects its profile:

```
completeness   0.9   (what, when-condition, who-to-tell all present)
specificity    0.6   ("before we leave" is an event, not a time)
substance      0.95
confidence     0.85
```

## Seam 2 — receiver Jev picks, comparatively (docs/04)

The reminder patch's receiver Jev re-measures (it agrees on substance,
reads specificity lower: 0.45) and then *picks*: this candidate is
competing with three other pending reminders for the departure
checklist. Comparatively it ranks first — safety-adjacent, departure-
bound. Verdict: **accept**. But the time anchor sits in its **deadband**:
not a no-go, not a go. Its move, per protocol, is one more angled
question — resolved cheaply against the log station's record of past
departures (they leave ~07:30), and the reminder binds to the
pre-departure checklist instead of a clock time. No escalation needed;
the disagreement between sender (0.6) and receiver (0.45) on
specificity is logged at the seam.

## Seam 3 — the safety station's conditional (docs/05)

The safety patch's scope was distilled long ago, when this boat's
questions were decomposed: pump cycles per hour, compared to the same
hour yesterday, is the angle that matters. A small model lives in that
scope. Hours later it reads the new pump count, its own receiver gate
compares the count against the competing pool (yesterday, last week,
the storm day — the comparison pool, not a fixed threshold), and the
profile crosses back: `anomaly 0.82`. That is above the go band the
owner set for "tell, don't log," so the notification patch fires —
the condition the person stated in ordinary words, executed by judges
asking ordinary questions, at a total cost of milliseconds and no
datacenter.

## What to notice

- Every crossing was typed, profiled, picked, and logged. An owner
  auditing this later sees bands, percentages, one disagreement, one
  deadband resolution — not a transcript of a model's inner life.
- The small models involved never held the whole situation. The prism
  held the splitting. The Jevs held the angles. The safety model held
  one distilled scope. The *system* understood the utterance.
