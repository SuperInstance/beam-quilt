# Foundations — what the research established before the architecture is completed

This document is the evidence layer under the architecture. Before the
design in `README.md` / `SPEC.md` / `docs/01–09` is completed or extended,
its load-bearing claims were checked against primary sources and its
mechanisms were surveyed against what already exists. The two research
dossiers are preserved in full in `docs/foundations/`:

- [`docs/foundations/LANE-A-PRIOR-ART.md`](docs/foundations/LANE-A-PRIOR-ART.md)
  — prior-art survey: 30+ systems across five domains, each tagged
  PULL / ADAPT / STUDY / REJECT against beam-quilt's own constraints.
- [`docs/foundations/LANE-B-FACTCHECK.md`](docs/foundations/LANE-B-FACTCHECK.md)
  — claims fact-check: every load-bearing factual claim in the programme's
  documents, checked claim by claim, with verdicts and sources.

## Method

Two research lanes ran on the Jetson on 2026-10-21 (ZCode), one per
dossier. Both lanes worked from primary sources — the repos, papers, and
official docs themselves — and marked anything they could not verify
UNVERIFIED rather than repeating it.

**Referee rule: no verdict was relayed unverified.** Before merge, Muse
independently spot-checked five decisive items and confirmed all five:
the Dec-POMDP NEXP-hardness result, the 2D scope of Laman's theorem, the
account repo count (5,306 public / 4,485 non-fork), the Anthropic ARA
figures against the original post, and the MIDI bandwidth arithmetic.
What follows is the condensed, architecture-facing version of the two
dossiers; where this document and a dossier disagree, the dossier's
citations win.

## The pull list

What the survey found genuinely worth taking, condensed from Lane A's
closing pull list. Pull means *fit*, never popularity: each item below
matched the design's constraints (sovereign patches, no shared state,
influence only as refusable proposals, edge hardware, owner consent) in a
way the alternatives did not.

### PULL

- **BEAM process and supervision semantics** (Erlang/OTP, Apache-2.0) —
  for `continuum-runtime`. Per-process heaps with copied messages,
  selective-receive mailboxes (a patch's inbox is the natural
  seam-arbitration chokepoint), supervision trees with restart-intensity
  escalation, monitors/links, two-generation hot code swap. Explicitly
  **not** pulled: distributed Erlang's full-mesh, cookie-trust,
  kill-the-loser name-resolution layer — it assumes the uniform-trust
  cluster edge hardware is not.

### ADAPT (take the mechanism, rebuild the surface)

- **LTTng session/channel/ring-buffer semantics** — for `chrono-tensor`.
  Session = the fleet stream; track = channel, independently
  enabled/disabled (= mute/solo); per-channel discard-vs-overwrite policy
  makes the ring-buffer GC choice explicit; rotation/chunk archives and
  snapshot mode = downmix extraction; per-user/per-process buffer
  ownership is the shipped analogue of owner-consented recording.
- **Kafka's log-compaction contract** — for `chrono-tensor`'s downmixing
  rule, currently named but not specified. Encode as the contract:
  addresses (tau) never change, order is preserved, latest value per key,
  tombstones for deletion, current state rebuildable from the tail alone.
  (Kafka the broker is rejected: a central-cluster JVM system.)
- **OpenTelemetry span context / W3C context propagation** — for
  `quilt-engine`'s transmission envelope, replacing a bespoke provenance
  encoding. Trace/span IDs plus links are provenance-light; attributes and
  events carry intent and cost; propagation formats buy interop with
  existing edge tooling. OTel defines no storage, no replay, no tracks —
  the envelope only, not the backend.
- **Chronicle Queue's named tailers and memory-mapped append-only
  cycles** — for patch-local logs (SPEC P5) and `chrono-flight`'s
  playback cursors. Named, persistent, read-without-consume tailers are
  exactly "each sovereign reader keeps its own scrub position";
  cycle-rolling is the retention seam where downmix/archive hooks in.
  Rebuild in Rust rather than vendoring the Java original.
