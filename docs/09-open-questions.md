# 9. Open questions

Honestly open, in roughly the order they block progress.

## 1. The grain problem

In a transformer, patches are not freely fungible: layer 17 cannot swap
with layer 3, and experts specialize unevenly. So *at what grain does
the patch abstraction actually hold?*

Candidate answers, unresolved: the layer (portable but position-locked),
the expert (routable, nearly fungible within its layer), the whole small
model (fully fungible, coarser), the scope (chapter 5 — fungible by
construction, because a scope is a contract rather than a weight
layout). The suspicion in this workshop is that the quilt's true unit
sits higher than the layer — at the scope or the station — and that
layer-sharding belongs inside a patch, as its private business. Not
settled.

## 2. Receiver trust

Chapter 4 has receivers re-measure rather than trust sender profiles.
How far does that regress go? A receiver's readings are themselves
projections readable by a further judge. Practical systems will cut
the recursion somewhere — a depth budget, a spot-check rate, a
reputation log per station. Which cut keeps disagreement signal high
and cost low is an empirical question nobody here has run yet.

## 3. Prism audits

Projection leaves a spectrum — in principle. In practice, what is the
minimal log a prism must keep so that its influence on a system can be
reconstructed after the fact: which bands, to which cells, at what
strength, under which incoming state? "Auditable" is currently a
design intention, not a format.

## 4. When do gates graduate?

Chapter 4's graduation (gate → equation) needs a detection rule:
how many firings, at what stability, justify replacing a judge with
arithmetic — and who watches the equation afterward, in case the
world drifts out from under it? A graduated gate that silently
expires is worse than a judge, because nobody is reading its angles
anymore.

## 5. Deadband economics

The deadband trades accuracy for escalation cost. Its width is
currently set by feel. A principled version would price a wrong
collapse against a big-model call *per seam*, from the seam's own
logged history. Nobody has the dataset yet; the decision-grid
experiments (gates on instances, chapter 4) are the first sketch
of one.

## 6. Hologram budgeting

Chapter 7 says: buy graceful degradation deliberately, per station.
Deliberately requires a price list — throughput cost of redundant
stage copies, storage cost of erasure-coded weights, quality cost of
expert rerouting — measured on one real fleet, so "how holographic
is this station, and what did it cost?" becomes a line item instead
of a metaphor.

## 7. Consent infrastructure that isn't theatre

Enrolment, revocation, and stop buttons are easy to draw and easy to
fake. What does a device *prove* when it joins a quilt, what does an
owner actually see when a patch acts, and how does revocation
propagate to copies — at quilt speed, with receipts? The malicious
versions skip all of this; the useful versions live or die on
whether skipping it stays impossible when nobody is watching.
