> **Provenance:** Lane C (GitHub shelf survey) for the two-active-items foundation, 2026-10-21.
> **Sources:** the SuperInstance GitHub shelf, read via the Oracle (`gh`, logged in as SuperInstance) — READMEs, specs and key source files through the contents API; tests were not re-executed in this lane.
> **Standing:** merged into beam-quilt as part of the foundations layer (see FOUNDATIONS.md, "Shelf foundations — the two active items"); TAP/GAP findings here are the record the architecture docs must agree with.

# Lane C — SuperInstance shelf survey for the two-active-items foundation

**Date:** 2026-10-21 (AKDT) · **Lane:** C (shelf research, Jetson lanes A/B were prior-art/fact-check)
**Access:** GitHub via the Oracle (`gh`, logged in as SuperInstance). READMEs, specs and key source files were read through the contents API; maturity judgements are from repo docs + file trees. **Tests were not re-executed in this lane** — test counts below are the repos' own claims unless a test tree is cited.

## The foundation being tested against

Casey's restated anatomy (2026-10-21, deepening 2026-10-08/10-12): representing a system needs **two active items** —

1. **ACTIVE LOG** — an item's *current readings of every cell from its own perspective*. Readings are **dated** (fog of war: they age, the fog returns, you operate on a predictive model of the other side). There is **no God's-eye view**; each entity's own state is first-class truth *for it*. Devices, storage and memory blocks are not fundamentally different kinds of thing.
2. **ACTIVE LEDGER** — *not* a ledger of account values, but of **deductions and contributions**: payloads recorded by one side as a **SEND** and by the other as a **RECEIVE**; **their agreement is what creates the shared fiction that is operational.** Earlier formulation: the ledger holds the **contracts/algorithms** governing boundary crossings ("a contract is an algorithm"), and committing resolves uncertain log-state into shared agreement.