- **Conformal quantiles + SGR binomial-bound calibration** — for the Jev
  pair's band edges in `quilt-engine`, replacing hand-set go/no-go
  thresholds with formula-shaped edges set from a small calibration set
  (SPEC rule G2, with an auditable statistical story). Two inversions are
  required: band the *receiver's* scores, not a model's self-assessment;
  and let an ambiguous middle trigger a narrower question, not a bigger
  output set. Sender/receiver divergence is non-exchangeability to be
  logged, not noise to be absorbed.
- **Mozannar & Sontag's comparative-defer loss + Switch Transformer's
  divergence logging** — for the Jev pair's comparative pick and the seam
  handshake's divergence record. The cross-entropy-over-classes-∪-{defer}
  shape is the credible starting formula for a receiver score that must
  beat the competing pool *and* the option to defer; Switch's
  dispatched-fraction-vs-mean-probability term is the template for logging
  divergence-from-uniform (rule G3).
- **Temporal's position-matched command/event replay discipline** — for
  `chrono-flight` / `continuum-runtime`, replacing any naive notion of
  replay. Log transmissions so re-derivation is positionally checkable;
  treat every model call as an activity — recorded, never re-executed;
  detect drift as a positional mismatch instead of pretending
  determinism. This enforces the replay limit rather than contradicting
  it. (Temporal the platform is not pulled: it requires a database
  service and datacenter deployment.)
- **DecomP's typed decomposition with a base-case stop rule** — for
  `quilt-engine`'s TreeSplitter. Keep the named-handler type system and
  the decompose-until-small-enough recursion; invert DecomP's bet — our
  typed angles are static (a fixed schema), not LLM-generated programs,
  so the decomposer need not be the smartest model in the fleet.

Smaller patterns worth taking (mechanisms, not systems): Letta's
memory-as-git-repo for owner-readable, versioned patch state; Petals'
blacklist → reroute → regenerate-caches sequence and Hivemind/SWARM's
"rebalance the topology around the departed node" as the two proven churn
answers; WinDbg TTD's ring-recording mode, keyframe index, and Timelines
lanes as `chrono-flight` UI precedent; rr's cost-up-front documentation
style as the honesty bar for our own claims.

### Build our own — where nothing off the shelf fits

- **The seam itself.** Receiver-side comparative admission with a
  deadband whose honest outcome is a *narrower question* is unclaimed
  across every agent framework surveyed (AutoGen, LangGraph, CrewAI,
  Letta, OpenAI Agents SDK) **and** across the gating literature
  (selective prediction, conformal prediction, cascades, learning to
  defer, MoE routing). Every existing gate is sender- or
  orchestrator-decided; every abstention is terminal or a silent
  fall-through. The closest misses triangulate the design — defer's
  comparative third outcome, CALM's refinement-instead-of-abstention,
  LangGraph's narrower-question loop — and beam-quilt's contribution is
  re-siding all of it to the receiver. **This is the genuinely unbuilt
  piece, and it is ours to build.**
- **The fleet flight recorder.** Every mechanic exists somewhere (Lane A
  §4) but has never been assembled owner-consented and fleet-wide. The
  assembly — consent boundaries, per-patch logs into one multi-track
  fleet stream, playback without re-execution promises — is ours.
- **Latent seams between different models: keep parked.** Stitching works
  only with a trained low-capacity translator fit on paired activations
  from the task distribution run through both networks (5K–25K+ samples in
  the kindest, same-architecture, vision-only results; no LLM
  demonstration anywhere; identity transfer fails catastrophically). The
  un-park condition is concrete: name the calibration corpus, the
  stitch-point granularity, and the acceptance test first. Typed textual
  payloads remain the evidence-backed seam.
