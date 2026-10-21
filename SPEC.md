# SPEC — the patch contract and the seam handshake

v0, 2026-10-21. Normative where it says MUST; the chapters in `docs/`
are the reasoning, this file is the checklist an implementation can be
tested against.

## 1. Patch

A **patch** is a unit that satisfies all of:

- **P1 — Fixed envelope.** It presents a small, fixed description:
  name, what it accepts, what it emits (with types), what it costs.
  The description MUST be human-readable without opening the patch.
- **P2 — The contract.** For every input it produces
  `OUTPUT + WHY + COST` (docs/02). A bare score with no why is
  non-conforming.
- **P3 — Standalone worth.** It MUST be useful, or at minimum honestly
  evaluable, with no quilt around it.
- **P4 — Sovereign state.** It holds its own state and MUST NOT read
  or write another patch's state directly. All influence arrives as
  transmissions (docs/01, docs/03).
- **P5 — Own log.** It keeps a log of its transmissions and decisions,
  garbage-collected at a rate it declares in its envelope.
- **P6 — Haltable.** It can be stopped, and its station (docs/02)
  survives its absence; nothing downstream may require this specific
  instance, only the station's contract.

## 2. Transmission

A **transmission** is a tuple:

```
{ payload, type, sender-profile, ending, cost-budget }
```

- **T1 — Typed.** The payload's type is declared. Crossings that can't
  be typed don't cross; they get decomposed first (docs/05).
- **T2 — One ending.** Exactly one of MUTATE / MOVE / COPY / PROJECT
  (docs/01) is chosen per transmission, and the choice is logged.
  COPY MUST never be a default; it is chosen, per transmission or per
  standing owner instruction, and the choice is attributable.
- **T3 — Profiled.** A sender-side judge (below) attaches the profile
  before the payload crosses.

## 3. The seam handshake

For a transmission from patch A to patch B:

1. **Profile (sender Jev).** A's sender judge projects a typed vector
   — completeness, substance, confidence-by-aspect — describing the
   payload (docs/04). It does not decide delivery.
2. **Cross.** The payload and profile cross the seam. Nothing else
   crosses; in particular no state references cross (P4).
3. **Pick (receiver Jev).** B's receiver judge re-measures the payload
   with its own questions and decides **comparatively** — against the
   pool of candidates competing for B's attention in this round:
   - above the go band → accept;
   - in the **deadband** → hold, ask a further typed question, or
     escalate to a larger model scoped to exactly this boundary;
   - below the no-go band → refuse, with the refusal logged and its
     profile returned to A.
4. **Disagreement record.** If sender profile and receiver measurement
   diverge beyond a declared tolerance, the divergence is logged as a
   first-class event at the seam (docs/04). It is never averaged away
   silently.
5. **Act and re-profile.** If B acts, B becomes a sender for the next
   seam; its outgoing profile describes the *transformed* result, not
   the input it received.

## 4. Gates and graduation

- **G1 — Gates judge instances.** Any go/no-go gate MUST be applied to
  the specific instance crossing, not merely to its type or category
  (docs/04).
- **G2 — Graduation.** A gate whose decisions are demonstrably
  formula-shaped over a declared number of firings SHOULD be replaced
  by the explicit equation, and the equation MUST be entered in the
  seam's log with its evidence.
- **G3 — Drift watch.** A graduated equation MUST keep a cheap judge
  on spot-check duty; an equation without a watcher is
  non-conforming.

## 5. Consent and visibility (owner-facing)

- **C1 — Enrolment.** A device or patch joins a quilt by its owner's
  act. Departure is the same kind of act, and COPY endings do not
  outlive it.
- **C2 — Visible seams.** Every crossing is logged with payload type,
  profile, pick, ending, and cost. An owner MUST be able to read,
  for any patch, every influence it sent and received.
- **C3 — The stop test.** An owner MUST be able to halt any patch —
  and, separately, the whole quilt — and have the halt take effect
  without the cooperation of the thing being halted.