**Test applied to every repo:** does it actually implement a mechanism serving the log, the ledger, both, or the contract layer between them — or is it a name collision? **Anti-bloat rule (Casey's):** tap the foundation, don't import it. The preferred outcome is "this repo already settled X — cite it," not "depend on this codebase."

## Headline findings

- **One repo is the direct prior art: `quilt-rooms`.** It names the exact pair — `ACTIVE-LOG.md` / `ACTIVE-LEDGER.md` — and implements sovereign local sequence time, hash-chained append-only logs, pending links, and bilateral agreement minting a scoped "universe time." It is a small shell prototype, and its ledger is about *temporal agreement for messages*, not deduction/contribution accounting — but the vocabulary and the sovereignty rules are already settled there. **Cite it as the source of the log/ledger split.**
- **No repo implements the full restated anatomy.** In particular, nobody has: (a) a tensor of dated, perspectival readings over *every* cell with explicit staleness/prediction; (b) bilateral SEND/RECEIVE records held sovereignly on both sides whose *agreement* mints shared state for payload transfers (quilt-rooms agrees on *time*, edge-ledger gets an *ack* from a store); or (c) a generic contract-as-algorithm layer whose version/hash is bound into a ledger entry. Those three are the genuinely unbuilt remainder — the same shape as Lane A's conclusion about the Jev pair.
- **The components around that remainder are unusually well settled** — by `quilt-chrono` (cell read/write ledger + time projections), `edge-ledger` (the travel shape for local truth), `gitpp-sheet` (confidence propagation + receipt sufficiency), `jev-semantic` (judgment keying + disagreement-as-data), `naDir` (async work provenance lifecycle), and `cocapn`'s sxc1 dialect (fail-closed cross-repo envelope verdicts).
- **Terminology check:** account-wide code search for the foundation's own phrases found them almost exclusively in `AI-Writings` (prose/canon) — `"contract is an algorithm"` returns **no code hits at all**, and `deductions`/`contributions` hits are generic prose and CONTRIBUTING files, not mechanisms. The two-items anatomy is Casey's synthesis; the shelf holds parts, not the named whole.
- **Existence flags:** `quilt-study` **does not exist** as a GitHub repo (not under SuperInstance, not under patchwork-quilt; owner search returns nothing — the name survives from local study notes, not a repo). `nadir` exists as **`naDir`** (case variant). `patchwork-experts` resolves to **`Patchwork-experts`**. Every other candidate exists and is public.

## Per-repo findings

### 1. quilt-rooms — TAP · both items · prototype, end-to-end tested by hand

- **Mechanism (actual):** Each room is a directory and a sovereign universe. The **active log** is per-room append-only event files (`log/000001.json`…) with a local monotonic `seq` that *is* the room's time (wall clock advisory only), hash-chained (`prev`/`hash`), one writer only. Cross-room messages go outbox → inbox with a *proposed* universe time; the receiver logs `heard` at its own seq and writes a *pending* link; when both sides propose the same universe time, both write the link to their own `ledger/` copies and log `agreed`. Disagreement converges by higher-number-wins, game-master tiebreak, or negotiation; after 3 rounds a link is `disputed` and stays pending without blocking traffic. Universe time is **scoped** — no global clock; a room in three conversations has three universe timelines, tied together only by its own seq.
- **Serves:** Log (sovereign local timeline) + Ledger (agreed inter-room links). The closest existing statement of Casey's pair, including "no room can write to another room's log / dictate its time / force agreement."
- **Maturity:** Small prototype (10 files, 4 shell scripts in `ROOM-TEMPLATE/`), README claims send → receive → agree tested end-to-end with disagreement detection; no automated test suite.
- **Gap vs the restatement:** Readings are events, not dated cell-readings with staleness; the ledger agrees *when* a message happened, it does not book a deduction/contribution pair. **Tap the vocabulary, sovereignty rules, pending→agreed lifecycle and scoped-time doctrine; do not mistake it for the whole foundation.**

### 2. quilt-chrono — TAP · both items (one ledger, projected views) · built, 59/59 tests claimed

*(Caught by search; not on the candidate list.)*

- **Mechanism (actual):** A tiny reactive cell core where **every cell read is a "reading" and every write a "writing,"** all flowing through an append-only JSONL time ledger (`quilt.chrono.entry/v1`, gapless seq, strictly increasing timestamps, entries frozen at append). Push flows are double-entry in spirit: one `flow_id` links the source read and the sink write, and `ledger.balanced()` fails any flowed write without a paired read. Corrections are **compensating entries** (`cause:"correction"`, `corrects:<seq>`) — the original stays byte-intact; "a rewind does not erase — it appends." Projections are pure: `stateAt(t)`, `diff(t1,t2)`, `flowMap`, SVG/table renders, hash-pinned snapshots + restore. A sidecar hash chain + signed checkpoints (HMAC-SHA256 or Ed25519) make the ledger tamper-evident without touching its bytes; custody law is deliberately owned by another repo's organ protocol and matched byte-for-byte, "no custody law re-decided here."
- **Serves:** The strongest existing machinery for the **log side as a ledger of readings**: dated entries, current state as a projection (`stateAt`), history never rewritten. Its "tide" (min-interval propagation where attempts are *held and journaled*, latest value rides the next crest) is also a concrete, tested relative of hold-vs-act behaviour.
- **Maturity:** Built and tested — README reports 59/59 `node --test` green including cross-repo Ed25519 seal proofs against the organ toolkit's own verifiers; worked tide example with a 547-entry ledger and receipts.
- **Tap:** entry shape (seq/ts/op/cell/by/cause/flow_id), compensating-correction rule, balanced-flow check, projections-as-views doctrine. This settles "how a cell's readings get recorded and replayed" — cite it instead of designing a new log format.

### 3. edge-ledger — TAP · ledger (travel shape) over per-node logs · built, pinned by tests

*(Caught by search; not on the candidate list.)*

- **Mechanism (actual):** `fleet-state@v1` — the sync contract for when a node's local truth must cross a network. Each node emits envelopes `{schema, node, seq, prev, tip, kind, payload, sig}` forming a **per-node hash chain** with monotonic seq; kinds are `state-delta`, `receipt-batch` (local `quilt/cell-receipt@v1` records carried *verbatim* and verified before ack), and `lane-heartbeat` (liveness only — heartbeats must not carry result-shaped fields). Ingest is **idempotent**: exact redelivery of `(node, seq)` is an acked no-op; same seq with a different hash is a **fork, rejected loudly** with the offending hash quoted. HMAC signing with the key outside the agent's writable context; unsigned is a declared state (`sig: null`), never backfilled. Projections (fleet boards) are pure functions over envelopes and must recompute to the live board. Explicitly **not a CRDT**: merge is a projection concern, never a write concern.
- **Serves:** Ledger — specifically the *boundary-crossing* half: how sovereign local records travel and get accepted without a God's-eye store.
- **Maturity:** Built, zero-dependency Node reference implementation; schema marked HEWN with fail-first pins E1–E8 in `test/edge.pins.mjs` and a 3-node demo where a fork is rejected. (Substrate adapters — D1/KV, EmDash plugin — are only DRAWN.)
- **Tap:** envelope + chain + idempotent-ack + loud-fork rules are the settled answer to "what does a SEND look like on the wire, and what does RECEIVE do on ingest." Note the remaining gap: ack comes from a *store*, not from a second sovereign ledger — quilt-rooms' bilateral agreement is still the missing half.

### 4. gitpp-sheet — TAP · both (fold state + observation ledger) · built, mutation-verified tests

- **Mechanism (actual):** Treats a git.pp fold as a spreadsheet — `state` (cells), `setters` (provenance), `order`, `conflicts` (who lost) — and adds what the fold lacked: **cell-level confidence**. Two settled ideas: (1) **score the claim, not the record** — the claim that decides a cell is a *relation* ("r2 outranks r7 for k0"), the only kind a text judge can answer; (2) confidence **propagates through the fold as the minimum over the decisions that produced the cell** (never mean, never product), a loser's uncertainty does not propagate, and the winner is read from the conflict ledger first (reading only `setters` would report 100% confidence from ignorance). The v0.2 question graph adds a durable **ledger of observations**: each record carries score *and* adjudicated verdict, guards' reason, solo/batched mode, source judge, spread over repeats, and the `stateHash` of exactly what it was read against. **Closure property:** the ledger is sufficient — a verdict reproduces with *no judge call at all*, so unasked questions can be derived and a small model/rule engine can answer from the ledger. **Latest reading wins** (keeping the best reading is called out as laundering a flaky judge); correlation is reported as separate counts of distinct documents vs distinct experts, never averaged into an independence score.
- **Serves:** Log (dated readings with spread, latest-wins, "a score is a sample") + Ledger (the sufficient receipt record) + contract layer (guards adjudicate observations into verdicts).
- **Maturity:** Built and tested — 37 files, 8 test suites; the sheet suite is mutation-verified (mean-instead-of-min, setters-only, loser-propagation mutations each fail exactly one test).
- **Tap:** minimum-link confidence, score-the-claim, closure/sufficiency, and the receipt format (score + verdict + provenance + state-hash, never a bare number). Already routed here by the sorting pass; this lane confirms that routing was right.

### 5. jev-semantic — TAP · log (judgment record) + contract layer (anchors/validators) · v0 built, semantic graph spec-first

- **Mechanism (actual):** An append-only judgment log keyed by **(subject, question, judge)** — all blob hashes — producing `(neg, zero, pos)`. The SPEC's central decision: a judgment is keyed by the full triple **(content-hash, encoder-hash, jev-hash)**; same content under a new encoder is a *different question*, and refusing to collapse the triple is what keeps readings honest about *whose* reading they are. **Two bodies may log the same key; their disagreement is data.** Judgments are never updated in place; anchors (the only objects allowed to cite outside-the-loop outcomes) feed per-(jev, question) calibration tables, and a long microcosm-derived alarm rule set (R1–R17+) governs collapse detection — including that sampler provenance ("what is your kernel width and who set it") is itself a pinned, content-addressed log object. A window compiler projects task + precedents + recent activity into an agent's working window.
- **Serves:** Log — the record of *dated, attributed readings/judgments* with drift made visible by hash comparison rather than metadata flags. Directly feeds the deadband/calibration side of beam-quilt.
- **Maturity:** Mixed: `judge_log.py` is a working v0 (ONNX student, TSV log, format tests exist); the semantic graph SPEC is explicitly "SPEC ONLY" for its layer. The 7 ms / "bit-identical" claims remain **not independently auditable** (per Lane B: publish the artifact, input contract and quantization contract).
- **Tap:** the keying triple, append-only multiplicity, anchors-must-be-external, disagreement-as-data. This is where "every reading is someone's reading" is already enforced mechanically.

### 6. naDir — TAP · ledger (async work provenance) · built, minimal, untested

*(Candidate listed as "nadir"; actual repo name `naDir`.)*

- **Mechanism (actual):** Directory-native async work ledger — no server, no database. Every unit of agent work **opens** a ledger entry (a folder: `intent.md`, `meta.json` with agent/project/`started_at`/`heartbeat_at`/`source`/`source_agent`, `inputs/`, `output.log`), **heartbeats** while in flight, and **closes** by moving to `closed/` with `result.md` and `outputs/`. A reaper moves stale-heartbeat entries to `abandoned/` with a note — every entry gets closed, even dead ones. Because each entry records source and destination, pipelines *emerge* as a provenance graph traceable with the `trace` command; the same ledger supports follow-the-data / follow-the-agent / follow-the-project / follow-the-time projections with `ls` and `grep`. The whole agent contract is three shell moments: open, heartbeat, close.
- **Serves:** Ledger — of *work*, not payloads: open = a contribution claimed, close = a contribution booked with its destination; heartbeat/staleness supplies the liveness half that pure claim records lack.
- **Maturity:** Built but minimal — one shell script + README, no tests; the pattern is the product.
- **Tap:** open/heartbeat/close/reaper lifecycle and source→destination provenance fields for any async contribution bookkeeping. Gap: closing is unilateral (the destination never countersigns) — it is a provenance ledger, not a bilateral agreement ledger.

### 7. elephant — TAP · log (subjective readings) · built research codebase, learned core still a stub

- **Mechanism (actual):** Room-perception research built on the premise Casey's foundation states: **"every reading is someone's reading"** — subjective, shaped by that agent's experience. A room is a *field*, not a stream; a bank of dials (mood, panic, presence…) reads it simultaneously, and both a Room-Elephant (zeitgeist) and a Personal-Elephant (one agent's feel, with attachments) are first-class. Training signal is **contrast between rooms** (`distance`, sauna/plunge gap), never ordering within a stream. Crucially for this foundation, its quilt-bridge doc settles an identity: every reading is a **delta** (field after minus field before), and that before→after pair *is* the directed edge a quilt cell-ledger records — `imbalance ≡ d_mu`, verified to 1e-12 against quilt-rust's field-edge bridge, with the ledger's null-prior identified as the elephant's deadband/noise floor.
- **Serves:** Log — the *predictor/reading* side: how a perspectival reading is produced, kept subjective, and converted into a ledgerable edge.
- **Maturity:** Large built codebase (689 files, many test/report artifacts, demos runnable) but honestly labelled: the dials are hand-crafted heuristics with a JEPA-shaped interface; the learned backbone is a stub.
- **Tap:** the subjective-reading framing and the reading-as-delta identity. Do not tap it as a general active-log implementation — it reads *rooms*, not arbitrary cells, and it keeps no dated tensor of readings.

### 8. erised — TAP (narrow) · both in miniature (local state + tick economy), scars · live product

- **Mechanism (actual):** A model-only cooperative-fiction engine (single-file HTML, state in localStorage) with two ledger-shaped mechanics. **Tick economy:** each character has a tick budget; turns *spend* ticks, resonance (earned via citations/positive net shift) *earns* them, and the conservation invariant is enforced without negotiation — an out-of-ticks character goes inert. **Scars:** when a run breaks down (error, budget exhaustion), the scar is recorded; **rewind drops the round but the scar persists** — the next iteration must address it; you can't patch over it. Its QUILT.md maps these onto the cell fabric explicitly (scar = `BIND` of an entry to tick + kind + note).
- **Serves:** Ledger in miniature (spend/earn accounting of a conserved budget) + Log (the scar record as the part of history that survives state rewind).
- **Maturity:** Live product with a corpus of 24+ runs; companion CLI and cell form exist in `erised-cli`. No automated tests in this repo.
- **Tap:** the scar rule (failure records are append-only across rewinds) and tick spend/earn semantics — already the sorting pass's home for those ideas; nothing else here serves the foundation.

### 9. quilt-cells — TAP (contract only) · contract layer for log cells · spec seed

- **Mechanism (actual):** A two-file specification: a cell is a unit of visible logic, `INPUT → OUTPUT + WHY + COST` — the output, a human-readable reason (a score without a reason is "a black box with a number"), and visible cost (ms/MB/$). Prism cells fan one output to many cells or collect many into one decision. Explicitly "seeds, not constraints": if something doesn't fit, the contract bends.
- **Serves:** Contract layer — this is the unit whose readings an active log holds; WHY+COST are exactly the fields a reading needs to stay interpretable and priced when it later crosses into a ledger.
- **Maturity:** Scaffold/spec only (2 files, no implementation, no tests).
- **Tap:** cite the contract as the cell record shape (already the sorting pass's routing). Nothing to import.

### 10. Patchwork-experts — ADAPT · contract layer (payload format) · format + catalogue, no runtime

*(Canonical name `Patchwork-experts`.)*

- **Mechanism (actual):** A patch is a frozen, versioned, tradable expert: `patch.md` (identity / what I know / how I work / stances / limits) + `meta.yaml` (capabilities, limitations, **lineage**, changelog). Provenance is non-negotiable — every patch records what went into it and what changed between versions, no silent edits; payload (scripts/schemas/runbooks) is optional, readable text only, and must be explained by the patch. Two in-repo suggestion docs go further: patches as routable *cells* needing a **gate** (probes → load/refuse), a **budget** tier, and a **witness** (version claims re-checked over time); and a verification layer moving trust from provenance to measurement (published probe results, including the misses).
- **Serves:** Contract layer — the *payload* that crosses a boundary when expertise moves: frozen state + lineage + versioned changes. It is what a ledger entry would carry, not a ledger.
- **Maturity:** Built as a format/catalogue (37 files, seed patches, compiler docs); the gate/budget/witness machinery is **proposed, not implemented**.
- **Adapt:** the freeze/lineage/changelog discipline for any payload format; treat probes/cost/witness as a design brief, not settled mechanism.

### 11. cocapn — ADAPT (sxc1 submechanism is TAP-grade) · contract layer + local tile log · built, tested

- **Mechanism (actual):** A repo-first agent framework: knowledge as **tiles** (Q&A units with confidence, usage/success counts, version) in append-only JSONL, rooms that rank tiles, a flywheel that records every exchange back as a tile. The foundation-relevant part is the **sxc1 cell-exchange dialect**, where cocapn is the fleet's *validator mirror*: envelopes `{v, seq, cell, body, seal}` with `id = sha256("sxc1:"+seq+":"+prev+":"+canonicalJSON)`, genesis prev, and **fail-closed named error codes in fixed order** (field → seq → prev → hash → spec). A generator's envelope is treated as a claim, never evidence; the validator re-derives every hash, answers only `COMPILED | INDETERMINATE`, seals its own verdict envelopes, and **never mutates another repo's state — it answers.** Cross-repo spec seals are checked for presence + hex shape, never equality — foreign contracts are respected as foreign.
- **Serves:** Contract layer — the most worked-out "contract as algorithm" on the shelf: a versioned, sealed, machine-checkable agreement about what a crossing payload must satisfy, with refusal as a first-class outcome. Tiles are a lightweight local log of exchanges.
- **Maturity:** Built (167 files, pytest suite incl. `tests/test_sxc1.py`, spec pre-registered and seal-gated in CI).
- **Adapt:** adopt sxc1's fail-closed verdict pattern and seal discipline for seam handshakes; do not adopt the whole agent framework — that is exactly the bloat Casey ruled out.

### 12. constraint-theory-core — ADAPT · contract layer (agreement substrate) · built, published, heavily tested

- **Mechanism (actual):** Deterministic manifold snapping: continuous 2D direction vectors are snapped to exact Pythagorean-rational lattice points (identity = integer triple, not a float computation), so **two different readings from two different machines collapse to the same exact point** — bit-identical agreement by construction. Plus holonomy verification (carry a direction around a loop of constraints; if it doesn't return to itself the network is inconsistent), Laman rigidity checks, deadband funnels, and quantization modes. Zero dependencies, published on crates.io (v2.2.0), README reports 262 tests passing.
- **Serves:** Contract layer — not a log or ledger, but the *canonicalization* that lets separate perspectives agree on what a payload *is* before they agree to book it; holonomy is a consistency check over a web of agreements.
- **Maturity:** The most production-hardened repo in this survey.
- **Adapt:** cite exact-snap as the settled answer where a seam needs cross-machine identity of values, and holonomy as the shape of a consistency audit. (Keep Lane B's scope discipline: Laman here is a 2D bar-joint check, not a definition of truth.)

### 13. gitpp-oracle — ADAPT · ledger (witness record) · built, running

- **Mechanism (actual):** A continuous cross-port conformance oracle whose core is an **append-only JSONL ledger of runs** — each row carries timestamp, every port's git SHA, suite results, and new vs accepted divergences. A `known-limitations.json` **contract** lists characterised, accepted divergences: exit 0 while only those persist, non-zero the moment anything else appears. **Fail-closed** throughout: an unparseable result is FAIL, "absent signal is not consent," an absent reference is reported as absent, not agreeing. The ledger itself is governed: scheduled runs must *grow* it (asserted, not assumed), it is committed and rolled up daily, and failing runs are recorded the same way — "a witness log that only remembers the green ones is not a witness."
- **Serves:** Ledger — as a *witness* record over time, plus the contract pattern for pre-registered exceptions.
- **Maturity:** Built and operating (scheduled runs, committed ledger history, verdict tests).
- **Adapt:** the ledger-governance rules (growth assertion, per-participant version pinning, known-limitation contract) for any long-lived ledger; the domain (port conformance) doesn't transfer.

### 14. synoptic-sheet — ADAPT (renderer only) · log projection · built, thin

- **Mechanism (actual):** A single-page renderer over a `state.json`: rows are agent entries in write order — tasks propose and do, witnesses watch and sign, an override settles a contested key — and the displayed state is **a fold of everything that was said, including the claims that lost**, with conflicts shown (winner + losers) and an honest "state as of" timestamp. Nothing is ever deleted.
- **Serves:** Log *projection* — it demonstrates the reading side of the anatomy (current state = fold of dated entries, losers retained, as-of stamping) but contains no writer, ledger or fold engine; the mechanism lives upstream in the git.pp fold.
- **Maturity:** Built and live, but deliberately thin (4 files).
- **Adapt:** the presentation contract (fold-state + conflicts + as-of) for any active-log viewer. Do not cite it as a ledger implementation.

### 15. plato-nervous — ADAPT (weak) · log intake · built, test count claimed

- **Mechanism (actual):** The PLATO signal chain: `Sensor → Deadband → Nano → LoRA → Fleet → Cloud`. Timestamped sensor readings enter at Layer 0 (a scalar deadband filter against last value); each layer resolves what it can and escalates the rest; every resolution produces a **tile** recording what was found, its confidence, and *who resolved it* — tiles accumulate as training data for room-specific adapters.
- **Serves:** Log intake only — a reading pipeline that turns raw dated readings into attributed, confidence-bearing records. Its deadband is the older amplitude-only form (no persistence/yaw-time parameter) and is superseded by Casey's 2026-10-21 autopilot doctrine; there is no ledger and no bilateral anything.
- **Maturity:** Built Rust crate; README claims 45 tests (not re-run here).
- **Adapt:** the tile shape (reading + confidence + resolver) as an intake record idea. Otherwise leave it to the PLATO line.

### 16. ledger-continuity — ADAPT · shared claim ledger (recovery) · built, recovery-tested

*(Caught by search; not on the candidate list.)*

- **Mechanism (actual):** The minimal ledger pattern for agent crash recovery: three claim types only — a self-contained **task claim** (full work definition in the ledger, not in the dead agent's memory), **per-unit result claims** with mandatory evidence, and **heartbeats** (recovery requires liveness *in* the ledger: without them a successor can't distinguish dead from slow). Plus re-check-before-claiming, giving exactly-once *recording* under at-least-once *execution*. Proven on Oracle across clean crash, mid-subtask crash, and chained A→B→C takeover.
- **Serves:** Neither item directly — it is a *shared* claim ledger (closer to a blackboard than to sovereign logs), but it settles the recovery/liveness semantics any active log needs when its writer dies.
- **Maturity:** Built, minimal (4 files), test script demonstrates all three crash variants passing.
- **Adapt:** heartbeat-in-the-record + self-contained claims + re-check-before-claim. Its honest limitations (timeout-based death detection, no fencing/leases) are stated in its README and should be inherited with the pattern.

### 17. lau-conservation-engine — ADAPT (narrow, with caution) · ledger (double-entry transfers) · built library

*(Caught by search; not on the candidate list.)*

- **Mechanism (actual):** A Rust double-entry energy ledger for agents: per-agent accounts, `spend`/`earn`/`transfer`, immutable history entries, and paired action types (`Transfer { to }` / `Receive { from }`) — the closest literal implementation of *deduction/contribution pairs* on the shelf. A parametric conservation law (`γ + H = C − α·ln(V)`) is continuously verifiable against the ledger.
- **Serves:** Ledger — for *conserved quantities only*.
- **Caution:** Its centre of gravity is account **balances**, which Casey's restatement explicitly says the active ledger is *not* ("not a ledger of account values"). The paired transfer/receive entry types and the invariant-checking pattern are tappable; the account model is the thing to leave behind.
- **Maturity:** Built library (serde-only deps, full API documented); test evidence not surfaced in the tree survey.

## Name collisions and non-repos

- **`quilt-study` — DOES NOT EXIST** on GitHub (SuperInstance or patchwork-quilt). Flagged per instructions; the sorting pass's references to it are to local study material, not a shelf repo.
- **ActiveLog-MVP / ActiveLog-TechnicalRepo** — exist, but are generic activity-tracking apps (descriptions only; no README served). **LEAVE** — name collision with "active log," no perspectival-readings mechanism in evidence.
- **AI-Writings / SuperInstance-papers hits** — the exact foundation phrases live there as prose and canon. They are the *record* of the ideas, not mechanisms; **LEAVE** for builder purposes.
- The wider `*-ledger` family (moth-ledger, simulation-ledger, doubt-ledger, trust-ledger, decision-ledger, ghost-ledger, etc.) is mostly receipt/witness variants of the patterns already covered by gitpp-oracle, quilt-chrono and edge-ledger; none surfaced a distinct log/ledger mechanism in search metadata. Not individually surveyed — flagged as a known unswept corner, not a gap in the candidate list.

## What to tap, in one list (anti-bloat form: cite, don't import)

| Foundation need | Settled at | What to cite |
|---|---|---|
| Log/ledger split, sovereignty, scoped time | **quilt-rooms** | seq-as-local-time, both-sides-record, pending→agreed, scoped universe |
| Cell readings/writings record + replay | **quilt-chrono** | entry v1 shape, compensating corrections, `stateAt` projections, balanced flows |
| SEND on the wire / RECEIVE on ingest | **edge-ledger** | per-node hash chain, idempotent ack, loud fork rejection, declared-unsigned |
| Confidence in a cell + sufficient receipts | **gitpp-sheet** | minimum-link rule, score-the-claim, closure property, latest-reading-wins |
| Attributed judgments, disagreement as data | **jev-semantic** | (content, encoder, judge) keying triple, append-only multiplicity, external anchors |
| Async contribution lifecycle | **naDir** | open / heartbeat / close / reaper, source→destination provenance |
| Subjective reading → ledgerable delta | **elephant** | personal vs room reading, reading-as-delta identity (`imbalance ≡ d_mu`) |
| Failure records that survive rewind | **erised** | scar rule; tick spend/earn conservation |
| Cell record shape | **quilt-cells** | INPUT → OUTPUT + WHY + COST |
| Payload that crosses a boundary | **Patchwork-experts** | frozen patch + lineage + changelog discipline |
| Contract-as-algorithm at a seam | **cocapn (sxc1)** | sealed envelope, fail-closed named refusals, verdict-only replies |
| Cross-perspective value identity | **constraint-theory-core** | exact snap to integer-defined points; holonomy consistency |
| Ledger governance over time | **gitpp-oracle** | growth assertion, known-limitation contract, fail-closed witness |
| Recovery/liveness of a writer | **ledger-continuity** | heartbeat-in-record, self-contained claims, re-check-before-claim |
| Conserved-quantity transfers | **lau-conservation-engine** | paired Transfer/Receive entries, invariant verification (not the account model) |

## The unbuilt remainder (what beam-quilt must still define itself)

1. **The active log as a tensor of dated readings.** quilt-chrono records readings/writings of cells and gitpp-sheet ages readings honestly (latest-wins, spread), elephant keeps readings subjective — but no repo holds *per-observer, per-cell, dated readings with staleness and a predictive model* as one structure. This is beam-quilt's own P4/P5 territory, now with parts to cite rather than a blank page.
2. **Bilateral agreement for payload transfers.** quilt-rooms agrees on *time*; edge-ledger's ack is a store's, naDir's close is unilateral. Two sovereign ledgers each booking SEND/RECEIVE and minting shared state *by their agreement* — the heart of Casey's restatement — is not implemented anywhere surveyed.
3. **Contract binding.** sxc1 seals an envelope dialect, Patchwork freezes a payload, gitpp-sheet's guards adjudicate observations — but no repo binds *the algorithm (id/hash/version) that governed a crossing* into the ledger entry itself. "A contract is an algorithm" remains a definition to build, with sxc1 as its nearest ancestor.