- **Churn-tolerant pooling of home hardware for inference.** No surveyed
  system fits sovereignty — all shard one shared model, none let a
  receiver refuse, none log influence. Learn prima.cpp's
  heterogeneity-aware placement and Petals' recovery sequence; build the
  sovereign version.

## The verdict table

Condensed from Lane B. Full reasoning, quotations, and sources:
`docs/foundations/LANE-B-FACTCHECK.md`.

| Claim | Verdict | The finding in one line |
|---|---|---|
| Anthropic ARA figures (Gemma 0.39 tok/s on 15 TV boxes; 9B setup on 8/9 hardware types; GLM-5.2 8/8 nodes in 4/5 trials; Memento 45% → 61–84%; +13.7 ECI/yr) | **VERIFIED** | All five confirmed against the primary post, with measurement vs. projection correctly separated in our text |
| Account scale ("roughly five thousand repos") | **VERIFIED, narrowed** | 5,306 public repositories in total, of which 821 are forks — the non-fork count is 4,485; state it that way |
| TUTOR in use at the FAA until 2006 | **VERIFIED** | The FAA retired its PLATO system in September 2006, the last CDC Cyber PLATO install |
| TUTOR shared-memory "common blocks" | **VERIFIED** | Up to 1,500 shared 60-bit words per lesson, attached explicitly — the closest historical analogue to a seam |
| Gooch instruments (GSW, 16-voice GCS) | **VERIFIED** | GSW is 4-voice; the later GCS is the 16-voice one — our sentence attaches the number to the right instrument |
| Empire "30 players, 1973" | **MISLEADING-SCOPE** | The 1973 original was 8-player turn-based; the 30-player real-time cap is Empire IV's, by 1977, at ≤15 players per team, over 1200 baud with 1–2 s screen updates |
| Avatar "60 concurrent users" | **MISLEADING-SCOPE** | 60 is system-wide concurrency; a dungeon group was ~15; the "closed gold economy" detail is unsourced |
| OPAL integrated into TUTOR; TUTOR compiled and "distributed" | **MISLEADING-SCOPE** | OPAL (name and expansion verified) was a separate PLATO-compatible language with its own compiler and editors; TUTOR was interpreted, and the 60-bit words never left the host — the wire carried a character stream |
| "Parsed in 1–2 clock cycles via barrel shifts" | **REFUTED** | TUTOR was interpreted; the CDC 6600's fastest functional unit is 3 cycles; "barrel" names the I/O round-robin, not a data-path shifter |
| OPAL → MIDI lineage ("MIDI inherited this blueprint") | **REFUTED** | MIDI's documented lineage is Oberheim/Roland DCB → the 1981 Universal Synthesizer Interface → the 1983 spec; no PLATO/CERL/OPAL connection exists in any source. The correct verb is *converged on*, not *inherited* |
| MIDI "sub-millisecond temporal alignment" | **REFUTED** | The arithmetic: 31.25 kBaud with 10-bit framing = **3,125 bytes/s**; a 3-byte message takes **~0.96 ms** of wire time; the best synchronisation available is 8.33 ms (MTC quarter-frame at 30 fps). 16 tracks are real; per-track ceiling is ~65 events/s |
| MIDI "near-zero overhead" | **MISLEADING-SCOPE** | True only of the 1-byte status header (33% of a 3-byte message); bought by giving up checksum, sequence numbers, and resynchronisation — one dropped byte silently corrupts the stream |
| "Truth is mathematically defined as a rigid graph" (Laman) | **REFUTED** | Laman's theorem is a 2D bar-joint result (no 3D analogue is known), and rigidity is geometric determinacy, not a truth predicate — a rigid graph can be rigidly wrong |
| Dec-POMDP "convergence to a globally optimal tactical map" | **REFUTED** | The finite-horizon policy-existence problem is NEXP-complete (Bernstein et al. 2002); no general convergence or optimality guarantee exists. Comparative picking — a local, per-pool operation — is the tractable design, and it is already ours |
| A₂ / Eisenstein lattice optimality | **MISLEADING-SCOPE** | A₂ is the densest lattice in **2 dimensions** only; the theorem says nothing about high-dimensional intent vectors. The hexagonal *time* grid remains a sound design choice — on neighbour structure, not optimality |
| Kuramoto entrainment as the sync mechanism | **MISLEADING-SCOPE** | The model needs one continuously advancing phase scalar per node, observable phase differences (which presuppose a shared clock), weak coupling, near-identical frequencies. The ideation prose also misstates the threshold condition (ωᵢ is a natural frequency, not a variance). As used here it is a cadence heuristic; no oscillators are coupled |
| Orthogonal Procrustes for cross-model latent alignment | **MISLEADING-SCOPE** | Classical Procrustes requires **paired correspondences**; it cannot align independently trained models from nothing. This is exactly why the default seam is typed, not latent |
| Hodge decomposition of seam friction | **MISLEADING-SCOPE** | Hodge needs an oriented, weighted simplicial complex with 2-simplices and an edge flow; two proposals are none of those. The three-bucket triage it inspired (noise / loops / structural bottlenecks) is a good taxonomy — presented as a taxonomy, not a decomposition |
| Tiny judge: 6.8M params, ~7 ms on ARM | **PLAUSIBLE, with caveats** | The arithmetic fits published edge-inference numbers for one forward pass — but the artifact is not public, so the figure is not yet auditable, and a 6.8M transformer cannot read a long prompt in 7 ms (a 512-token prefill is ~seconds). Scope the claim to short typed profiles, and publish the model plus a benchmark to make it checkable |

