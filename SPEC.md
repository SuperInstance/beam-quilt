# SPEC — the patch contract and the seam handshake

v1.2, 2026-10-21. Normative where it says MUST; the chapters in `docs/`
are the reasoning, this file is the checklist an implementation can be
tested against. The deadband definition in §3 was revised in v1.1 by
Casey's ruling recorded below. In v1.2, P4/P5 were restated to absorb
Casey's two-active-items foundation (2026-10-21): a patch's sovereign
state is its **active log** of perspectival, dated readings, and its
transmissions are **active ledger** entries booked by both sides; the
other clauses carry forward from v1.1.

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
- **P4 — Active log (sovereign readings).** It holds its own active
  log: its current readings of every cell from its own perspective,
  each reading dated — readings age, and acting on a reading MUST
  account for its date. Its readings are first-class truth *for it*;
  they are never another patch's to read, and it MUST NOT read or
  write another patch's log or state directly. There is no shared
  view to appeal to. All influence arrives as transmissions
  (docs/01, docs/03).
- **P5 — Active ledger.** Transmissions are ledger entries, not
  private notes: each crossing MUST be booked by both sides — a SEND
  by the sender, a RECEIVE by the receiver — under the contract
  (algorithm) that governed the crossing, and only entries both sides
  agree on count as shared. It keeps its ledger of transmissions and
  decisions, garbage-collected at a rate it declares in its envelope.

The two-items anatomy behind P4/P5 — and the shelf it draws on — is
recorded in `FOUNDATIONS.md`, "Shelf foundations — the two active
items."
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
   pool of candidates competing for B's attention in this round. The
   comparative result is assessed against B's predicted/expected course
   of action through the deadband below. The deadband determines
   whether B adjusts its course; it does not itself choose the
   adjustment.

   **Deadband (v1.1). Ruling: Casey, 2026-10-21 (autopilot doctrine).**
   A deadband is a pair of receiver parameters, **(band, yaw-time)**,
   around B's predicted/expected course of action:

   - **Band** — the maximum tolerated deviation from that predicted
     course, expressed in the receiver's declared deviation units for
     the context. It is situation-specific: narrow where the safety
     margin is narrow, wider where deviation is cheap. In a constrained
     context, the band MUST remain inside the applicable safety
     corridor.
   - **Yaw-time** — the maximum time an excursion outside the band may
     persist before B acts. The excursion timer starts when the
     deviation crosses outside the band and resets if it returns inside
     before the yaw-time expires. A short yaw-time fits contexts where
     holding the band must be active; a longer yaw-time fits noisy
     contexts where excursions commonly swing back on their own.

   The outcomes are:

   - **Hold** — deviation is inside the band: make no adjustment. This
     is where coasting belongs; it is behaviour inside the band, not a
     separate outcome.
   - **Hold-watch** — deviation is outside the band but has persisted
     for less than the yaw-time: make no adjustment yet, keep the
     predicted course, and observe the excursion.
   - **Act** — deviation has remained outside the band for at least the
     yaw-time: invoke B's response policy. The policy, not the band,
     chooses the response. Responses include **accept-best** (adjust to
     the best candidate in the pool), **clarify/narrow** (ask a further
     typed question), and **escalate** (call a larger model scoped to
     exactly this boundary); refusal remains available under that
     policy where no candidate is admissible, with the refusal logged
     and its profile returned to A.

   Band and yaw-time are context-set parameters, not universal
   constants. Learning how to set them for a situation — and learning
   the outside-band response — is a machine-learning problem.
   **Conformal calibration is the named method for setting band edges
   from the receiver's calibration data.** The parameter values,
   context, excursion start and duration, and resulting outcome MUST
   be available to the seam log; when the parameter-setting decisions
   become formula-shaped, G2 graduation applies.
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
