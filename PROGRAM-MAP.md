# Program Map — where the Gemini-conversation ideas live

## Purpose

Casey ran a long conversation with Gemini (preserved in `docs/proposed/gemini1–22.md`, plus three ideation documents in the `chrono-tensor` repo) seeded by this repo and by years of SuperInstance projects. The conversation sprawled — deliberately — across many of those projects at once.

This map assigns every idea cluster in that conversation a **home**: the repo where its builder documentation lives. A builder agent working in one home needs that home's need-to-knows only — not the whole landscape the conversation ranged over. The raw conversation stays in `docs/proposed/` as the record; the working documents live in the homes.

**Verification (2026-10-21):** every SuperInstance repo name referenced anywhere in the conversation files was checked against GitHub — over sixty names, and every one that is presented as an existing project exists and is public (including snapkit-v2, flux-tensor-midi, plato-engine-block-c, forgemaster, erised, cocapn, quilt-cells, jev-semantic, gitpp-sheet, constraint-theory-core, quilt-atlas, elephant, hermit-crab, hodge-music-rs, midi-temporal, counterpoint-engine, Otherhere). The only names that do not exist are the ones the conversation itself coined as proposals — quilt-engine, continuum-runtime, chrono-flight, chrono-shell — and the first three of those were created as scaffolding repos in this sorting pass. A reference in these files is a checkable pointer, not a claim to take on faith — but check it at its home, because several in-text links are malformed and point at the wrong place.

## Correction of record — how to read the source files

Three things about these files are easy to misread, and the first digest of them (see `docs/proposed/DIGEST-2026-10-21.md`, correction appended) got the first one wrong:

1. **The repetition is Casey's method, not echo.** He asks Gemini for two or three retry branches of the same prompt and keeps the branch with the best wording for the narrative line he is following. The discarded branches are not redundant: they are sampling. Each variant explores what else the model would think of the same question, and some contain nuggets — a named component, a mechanism, a worked detail — that the chosen branch left out because the narrative mattered more than completeness. Reading the files as "the same essay restated" misses material that exists in exactly one branch.
2. **The concepts are mostly pointers, not inventions.** Most named things in these files refer to existing SuperInstance repositories with real prior work behind them — in a collection of 5,306 public repositories (4,485 of them non-forks; count verified 2026-10-21, see FOUNDATIONS.md). When a file names `erised`, `gitpp-sheet`, `jev-semantic`, `snapkit-v2`, or a dozen others, it is referencing researched, partially built work, not coining a term in the conversation. The pointer is not proof the thing is true or finished; it is proof the idea has a history and a home where its actual state can be checked.
3. **Voice matters.** The files mix two voices. Casey's own prompts carry his doctrine and his ground truth — the fishing-fleet material in `gemini18.md` is his, written from the water. Gemini's replies elaborate, formalize, and sometimes inflate. Provenance is annotated per file in the header now prepended to each `docs/proposed/geminiN.md`.

## Confidence key

Used in this map and across the home repos:

- **SETTLED** — Casey's doctrine, or written into this repo's SPEC. Build on it.
- **PROPOSED** — coherent and specified enough to build; not yet built or decided.
- **SPECULATIVE** — asserted in the conversation; the mechanism is missing or unproven. Do not build on it without closing the gap first.
- **PARKED** — recorded here so it is not lost; explicitly not adopted.

## The map

### beam-quilt (this repo) — the architecture study