## What this changes in the docs

Each refuted or narrowed claim was mapped to the documents that repeat
it. The four sibling repos were grepped for `sub-millisecond`, `rigid`,
`globally optimal`, `5,000`, `OPAL`, `lineage` (report only — those repos
are corrected by their own maintainers, not from here); beam-quilt itself
was grepped the same way and fixed in place.

### Fixed in beam-quilt (this merge)

- `docs/04-jev-pairs.md` — the "~7 ms on ARM" figure is now scoped to
  short typed profiles, with a pointer here.
- `README.md` — the same scoping applied to the jev-semantic line.
- `PROGRAM-MAP.md` — "roughly five thousand repos" replaced with the
  verified count: 5,306 public repositories, 4,485 non-fork.
- `docs/06-moe-modularized.md` — precision addition per Lane B: the 9B
  setup agent is named (Qwen3.5-9B) and the measured task named
  (standing up Llama3.1-8B inference), which is what makes the 8-of-9
  figure comparable across models.
- Checked and deliberately left alone: `docs/08-lineage.md` and the
  README's chapter listing (a Fortran/BEAM/model-as-file lineage — no
  OPAL or MIDI claim); `docs/05-scope-distillation.md`'s "lineage of
  identical small models" (generic usage); `PROGRAM-MAP.md`'s pointer
  naming "Laman rigidity checks" as another repo's cluster (a map entry,
  not an endorsement of rigidity as truth).

### chrono-tensor — builder docs already correct; ideation carries the overclaims

