# 4. Jev pairs: judges on both sides of a seam

A **Jev** is a small, cheap judgment model: it takes a question and returns
a *typed* answer — a percentage, a yes/no, a classification — fast (the
workshop's student judge: 6.8M parameters, ~7 ms on ARM). This chapter is
about what happens when you put a Jev on **both sides of every seam**, and
why that pairing, more than any single model in the system, is where the
intelligence of a quilt actually resides.

## Typed answers are angles

Free-form text can go in any direction, which is why producing it and
checking it both take a large model. A typed answer fixes the axis of
measurement *before* the model speaks:

- a yes/no is a reading along one bit;
- a percentage is a reading along one continuous axis;
- a classification is a reading across a fixed set of axes.

The judge no longer supplies the interpretation. It supplies **a reading
at a known angle**. That single change is what lets a tiny model be
trustworthy: it never has to understand the whole question, only to read
its one angle honestly. And readings at known angles have the property
text lacks — they can be compared, composed, thresholded, and audited
mechanically.

Sweep enough angles and a further thing becomes possible, the rangefinder
principle: one angled reading says almost nothing, but the *relations
among* thirty or fifty of them triangulate the shape of the thing being
measured, the way CT projections at many angles reconstruct a volume no
single projection contains. The probability mesh transfers an image —
of an intention, of a document's quality, of a situation's risk — across
a layer boundary without any model having to hold the whole image.

## The pair

**Sender Jev — interrogates what is leaving.** It does not decide
"send / don't send." It projects a *profile*: a small vector of typed
percentages that rides alongside the payload as the payload's measured
description of itself. How complete is this? How much is substance, how
much noise? Confident — about what, exactly? Mechanically, the sender
Jev is a prism (chapter 3) pointed at an outgoing state: one thing in,
a differentiated, typed spectrum out.

**Receiver Jev — interrogates what is arriving, from the next layer's
own needs.** Its go/no-go is **comparative, not absolute**: is this good
enough *in comparison to the other things competing for my attention,
my context, my slot?* That is a picking decision, and it is the same
mathematics as a softmax — a value means nothing alone and everything
relative to the pool it is normalized against. Absolute thresholds
describe a world where one candidate arrives at a time. Picking-side
judges describe the real one.

## The deadband

Between go and no-go there must be a third region, or the system will
collapse to verdicts it hasn't earned. In the deadband, the receiver's
honest answers are: *hold; ask one more angled question; escalate to an
expensive model for exactly this boundary.* The full field of percentages
— including the disagreement structure — is the primary data. Forced
words like keep / mutate / drop are a late collapse, applied only at the
moment a layer must act.

A measured result from the workshop's decision-grid experiments: gates
of this kind belong on **instances, not on cell types**. Applied blanket
rules misfired; the same gates applied per-instance took small-model
decisions from 50% correct to 100% correct on the tested set, accepting
11 of 11 correct small decisions and 0 of 18 wrong ones. Judging *this
specific thing at this specific seam* is a different, easier, and more
valuable job than judging a category.

## Disagreement is a first-class signal

The receiver must never merely trust the sender's self-reported profile:
it re-asks its own questions on arrival. When the sender projects 90%
substance and the receiver measures 40%, that gap is not an error to be
averaged away. It is among the most informative events in the system —
it localizes a misunderstanding, a corrupt patch, or a question that
needs decomposition (chapter 5) — and it costs nothing extra to obtain,
because both readings were being taken anyway.

## Chaining: a transformer made of judges

Layers connect by chaining pairs. A receiver processes what it picked,
then *becomes* the next sender: re-projecting a fresh typed profile of
the transformed thing for the next layer's receiver to pick against its
own pool. Probabilities feed gates with deadbands; threshold
combinations route to new question sets; values from prior rounds may
remain active alongside new ones. Assembled this way, a large question
can be answered by a battery of small judges and gates that is human-
readable end to end — every intermediate reading was typed, logged,
and priced.

## Graduation: when a gate stops needing a model

When a Jev pair has fired the same way a thousand times and its
behavior turns out to be formula-shaped, distill it into an explicit
equation and remove the model. The pair was scaffolding for discovering
a rule. This graduation path matters architecturally: it means model
presence in a mature quilt *shrinks* toward exactly the boundaries
where judgment is genuinely uncertain, and everything else becomes
arithmetic that anyone can audit. A judge can't certify itself — but an
equation doesn't have to; you can just read it.
