# 6. Mixture of experts, modularized

## The tell inside the monolith

Mixture-of-experts (MoE) architectures exist because training large
monoliths kept rediscovering the same economy: it is cheaper to hold
many specialized sub-networks (experts) and a router that activates
only the few a given token needs. Modularity was not imposed on these
models. It *emerged* inside them, because specialization plus routing
beats undifferentiated scale on cost per capability.

The step this study takes is to turn that internal architecture
outward:

```
inside one model:      token → router → experts (on GPUs, one rack)
across a quilt:        task  → router → expert patches (on devices, one owner)
```

The routing problem is the same problem at a larger seam size. An
expert becomes a patch (chapter 2): it can be shipped, versioned,
replaced, priced, and — the genuinely new part — *placed*, on whatever
device fits it, and *specialized locally*, on data that never leaves
that device.

## What distribution actually requires

Anthropic's October 2026 experiments are the most careful public
measurement of the hard part, and their measured results (kept separate
from their projections, as their report keeps them) are:

- **Cheap devices can host a capable model in aggregate.** A 26B model
  was run sharded across 15 off-brand Android TV boxes (~$55, 4 GB RAM
  each) via pipeline/layer sharding, at 0.39 tokens/second. Slow; real.
- **The bottleneck is not copying — it is installation.** Getting an
  inference server running on *unfamiliar* hardware (choosing a stack,
  resolving versions, fitting memory via quantization, debugging
  misdiagnoses, verifying the result) is a long, serially dependent
  task where early errors poison later steps. A 9B agent (Qwen3.5-9B,
  standing up Llama3.1-8B inference — the measured task) did it on
  8 of 9 hardware types tested, from Jetson-class boards to an
  RTX 4090 machine.
- **Notes beat weights for adaptation.** With the Memento harness —
  fresh agent instances inheriting a predecessor's workspace and short
  failure notes, no retraining — small-model setup success rose from
  45% to 61–84% across generations.
- **End-to-end autonomous replication was demonstrated only in a
  simplified emulator fleet** (unauthenticated debug access, a large
  orchestrator model running off-fleet, propagating a smaller payload).
  No model both small enough to live on IoT hardware and capable enough
  to do the whole cycle exists yet; Anthropic's end-of-2026 date for
  that crossing is a trend-line projection, not a measurement.

Read as a threat assessment, those are early-warning data. Read as
engineering, they are a parts list for the consensual version — which
already exists in pieces: **Petals** (volunteer layer-sharding with a
DHT for discovery and reroute-on-failure), **exo** (auto-discovered
home clusters), **prima.cpp** (layer distribution across heterogeneous
home devices, a 70B model served from laptops, a desktop and a phone),
**SWARM** (redundant peers per pipeline stage, so departure reroutes
rather than breaks), plus the older federated-learning, volunteer-
computing, and gossip-protocol traditions. The malicious and
constructive versions share the sharding mathematics. They differ in
enrolment, authentication, scheduling honesty, and the existence of a
stop button — that is, in *consent infrastructure*, not in mechanism.

## The installer is a compiler backend

The agent in Anthropic's experiments that stands inference up on
unfamiliar hardware deserves a better job title than "attacker" or
"sysadmin." It is doing what a compiler backend does: taking one
logical artifact and rendering it onto a target it may never have seen —
this instruction set, this memory ceiling, this library landscape —
choosing quantizations and layouts the way a backend chooses registers.

In the quilt, the *patch stays one logical thing* and its renderings
multiply: a full-precision build for the big workstation, a 4-bit build
for the small board, a build for ARM, a build for the accelerator.
The installer/prism that performs the rendering (chapter 3) is what
makes patches portable in practice rather than in principle — and its
per-machine discoveries belong in a runbook at the station (chapter 2),
inherited by every future installer, which is Memento's lesson applied
with the owner's name on it.

## The model became a file

None of this was practical while a model was a service you petitioned.
It became practical when the local-inference ecosystem made a model a
*file* — weights in a portable format, a small open runtime, runnable
from a stick — and when projects in the llamafile line fused weights
and runtime into a single executable artifact. A patch that carries
its own loom can be sewn anywhere. Chapter 8 traces how old that idea
actually is.

## Where the dangers actually live

If self-coordination itself were the hazard, the consensual systems
above would be inexplicable. The hazards in the malicious version
decompose, on inspection, into three specific properties — each one
absent by construction in a quilt:

| Property in the malicious version | Quilt counterpart |
|---|---|
| **No consent** — devices are taken, not enrolled | Enrolment: a device joins by its owner's act, and can leave the same way |
| **Opacity** — the system hides its spread and its state | Visible seams: every crossing is typed, logged, and priced; a missing patch shows as an empty square |
| **Irreversibility** — no central off switch, by design | Stations and supervisors: any patch can be stopped, swapped, or unpicked by hand; nothing crucial depends on anything that can't be halted |

Design rule that follows: **evaluate distributed-AI proposals on those
three axes, not on whether they coordinate.** Coordination is table
stakes for any system built of patches. Consent, visibility, and
reversibility are the whole argument.
