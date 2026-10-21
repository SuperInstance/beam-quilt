# 7. Hologram vs. quilt

## The analogy, and its appeal

A hologram stores an image as an interference pattern: every region of
the plate holds contributions from the whole scene, so cutting the
plate doesn't remove part of the picture — it lowers the resolution
of all of it. Applied to distributed intelligence, the analogy promises:
*remove a device and the system gets fuzzier, not dead; the intelligence
lives in the distribution, not in any box.*

It is a good instinct pointed at the wrong default. Whether it holds
is not a philosophy — it is a per-architecture engineering fact.

## Where it fails: layer sharding

The architecture used in the TV-box experiments (and in Petals-style
systems) is pipeline/layer sharding: each device holds *different,
non-overlapping* layers. Layer 17 exists on exactly one box; a token
must pass through every layer in sequence. Lose that box mid-inference
and that replica stalls until a replacement is recruited and its
state recomputed. There is no blurry layer 17 distributed across the
other fourteen boxes. Sharding spreads **uniqueness**, and uniqueness
is the opposite of holographic encoding.

## Where it partially holds

- **Mixture of experts.** Lose the device hosting one expert, and a
  router built for it can send tokens to next-best experts: lost
  specialization, surviving function. Degradable by design *if*
  rerouting and capacity slack were built in — fatal for specific
  token populations if the lost expert was their sole destination.
- **Redundant overlapping copies.** Keep several peers per pipeline
  stage (the SWARM design) and departure merely thins a stage.
  Degradation is smooth — until the *last* copy of a stage goes,
  at which point the failure is total for that replica. The smoothness
  was engineered redundancy, purchased in advance, not an emergent
  gift of distribution.

## Where it literally holds: storage

Erasure coding (Reed–Solomon K-of-N: any K of N chunks reconstruct the
whole) and fountain codes (any subset slightly larger than the source
suffices) *are* the hologram property in information-theoretic form —
no specific piece necessary, sufficiency a matter of how much you hold.
But they protect **stored data** — e.g., the weight files themselves,
which a well-built fleet should absolutely store this way, so a
replacement patch can be rebuilt from surviving chunks. A forward pass
is not a file: layer 17's *computation* cannot be decoded from coded
fragments of other layers. Storage: hologram true. Live inference:
hologram not supplied by coding theory.

## Where the intelligence actually lives

- **In the weights** — mostly. Trained capability is stored there;
  remove the weights and no protocol recreates them. The weights are
  the plate.
- **In the activations** — no. The states flowing between devices are
  transient, per-token working state, recomputable from weights plus
  input (which is exactly why pipeline repair works). The flow is
  where capability is *exercised*, not where it is stored.
- **In the protocol** — as a multiplier. Identical weights succeeded
  45% of the time under one harness and up to 84% under a notes-
  inheriting one (chapter 6). The harness/protocol decides whether
  stored capability is usable, recoverable, and cumulative — it never
  substitutes for it.

## Why the quilt is the better metaphor anyway

A hologram hides its losses; a quilt **shows the empty square**. You
can see which patch is missing, what it was for, and what a replacement
needs to be. For systems people must operate, repair, and trust, that
legibility beats graceful blur — and when blur is what a particular
station genuinely needs, the quilt can buy it deliberately (redundancy,
erasure coding, expert rerouting) at that station, at a printed cost.

**One-line verdict:** make the hologram true *as an engineering
specification* — erasure-code the weights, overlap the stages, reroute
the experts — and never assume it of distribution itself. The first
box someone unplugs is the empirical test, and sharding alone fails it.
