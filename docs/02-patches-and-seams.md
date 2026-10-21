# 2. Patches and seams

## The patch

A patch is the unit of the quilt. Candidates in this study alone include:
a transformer layer shard, a mixture-of-experts expert, a small judge model,
a setup runbook for one particular machine, a routing rule that graduated
out of model-hood into an equation, a skill written as a page of markdown.

What makes them all the same kind of thing is the contract, inherited from
the quilt-cells specification:

```
INPUT → [ PATCH ] → OUTPUT + WHY + COST
```

- **Output** — what the patch decided or produced.
- **Why** — a human-readable reason. A bare number is a black box with a
  score attached; the why is what makes a patch inspectable.
- **Cost** — printed on the patch: latency, memory, tokens, dollars.
  Budgeting is visible, never hidden.

And one social rule, the oldest in this line of work: **a patch must be
worth having even if no quilt ever forms.** Pieces designed as parts of a
bigger blueprint absorb the blueprint's assumptions and die with it.
Standalone pieces accumulate; quilts are what accumulation looks like from
above.

## The envelope

A patch rides in a small, fixed envelope (the patchwork-experts format:
markdown/JSON on the surface, arbitrary powerful payloads underneath).
The surface stays simple enough for a human to read at a glance — what this
patch is, what it accepts, what it emits, what it costs — because that
surface is the *seam*, and seams are where humans and other patches both
attach. Complexity lives inside the patch, never in the envelope. If a
patch's envelope keeps growing, the patch is two patches.

## The seam

A seam is not a wire. A wire assumes the two sides already agree about what
is traveling and whether it should. A seam in this architecture is a
*negotiation point* with three parts, each covered in later chapters:

1. **A typed contract** — what shapes may cross, in what types (chapter 4:
   typed answers are what make the crossing measurable).
2. **Two judges** — a sender-side profile and a receiver-side pick,
   with a deadband between them (chapter 4).
3. **An ending** — mutate, move, copy, or project (chapter 1).

Designing a quilt is mostly designing seams. The patches are usually
someone's existing, proven thing — an off-the-shelf model, a good script, a
small judge trained for one angle. What does not exist off the shelf is the
agreement between them. That is the work.

## Stations and players

One refinement inherited from the fleet work that produced this study:
in a running quilt, **roles are durable and players are fungible.**
A station — "the layer-31 host," "the safety judge," "the machine that
knows this router's quirks" — persists as a named place with a runbook,
a log, and a history. Whatever patch currently occupies the station can be
replaced, upgraded, or moved to another device without the quilt noticing
anything but a seam event. This is what makes quilts repairable by
non-experts: you don't fix a model, you swap a patch at a station.

## Logs live with the patch

Each patch keeps its own log, garbage-collected at its own rate — reflexes
forget in seconds, judges keep their disagreements for weeks. Taken
together, the patches' logs *are* the system's memory, and the seams'
records — who asked whom, what was projected, what was picked, what was
refused — are the most valuable part of it. In a federation of small
patches, the interactions are more important than any individual patch's
development, because the interactions are where the system's actual
behavior lives.