The builder docs need **no correction**: `ARCHITECTURE.md:112` scopes
Laman correctly ("a rigid graph can be rigidly wrong, so rigidity is
never a test of truth") and `ROADMAP.md:19,24` frames rigidity checks as
an under-specification heuristic. The refuted assertions live in
`docs/ideation/`, which is provenance only and already header-annotated
as such — they must not be quoted into builder docs:

- `docs/ideation/gemini1.md` (15 term-matching lines) — the load-bearing
  ones: `:263` "native sub-millisecond temporal alignment"; `:196`,
  `:495`, `:534` "sub-millisecond" serialization/envelopes; `:330`,
  `:468`, `:549` "Truth is mathematically defined as a rigid graph";
  the Kuramoto threshold misstatement (Lane B §3.4).
- `docs/ideation/gemini2.md` (55 term-matching lines) — the lineage
  essay: `:111` OPAL "integrated directly into TUTOR… vectorized… 
  ultra-compact"; `:144`, `:149`, `:188`, `:296` the OPAL→MIDI lineage
  framing (refuted: convergence, not inheritance); `:93`, `:128`, `:240`
  the same lineage asserted as the stack's ancestry.
- `docs/ideation/gemini3.md` (39 term-matching lines) — `:59`, `:129`,
  `:239`, `:326` "Truth is mathematically defined as a rigid graph";
  `:208`, `:295` "sub-millisecond event bus"; `:11` OPAL streams folded
  into the CTC lineage claim.

### quilt-engine — no hits

No occurrence of any checked term. Nothing to correct.

### continuum-runtime — one hit, already correct

- `ARCHITECTURE.md:167` — a parked-vocabulary table row that names
  "Laman rigidity = truth" in order to reject it ("determination is not
  truth"). Correct as written; no change needed.

### chrono-flight — no hits

No occurrence of any checked term. Nothing to correct.

### Standing follow-ups (from Lane B, not yet done)

- **jev-semantic**: publish the judge artifact (ONNX or its hash), a
  one-command benchmark (input shape, quantization, board, median of N),
  the judge's explicit input contract, and the quantization contract
  behind the "bit-identical across architectures" claim — or soften that
  wording to "reproducible to within tolerance."
- **chrono-tensor ideation**: the propagation fix Lane B recommends is
  alignment of prose, not redesign — the architecture doc already says
  the correct thing about all six mathematical mechanisms.

## Ruling recorded 2026-10-21 — deadband

Casey rejected both framings previously offered for the deadband —
clarify-only, and clarify plus abstain as a fourth outcome. The ruling
is the autopilot mechanism: a deadband is a **(band, yaw-time)** pair
around the receiver's predicted/expected course. Inside the band, the
receiver holds and makes no adjustment; outside the band but within
the yaw-time, it holds and watches; only an excursion that persists
beyond the yaw-time triggers action. The band and yaw-time are
context-set, learnable parameters, with conformal calibration as the
named method for setting band edges from data.

This replaces the older wording wherever it appears. **Abstain is not
a fourth outcome** — coasting is inside-band behaviour. Likewise,
clarify/narrow is not the meaning of the deadband; it is one possible
response after an Act trigger, alongside accept-best and escalate.

## Standing rule

**Foundations before architecture completion.** No chapter, spec clause,
or builder doc in this programme asserts a capability, a number, or a
lineage that has not been checked against a primary source — and where a
check narrows a claim, the narrowed version is what the docs carry.
Quality over efficiency throughout: we pull prior art only when it fits
the constraints, never because it is popular or near to hand, and we
build our own where the fit is wrong — the seam itself being the standing
example. New load-bearing claims enter through this file: checked first,
then written down.

## Shelf foundations — the two active items

Two further lanes ran on 2026-10-21 for the foundation Casey restated
that day — Lane C, a survey of the existing SuperInstance shelf, and
Lane D, a sweep of the local record for prior formulations. Both
dossiers are preserved in full in `docs/foundations/`:
[`LANE-C-SHELF.md`](docs/foundations/LANE-C-SHELF.md) and
[`LANE-D-LOCAL.md`](docs/foundations/LANE-D-LOCAL.md).

**The anatomy.** Representing a system takes two active items. The
**active log** is an item's current readings of every cell *from its
own perspective*: each entity holds first-class knowledge of itself,
and that is truth *for it* — there is no God's-eye view, only
perspectives that learn from each other through the compressed
payloads they exchange (a quorum of blind men holding legs still does
not have the sighted view of the elephant). The readings are **dated**
— fog of war: they age, the fog returns, and each item operates on a
predictive model of the other side. The **active ledger** is not a
ledger of account values but of **deductions and contributions**:
payloads out of one item and into another, recorded by the sender as a
SEND and by the receiver as a RECEIVE — and the *agreement* between
the two sovereign records is what mints the shared fiction that is
operational. The first-class citizen is the **observer's own state**,
not the thing observed: a reading of another cell is an abstraction
*of the reader's state*, produced by the reader's log — there is no
reading independent of a reader. Two views come into contact only
through ledger agreements; the rest stays hidden, and it is hidden
*structurally*, not cryptographically — there is no God's-eye record
anywhere that could be decrypted, because none is ever made. (Casey's
game form of the same point, 2026-10-21: the streaming ins and outs
between players as they confirm the logic between them — agreements
determine exactly where everything is, while each side's view remains
its own.) A **contract is an algorithm**: the agreed algorithm
governing a crossing is itself an early ledger entry, an agreement
about future sends and receives; and committing is the bridge that
resolves uncertain log-state into ledger agreement. Consequence: there
is no universal stored state to appeal to — state lives in each item's
active log (perspectival, dated); shared state is never stored
anywhere, it exists only as agreed ledger entries.

**The lineage, in layers (Lane D + Lane E + the deep-research
lineage).** The flat October chain was the local record, not the whole
history. Read the lineage as layers of maturity: a paradigm, its early
expressions, components accreting around it, and finally the doctrine
stated in the terms this programme now uses. Dates below date
artifacts — local notes, repositories, commits — not the paradigm
itself. The full records are
[`LANE-D-LOCAL.md`](docs/foundations/LANE-D-LOCAL.md),
[`LANE-E-DATES.md`](docs/foundations/LANE-E-DATES.md) and
[`DEEP-RESEARCH-LINEAGE.md`](docs/foundations/DEEP-RESEARCH-LINEAGE.md).

- **Layer 0 — the paradigm (Casey's, pre-GitHub, years).** The
  foundation — as a user paradigm and as a developer paradigm — predates
  Casey's GitHub membership; in his words on 2026-10-21, this is how he
  has thought AI should go for years. **This layer is recorded as his
  statement, not as a publicly dated artifact.** The deep-research lane
  verified the public record from the account's creation
  (2024-12-29) forward; it cannot date what came before GitHub, and the
  one predecessor pointer it found (`ctothed/SuperInstance`, referenced
  inside SuperInstanceEco's README) is **UNVERIFIED** — that account
  returns 404, so no dates or contents could be checked. The early
  projects below were, in Casey's own assessment, immature expressions
  of this paradigm — expressions of it, not its origin.
  **Provenance caveat (Casey, 2026-10-21): the public record is a
  survivor set, not a complete archive.** Before moving his work to
  GitHub, Casey worked locally; a OneDrive sync incident deleted a
  large amount of the original SuperInstance projects, and he does not
  know what was saved and what was lost. Some repos — including
  fragmentary or placeholder-looking ones — are re-uploads of whatever
  fragments survived that loss, particularly from his 2025 work. Read
  gaps, stubs and late creation dates accordingly: absence in this
  record is not evidence of absence in the work.
  **Naming (Casey, 2026-10-21):** for a long time the whole system was
  called **Active Log** — the name predates "SuperInstance" entirely.
  ActiveLog was to be the back end, and the cluster of log-ending
  domains he purchased were to be its various front ends. The step-back
  realisation was that a **super instance** is what the thing is no
  matter what shape you give it — the shape-independent concept above
  any back end or front end — and the name stuck (SI, echoing the
  super intelligence idea he was drawn to at the time). This is why
  "ActiveLog" names a 2025 product line: it was the system's own first
  name, later demoted to one component's.
- **Layer 1 — early expressions, 2025.** The **ActiveLog product
  line**: `activelog-claude` (created 2025-08-19), `activelog`
  (2025-08-23) and `SuperInstanceEco` (created 2025-08-31, root content
  dated 2025-10-22), whose README is the “SuperInstance - ActiveLog
  Technical Repository” — event-driven logging applications
  (voice-note, Deckboss and business loggers), AWS microservices, and
  **CQRS / event sourcing** as named architecture. The instinct is
  legible here — the log as a first-class record — but the content is
  conventional activity/event logging, **not** the paired doctrine; the
  flagship-named `ActiveLog-MVP` and `ActiveLog-TechnicalRepo`
  (2026-01-10) are empty placeholders, and the real content lives in
  SuperInstanceEco. Alongside: **SmartCRDT** (2025-12-22), the earliest
  dated distributed-state work in the public record — convergent state,
  adjacent to the paradigm, in different vocabulary.
- **Layer 2 — components accreting, 2026.** The pieces gather, still
  un-unified. **Cells and spreadsheet form:** SmartCRDT (Layer 1) was
  the springboard to **`polln`** (2026-03-06) — “SuperInstance
  Visualized in Spreadsheets for Tile Intelligence” — which turned the
  thinking into the spreadsheet form, alongside the realisation that
  the main back end the paradigm needed did not yet exist.
  `SuperInstance-papers` (2026-03-10) — the repo where Casey's AIs wrote
  formal papers on each issue as it was discussed (conservation of
  intelligence, semantic distance, and more) — deconstructs logic into
  spreadsheet tiles and cells; `Polln-whitepapers` and `Spreadsheet-ai`
  land the same day. **`spreadsheet-moment-proto`** (2026-03-14) carries
  the thesis Casey named for it: AI's ChatGPT moment was a *shell* — one
  AI in a terminal; the spreadsheet moment puts many AI shells in a
  grid where they address each other **by spreadsheet location** rather
  than by any addressing system of their own, working agentically in
  the background, so a human (or an agent) can think about the *logic*
  of an agentic system agnostic of its coding and porting — coding-agent
  systems form an optimisation layer *behind* the spreadsheet, and the
  spreadsheet itself is a **middle layer** that can front anything: a
  UI, or every sensor on a boat ported to an LED screen mocked up as
  gauges. (The thread Casey discussed with a Devin co-founder; the
  current “spreadsheet moment” project is another builder's, which
  sidetracked him — the proto here is his.) The thread matures in
  `quilt-spreadsheet` (2026-09-23): cells as programs, double-entry
  bookkeeping, backend porting, distributed clocks. The profile repo
  (2026-04-11) states the concept — “The spreadsheet that thinks.” **Ledger precursors:** `lau-conservation-engine`
  (2026-05-31, double-entry energy ledger with transfer verification),
  `simulation-ledger` (2026-06-07, append-only simulation ledger with
  replay), `saddle` (2026-08-23, double-entry ledger per cell).
  **Quilt and perspective:** `quilt` and `elephant` both land
  2026-08-17 — the grid as a runtime of live, addressable cells, and
  the perspectival line already in elephant's description: “every
  reading is someone's reading.” **Travel shape:** `edge-ledger`
  (2026-09-30) — per-node hash-chained envelopes, idempotent ingest,
  loud fork rejection, pure projections.
- **Layer 3 — the doctrine stated, October 2026.** **2026-10-05,
  local notes (cordis-lab, the two spreadsheets):** active log = data
  sitting in cells (state at rest); active ledger = how data changed
  moving to the next cell — transitions, algorithms, agreements,
  custody, always double-entered (“deducted here, added there”), the
  algorithm part of the entry. **2026-10-08, quilt-rooms — the first
  public pairing, and a working implementation:** each room keeps a
  sovereign active log; between rooms, an active ledger of agreed
  links; cross-room time is itself an agreement, not a clock; the same
  day's notes add that committing resolves uncertain log-state into
  shared agreement. **2026-10-10, the cellular agent:** “an agent is
  really these two logs” — the active log is what the agent *is*
  (state-at-a-time); the active ledger is its contracts, the agreed
  algorithms for sending and receiving. “In human terms, a contract is
  an algorithm.” **2026-10-12, the hinge:** record primary, state
  derived — state as a fold over records. **2026-10-21, the
  generalization:** agents and rooms become **items** (deliberately
  generic — not necessarily objects); the log becomes perspectival,
  dated **readings**; the ledger's emphasis returns to **movements**
  (deductions/contributions, SEND/RECEIVE agreement). Nothing earlier
  was retracted — scope widened, the log went temporal, the two halves
  of the ledger (contracts ex ante, movements ex post) compose.

**What to tap (Lane C).** The shelf already settles the components
around the core; cite and reuse the mechanisms — **import nothing
wholesale** (the anti-bloat rule). In brief:

- **quilt-rooms** — the direct prior art: the named pair
  (`ACTIVE-LOG.md` / `ACTIVE-LEDGER.md`), sovereign local sequence
  time, both-sides-record, pending→agreed lifecycle, scoped universe
  time. Cite it as the source of the log/ledger split. Its ledger
  agrees on *time*, not on payload transfers — see the gaps.
- **quilt-chrono** — paired flows for the log side: every cell read is
  a "reading," every write a "writing," one `flow_id` linking source
  read to sink write; compensating corrections (history never
  rewritten); current state as a pure projection (`stateAt`).
- **edge-ledger** — the travel shape for local truth: per-node
  hash-chained envelopes, idempotent ingest (exact redelivery is an
  acked no-op; same seq, different hash is a fork, rejected loudly) —
  the settled answer to what a SEND looks like on the wire and what a
  RECEIVE does on ingest.
- **gitpp-sheet** — confidence and closure for readings: minimum-link
  confidence (never mean, never product), score-the-claim-not-the-
  record, latest-reading-wins, and the closure property — a ledger
  sufficient when a verdict reproduces from it with no judge call.
- **elephant** — perceiver-owned readings: every reading is someone's
  reading, subjective and experience-shaped; a reading is a **delta**
  (field after minus field before), and that before→after pair is the
  directed edge a cell ledger records.
- **naDir** — the async lifecycle for contributions: open → heartbeat
  → close, with a reaper for stale entries; source→destination
  provenance fields. (Its close is unilateral — provenance, not
  bilateral agreement.)
- **quilt-cells** — the record shape for what a log holds:
  `INPUT → OUTPUT + WHY + COST` — a reading stays interpretable and
  priced when it later crosses into a ledger.

Full per-repo findings, including the ADAPT-grade items (cocapn's sxc1
fail-closed seam dialect, constraint-theory-core's exact snap,
gitpp-oracle's ledger governance, ledger-continuity's recovery
semantics, erised's scar rule) are in
[`docs/foundations/LANE-C-SHELF.md`](docs/foundations/LANE-C-SHELF.md).

**The three gaps — the genuinely unbuilt pieces.** Lane C found no
repo implementing:

1. **A per-observer tensor of dated readings** — per item, per cell,
   each reading dated, with explicit staleness and a predictive model
   filling the fog. Parts exist (quilt-chrono's recorded readings,
   gitpp-sheet's aged readings, elephant's subjective readings); the
   structure as one thing does not.
2. **Two sovereign ledgers whose agreement mints shared state for
   payload transfers** — quilt-rooms agrees on time, edge-ledger's ack
   comes from a store, naDir's close is unilateral. SEND booked by one
   side, RECEIVE booked by the other, shared state existing *only* in
   their agreement — the heart of the restatement — is unbuilt.
3. **Contract identity bound into each ledger entry** — the algorithm
   (id / hash / version) that governed a crossing recorded *in* the
   entry itself. sxc1 seals a dialect and gitpp-sheet's guards
   adjudicate, but no repo binds the governing contract into the
   transfer record. "A contract is an algorithm" remains a definition
   to build.

These three are beam-quilt's own work, the same shape as the seam
itself in the pull list above: the components are settled on the shelf;
the composition is ours.
