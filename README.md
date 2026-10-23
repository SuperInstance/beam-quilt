# beam-quilt

**State, transmission, algorithm — a patchwork architecture for distributed, self-coordinating intelligence.**

A design study, written to be read. It takes one observation — an AI model has
become a file you can carry, split, and run on almost anything — and follows it
to a conclusion the field keeps circling: intelligence does not have to live in
one big model in one big building. It can be **modularized, vectorized, and
patched together** from pieces that each stand alone, coordinating at their
seams, on hardware the owner already has.

The occasion for the study is Anthropic's October 2026 Frontier Red Team work
on autonomous replication (see [References](#references)). Their framing is a
threat: a model that installs itself on unfamiliar devices and spreads. Read
through the patchwork-quilt abstraction, the same mechanics describe a
*toolkit*: modular experts distributed across consenting devices,
self-coordinating because their seams match — not because anything is in
command. This repo works out why both readings use identical machinery, and
why the difference is not coordination. The difference is **consent, opacity,
and irreversibility**.

## The core in five sentences

1. The minimal unit of everything here is a **transmission**: a state passes
   through an algorithm and becomes a state again — changed in place, moved,
   copied, or projected. Nothing else is required.
2. A **patch** is a thing that honors that contract and stands alone:
   a layer shard, an expert, a small judge, a runbook, a skill. A quilt is
   what *emerges* when enough good patches exist — a consequence, never a
   blueprint.
3. Between any two patches sits not a wire but **two judges**: a sender-side
   Jev that projects a typed profile of what is leaving, and a receiver-side
   Jev that decides — comparatively, against everything else competing for
   its attention — whether it is good enough to let in, with a deadband
   between go and no-go where the honest answer is "ask another question."
4. Big models are mostly needed because questions arrive unbounded.
   **Distill the scope, not the model**: decompose a question at fixed,
   typed angles until every sub-question is narrow enough for a small,
   fast judge to answer reliably — then a small model can live inside the
   distilled scope.
5. Self-coordination under this abstraction is not dangerous, because no
   patch can reach inside another. Like processes on the BEAM virtual
   machine, patches share no state; influence is only ever a *proposal*
   sent across a seam, and the receiver's own transition decides.

## Reading path

Read in order; each chapter is short and stands alone.

| # | Chapter | What it settles |
|---|---------|-----------------|
| 1 | [The minimal ontology](docs/01-state-transmission-algorithm.md) | State, transmission, algorithm; the four endings: mutate, move, copy, project |
| 2 | [Patches and seams](docs/02-patches-and-seams.md) | The patch contract, the fixed envelope, why seams are the architecture |
| 3 | [The prism and the BEAM](docs/03-prism-and-beam.md) | Influence without relocation; sovereignty of the receiver; supervision |
| 4 | [Jev pairs: judges on both sides of a seam](docs/04-jev-pairs.md) | Typed answers as angles, sender/receiver judges, percentages as comparative picking, deadbands, disagreement as signal |
| 5 | [Scope distillation](docs/05-scope-distillation.md) | Distill the question, not the model; decomposition from both directions; where the big model actually earns its call |
| 6 | [Mixture of experts, modularized](docs/06-moe-modularized.md) | MoE externalized onto devices; the installer as compiler backend; the evidence, including Anthropic's |
| 7 | [Hologram vs. quilt](docs/07-hologram-vs-quilt.md) | Which architectures degrade gracefully and which break; making the hologram true as an engineering spec |
| 8 | [Lineage](docs/08-lineage.md) | Fortran artifacts, linkers, the 1980s transmission turn, the model-as-file moment |
| 9 | [Open questions](docs/09-open-questions.md) | The grain problem, receiver trust, prism audits — what is not settled |

Formal companion: [SPEC.md](SPEC.md) states the patch contract and the seam
handshake as testable rules.

Worked example: [examples/voice-seam.md](examples/voice-seam.md) walks one
utterance through a prism, a Jev pair, and a small model living in a distilled
scope.

## How this relates to the sibling repos

This repo is the *synthesis*. The components live elsewhere and predate it:

- **jev-semantic** — the working Jev: judgment log, question tree, a small
  student judge (6.8M parameters; measured 12.3 ms p50 at 4 threads on a
  Jetson for short typed profiles — see FOUNDATIONS.md and jev-semantic's
  BENCHMARK.md for the corrected record on that figure).
- **quilt-cells** — the cell specification: input → cell → output + why + cost.
- **patchwork-experts** — the patch envelope format: a small, fixed
  markdown/JSON surface with powerful payloads underneath.
- **ai-writings / the deep-dive page** — the evidence base on Anthropic's
  experiments, with measured and projected claims kept separate.

Where this study states a measured fact about someone else's system, it cites
the source. Where it synthesizes, it says so.

## References

- Anthropic Frontier Red Team (Zhou & Freeman), *AI models may be capable of
  self-replicating without access to a datacenter*, 12 Oct 2026 —
  https://www.anthropic.com/research/self-replicating-ai
- Borzunov et al., *Petals: Collaborative Inference and Fine-tuning of Large
  Models* — https://arxiv.org/pdf/2209.01188v2
- Ryabinin et al., *SWARM Parallelism* — https://arxiv.org/pdf/2301.11913
- Yu et al., *prima.cpp: Speeding Up 70B-Scale LLM Inference on Low-Resource
  Everyday Home Clusters* — https://arxiv.org/html/2504.08791v1
- Men et al., *ShortGPT: Layers in Large Language Models are More Redundant
  Than You Expect* — https://arxiv.org/html/2403.03853v2/

## Status

Design study v0, 2026-10-21. Written from a conversation, published as
prior art while the thinking is fresh. Implementation is deliberately out of
scope here; the sibling repos carry the code.