| Cluster | Status |
|---|---|
| State / transmission / algorithm ontology; the four endings (mutate, move, copy, project) | SETTLED |
| Patch and seam contract; seam handshake (SPEC P1–P6, T1–T3, G1–G3, C1–C3) | SETTLED |
| Jev pairs (sender/receiver judges, comparative picking, disagreement as signal) | SETTLED |
| Scope distillation — distill the question, not the model | SETTLED |
| Consent / opacity / irreversibility as the safety axis | SETTLED |
| Reality Matrix (Rulebook / World Guide / Bestiary) + Campaign Manifest; Operator role | PROPOSED — adoption queue |
| Marine fleet worked example (bilge-pump seam; Sitka Sound manifest) | PROPOSED |
| Fleet doctrine from gemini18: incentive-aligned prior, action-is-the-message, unique-and-actionable filter | SETTLED (Casey's own text) |
| Threat set from gemini11: pathogen envelope, epistemic immune Jevs, canonical anchor, constitutional overhead | PROPOSED (names adopted; mechanisms owed) |
| Synthesized brief (gemini10): the digest a node prepares for the operator's return | PROPOSED — adoption queue |
| Dynamic deadband (gemini3): deadband width varies with uncertainty and velocity | PROPOSED — adoption queue |

Adoption queue and conflict analysis: `docs/proposed/DIGEST-2026-10-21.md`.

### quilt-engine (new repo) — the reference implementation

Python implementation of the beam-quilt contract. Homes: the transmission envelope and `JevPair` evaluator (gemini6's version-zero build order; gemini9's concrete schemas), the question decomposition pair — TreeSplitter and ReassemblyEngine (named in gemini2) — and the tooling from gemini17: zero-config seam bootstrap, the Quilt CLI, and the seam daemon. Status: PROPOSED.

### chrono-tensor (existing repo) — the temporal layer

The 4D event format `[time/tick, channel/agent, pitch/intent, velocity/pressure]`, the scrubbable multi-track event stream, and temporal downmixing (old history compressed into background stems). Absorbs gemini22's 16-track conversation allocation as a variant allocation. Status: PROPOSED (format and stream); the continuum ontology wrapped around it in the ideation docs is PARKED (see below).

### chrono-flight (new repo) — the recorder tool

The product surface of chrono-tensor: the fleet black box. Records runs as one scrubbable stream; debugging is rewind, mute a track, replay. Includes the chrono-shell piano-roll UI. Its own repo under the one-tool-one-repo rule. Status: PROPOSED.

### continuum-runtime (new repo) — the runtime

A Rust runtime over the chrono-tensor event stream, synthesized from gemini21's three rival scaffolds. Home of the **boundary driver**: the orchestrator reduced to a high-amplitude edge node that amplifies momentum the field already has, and never commands — recorded here as a *limit* on what an orchestrator may be. Status: PROPOSED (scaffolding); its trajectory-only state doctrine is PARKED (conflicts with SPEC P4/P5).

### Back to existing homes

These clusters were never beam-quilt's to keep; the conversation touched them in passing and they return to the repos that own them:

| Cluster | Home repo(s) |
|---|---|
| Ledger mechanics: minimum-link confidence, closure, score-the-claim, question graph (gemini13) | `gitpp-sheet` (beam-quilt adopts by reference) |
| Tick economy; scars as propagated failure records (gemini9/10) | `erised` |
| The judge itself: batteries, gates, deadbands | `jev-semantic` |
| Cell record (output + why + cost) | `quilt-cells` |
| Patch envelopes | `patchwork-experts` |
| Constraint stack: Eisenstein lattice snap, Laman rigidity checks, deadband funnel (gemini15/19) | `constraint-theory-core`, `snapkit-v2` |
| Edge escalation chain: sensor → history → deadband → nano model → fleet → cloud (gemini15/17/19) | `plato-engine-block-c`, `plato-nervous`, `open-mythos-edge` |
| Subjective predictive model per node (personal JEPA) | `elephant` |
| Hardware migration under an invariant check (hermit-crab, conservation ratio) | `hermit-crab` |
| Constraint-aware compilation gates (forgemaster) | `forgemaster` |
| Repo-first agents (git history and tests as the agent's memory) | `cocapn` |
| Living map of the repo collection (quilt-atlas) | `quilt-atlas` |
| Music-math cluster: counterpoint as coordination grammar, Hodge treatments, conservation/rhythm, Kuramoto cadence treatments | `counterpoint-engine`, `hodge-music-rs`, `conservation-rhythm-rs`, `midi-temporal`, `flux-tensor-midi` |
| Moiré / twist-angle framing (gemini12) | `Otherhere` — as metaphor; the mechanism is unproven, and gemini12 contradicts gemini11 on whether inter-node drift is failure or resolution. Unresolved. |

## Parked

Recorded so the ideas are findable; not adopted, with the reason in one line each:

- **Standing-wave / trajectory-only ontology** (gemini13/20/21) — a system with no stored state cannot keep the logs, disagreement records, or stop test the SPEC depends on.
- **Lattice routing** (chrono-tensor ideation) — nodes addressed by Eisenstein coordinates, messages propagating as waves; no routing mechanism specified.
- **Spectral identity** (gemini21) — node identity as a frequency signature recognized by resonance; no recognition mechanism, and it replaces the registry/keys the consent rules need.
- **Genome mutation loops** (gemini19–22) — self-modifying prompt/genome evolution; conflicts with the consent axis (COPY never a default, haltability) until a consent story exists.
- **Cross-manifold latent seams as the default** (gemini8/9) — raw vectors between different models "without calibration," while also proposing a calibration matrix; stays a priced research option, typed profiles remain the default.
- **The hologram claim** (gemini15: each node a full reflection of the topology) — rejected in docs/07 for sharding; patches hold unique parts by design.

## How to use this map

- Building in a home repo: read that repo's docs; the conversation files are background, not spec.
- Looking for an idea from the conversation: find its home above, then read the annotated header on the source file (each `docs/proposed/geminiN.md` now begins with where its ideas live).
- Adding a new cluster: give it a home and a confidence status here first. An idea without a home drifts back into every repo at once.
