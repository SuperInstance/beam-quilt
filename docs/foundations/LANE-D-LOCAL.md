> **Provenance:** Lane D (local chronology + mechanism sweep) for the two-active-items foundation, 2026-10-21.
> **Sources:** `~/MEMORY.md`, `~/memory/*.md`, side-chat notes under `~/side-chats/`, and workspace project docs (cordis-lab, quilt-rooms, nadir-docs, ledger-continuity, git.pp, quilt-cells, xero-seed, quilt-study, beam-quilt, ai-writings-src); quotes carry the voice key defined at the head of the dossier.
> **Standing:** merged into beam-quilt as part of the foundations layer (see FOUNDATIONS.md, "Shelf foundations — the two active items"); the chronology and already-specified mechanisms here are the record the architecture docs must agree with.

# Lane D — Local/memory sweep: prior formulations of the two-active-items foundation

Research lane D, 2026-10-21. Sources swept: `~/MEMORY.md`, `~/memory/*.md`,
side-chat notes under `~/side-chats/`, and workspace project docs
(`cordis-lab`, `quilt-rooms`, `nadir-docs`, `ledger-continuity`, `git.pp`, `quilt-cells`,
`xero-seed`, `quilt-study`, `beam-quilt`, `ai-writings-src`).

**Voice key — read this first.** Every quote below is tagged:

- **[Casey verbatim]** — Casey's own words, from a `quote:` field in the
  memory record or from his message text. Transcription garbles are his
  dictation's, preserved.
- **[Casey, as recorded]** — a memory/design entry attributed to Casey but
  written in the recorder's words (a summary, not a transcript).
- **[Muse gloss]** — Muse's crisp restatement of Casey's material.
- **[Project doc]** — text from a built project's documentation (mixed
  authorship, Casey-directed).

Nothing in the source files was edited by this lane.

---

## 1. Chronology

### Pre-SuperInstance (date unknown; reported 2026-10-12) — the name predates the programme

Casey stated that before SuperInstance had its name he roughed out a
Muse-like side-agent system "called it 'active log,' intended as white
label; big companies own the first mile (rendering) — his ground is
everything after it."

- [Casey, as recorded] `~/memory/2026-10-12.md:1319`; also `~/MEMORY.md:116`.

This is the earliest *named* use in the corpus. The earliest *dated
formulation* is Oct 5 below. (The names mattered commercially the same
week: `activelog.ai` and `activeledger.ai` sat on Casey's domain sale
list with tentative asks of $2,600 / $2,900 — above appraisal "because
the names fit the actual architecture" — `~/memory/2026-10-05.md`,
domain-portfolio section.)

### 2026-10-05 (~09:35–09:50) — the two spreadsheets: data at rest vs data in motion

The first dated formulation is spreadsheet-shaped, and it already
contains both halves of the later anatomy:

- **Active log** = "data sitting in cells (state at rest)".
- **Active ledger** = "how data changed while moving to the next cell
  (transitions, algorithms, agreements, and custody)".
- The two "should be viewable separately—just data or just algorithms—or
  synoptically together."

> [Casey verbatim] "one of them being what I like to call the active log,
> and the other one is the active ledger… one of them is the data sitting
> there and the other one is how the data changed when it went to the
> next cell." — `~/memory/2026-10-05.md:675–676`.

Captured the same day as a design doc [Project doc]
`~/workspace/cordis-lab/TWO_SPREADSHEETS.md`, whose rule deserves quoting
because Oct 21 returns to it almost word for word:

- "Data at rest goes in the log. Data in motion goes in the ledger. The
  movement is always by agreement (push and pull consented), always
  double-entered (deducted here, added there), and the algorithm is part
  of the entry" (`:34–39`).
- "The piping is a cell": the pipeline/algorithm is a first-class ledger
  cell, not plumbing (`:27–31`).
- Three views: just the log, just the ledger, synoptically both (`:20–25`).

Same session: **provenance vs providence** — provenance is the linear
double-entry chain of custody; providence is the larger synoptic graph
(gauges over time) that makes a break intelligible; "the diagnostic power
comes from seeing both dimensions together" (`~/memory/2026-10-05.md:678`).

### 2026-10-07 — the theater formulation

A one-line variant, worth recording because it assigns the two records
to *roles in the same event*: "the black-clad sound, lighting, and stage
crew on headsets are the active ledger, coordinating cues and
current/next state, while the performers are the active log—the event
being enacted. Both operate over the same performance and time span,
with different attention."

- [Casey, as recorded] `~/memory/2026-10-07.md:77`.

### 2026-10-08 — the quilt room protocol: log inside, ledger between

The first dated two-record formulation is at room scale, not agent scale:
each quilt room runs its own universe with an **active log** of intra-room
activity; between rooms there is an **active ledger** of inter-room
exchanges. Cross-room time is itself an agreement, not a clock:

> [Casey verbatim] "where they met is an agreement that that was a single
> time in the universe" — `~/memory/2026-10-08.md:281–284` (quote field).

Built the same day and tested end-to-end (public repo
`SuperInstance/quilt-rooms`): "Room A sends at seq 2, Room B receives at
seq 2, both ledgers agree room-a:2 = room-b:2 = U:101; on conflict the
protocol negotiates — higher wins, game master breaks ties."

- [Event, Muse-recorded] `~/memory/2026-10-08.md:315`, `:340–341`.

### 2026-10-08 (17:32) — commit as resolution; ledgers nested in logs; observer levels

The sharpest mechanism statement in the early corpus, from the
truth-architecture session:

- A **log** is "the uncertain working state inside one agent's cell"; a
  **ledger** is "the shared exchange between agents, and saving/committing
  resolves an uncertain candidate state into a shared agreement."
- "**Active ledgers can sit inside active logs**, while each observer
  operates at the abstraction relevant to its seat rather than inspecting
  ones and zeros."
- Companion primitive from the same session: "**'Facts' is the wrong
  primitive** for the operational ledger; **agreements/signatures are the
  primitive**" — a signed claim assigns responsibility upstream and lets
  the next party act downstream.

- [Casey, as recorded] `~/memory/2026-10-08.md:767`, `:769`.

Also 2026-10-08: the ledger side may optionally carry blockchain
machinery — a dial, not a default:

> [Casey verbatim] "on the active ledger side this could use blockchain
> technology in certain applications not every application because not
> everything needs that overhead" — `~/memory/2026-10-08.md:351`.

And: blockchain is "a sequence, not a clock" — relative ordering beats
absolute timestamps (`~/memory/2026-10-08.md`, same session). Muse wrote
this distinction up the same day as a thesis stub, `ledger-vs-log`, in
`SuperInstance/muse-workspace/ideas/` (remote; no local copy found in
this sweep) — `~/memory/2026-10-08.md:773`.

### 2026-10-09 — the terms in working use

A passing but telling use: model iterations on the logic layer "remain
rendered beside prior versions in the spreadsheet/active ledger, so
changes and live behavior can be watched rather than disappearing into
model weights or hidden files" — the ledger as the visible surface where
versions accumulate (`~/memory/2026-10-09.md:926`).

### 2026-10-10 (~12:12–12:14) — the cellular agent: "an agent is really these two logs"

The canonical Oct 10 statement, from the Z-zero ramble (full notes:
`~/side-chats/a00fcafa-6457-49cc-b389-08c762f93760/notes/2026-10-10-z-zero-agentic-terminal.md:371–384`):

> [Casey verbatim] "An agent is really these two logs."

1. **Active log (state):** "all the data points, the values that make this
   agent unique vs. another" — configuration files, conversations,
   memories, agent files. "All that stuff is its state at the time."
2. **Active ledger (contracts):** "what algorithms did you agree to for
   the sending and receiving of data in and out of your system? What are
   the contracts?"

> [Casey verbatim] "In human terms, a contract is an algorithm."

Muse's crisp gloss, same notes (`:386–392`), [Muse gloss]:

- "Active log = what the agent IS (state-at-a-time). Identity is the
  diff: same files + different state = different agent."
- "Active ledger = what the agent OWES (contracts)… who may send what,
  who must do what on receipt, how both sides verify."

Corroborating distillations: `~/memory/2026-10-10.md:1107`, `:1267`,
`:1551`; `~/memory/2026-10-12.md:950`.

Adjacent mechanics from the same ramble:

- **Boarding / nesting:** interacting with an agent is "stepping onto
  him"; threads interleave across agents; "**tool calls write to the
  active ledgers** — the ledgers previously imagined 'inside a
  spreadsheet' now have a home: they live in the cell" (side-chat notes
  `:307–365`).
- **Turing mapping:** the minimum agent is a perception check in a loop
  with I/O — tape = files/active log, **δ (the transition function) = the
  ledger of gates/contracts**, the never-halting loop = the churn
  (`~/memory/2026-10-12.md:1043`). The xero-seed notes tighten it:
  "δ IS the ledger — the agreed algorithm, the contract, in the machine's
  own terms… the judgment log is not a record of the agent, it's the
  agent's δ" [Project doc] `~/workspace/xero-seed/NOTES-2026-10-10-zero-uno-q-architecture.md:59–61`, `:89`.
- **Gates and contracts:** harnesses are points of demarcation, not the
  contracts themselves; "Gates tell you where the contracts are"; log the
  gate crossing, not the raw signal leading to it; each side may keep
  private complexity behind a tiny contract (bilge-pump parable)
  (`~/memory/2026-10-12.md:951`; side-chat notes `:433–451`).
- **Contract schema growth:** {trigger, algorithm, parties, verification}
  → plus deadband (how-far + how-long trip characteristics) → plus watcher
  / no-signal cadence; "the posted watch produces notes (ledger entries)"
  (side-chat notes `:557–559`, `:625`, `:662`).

### 2026-10-12 — restatements, and the hinge: record primary, state derived

The Oct 10 ramble was distilled into `~/memory/2026-10-12.md:950`
(two-log theory) and `~/MEMORY.md:116` (the pre-SuperInstance "active log"
history). The same day's SuperInstance survey recorded an orientation
finding that becomes the hinge toward Oct 21:

- "**The record is primary, state is derived** — git.pp-ng `fold(records)`,
  determinism measured over 50 shuffled arrival orders; zero-poc;
  agent-inbox." Also from the surveyed repos: zero-innate's minimum five
  things include an **append-only log**; zero-poc: "repo *is* the agent,
  git log is memory."

- [Muse survey summary of Casey's repos] `~/memory/2026-10-12.md:1321`.

The chat-to-git filing-loop prototype built the same day runs on the same
split: deposits accumulate, and "git log = ledger" (42 commits for the demo
run) — `~/memory/2026-10-12.md`, "Filing loop verified end-to-end" entry;
code at `~/workspace/xero-filing-prototype/`.

### 2026-10-21 (before the restatement) — the stance from the water

Casey's own fishing-fleet prompt (his text, highest provenance in the
Gemini set) states the perspectival stance operationally, before it is
stated formally:

> [Casey verbatim] "We aren't trying to finded the god's-eye-view… we
> aren't solving for truth,, we are solving for operational optimization"
> — `~/workspace/beam-quilt/docs/proposed/gemini18.md:5`.

Each boat's "ground truth is not [another's]"; information from others is
useful without being ground truth; the fleet coordinates through
compressed signals (texts, observed motion), never through a shared view.

### 2026-10-21 16:08 — the foundation restated: two active ITEMS

Casey's telling in this chat (curated record: `~/MEMORY.md:6`):

> [Casey verbatim] "what you need to represent a system is you need two
> active items… an item gives me a generic term"

> [Casey verbatim] "There's no God's eye view period"

> [Casey verbatim] "this is not a ledger containing the values that are
> in each account this is a ledger of the deductions and the
> contributions"

> [Casey verbatim] "the agreement between them that it was sent and
> received is what creates the shared fiction that is operational"

The restated definitions [Casey, as recorded in `~/MEMORY.md:6`]:

1. **Active log** — "your current readings of every cell FROM YOUR
   PERSPECTIVE (a tensor state of readings)". Every entity's first-class
   knowledge of itself is truth *for it*; perspectives learn from each
   other only through compressed payloads (the blind-men-and-elephant
   point: a quorum of leg-grabbers still doesn't have the sighted view).
   Readings are **dated** — fog of war: the record ages, the fog returns,
   and each side operates on a predictive model of the other. Devices,
   storage and memory blocks "are not different on any fundamental level;
   we abstract them different."
2. **Active ledger** — not account values but **deductions and
   contributions**: a payload out of one item and into another, recorded
   by one as a SEND and by the other as a RECEIVE; **the agreement
   between them is what creates the shared fiction that is operational.**
   The earlier contracts formulation is the same anatomy — the ledger
   holds "the contracts/algorithms governing boundary crossings," and
   committing resolves uncertain log-state into shared agreement.

Consequence recorded with it: there is no universal stored state to rule
on — state lives in each item's active log (perspectival, dated); shared
state is never stored anywhere, it exists only as agreed ledger entries.
This superseded Muse's "fix + track" candidate C for the
continuum-runtime state question, the same way the autopilot mechanism
superseded the deadband A/B framing.

---

## 2. How the definitions shifted

| | Oct 5–12 formulation | Oct 21 restatement |
|---|---|---|
| **Unit** | A spreadsheet (Oct 5), a room (Oct 8), or a cellular agent (Oct 10) | Any **item** — deliberately generic: not necessarily an object; a device, a storage block, a person |
| **Active log** | **State**: data sitting in cells (Oct 5); the values/files that distinguish this instance (config, conversations, memories, files) — "its state at the time" (Oct 10) | **Readings**: your current readings of *every* cell, from *your* perspective — a tensor of dated readings that ages (fog of war) and is extrapolated by a predictive model |
| **Active ledger** | **Movements + contracts**: how data changed moving cell→cell — transitions, algorithms, agreements, custody, double-entered (Oct 5) — with the emphasis narrowing to **contracts**, the agreed algorithms governing crossings (Oct 8–12) | **Movements**: deductions and contributions — each payload recorded as SEND by one side and RECEIVE by the other; the *agreement* creates the shared operational fiction |
| **Truth** | Agreements/signatures are the primitive (Oct 8) | No God's-eye view, period; each item's self-knowledge is truth *for it*; shared truth is only ever the agreed ledger |
| **Time** | Negotiated: two rooms' sequences meet at an agreed universe time (Oct 8) | Readings are dated and decay; the other side is always partly predicted, never seen |

**What actually changed (lane-D reading, not a Casey quote):**

1. **Scope widened.** Agent/room → item. The anatomy is claimed for any
   system representation, spreadsheet-representable at the simple end.
2. **The log went perspectival and temporal.** Oct 10's log is what the
   agent *has*; Oct 21's log is what the item *currently reads* — of
   itself and of every other cell — with timestamps and fog. The Oct 12
   survey line ("the record is primary, state is derived," fold over
   shuffled arrival orders) is the visible hinge between the two.
3. **The ledger's emphasis cycled: movements → rules → movements.**
   Oct 5's ledger already held movements (double-entered: "deducted
   here, added there" — Oct 21's "deductions and contributions" is this
   rule returning, not a coinage). Oct 8–12 narrowed the emphasis to the
   ex ante half: contracts, the algorithms agreed before crossings.
   Oct 21 restores the ex post half: the agreed record of crossings
   themselves (SEND/RECEIVE). The two compose rather than conflict — a
   contract is itself an early ledger entry (an agreement about future
   sends/receives), and Oct 8's commit mechanism is the bridge: committing
   resolves uncertain log-state into ledger agreement.
4. **Nothing was retracted.** MEMORY.md's Oct 21 entry explicitly keeps
   the contracts formulation as "the same anatomy." The shift is emphasis
   and generality, not replacement.

---

## 3. Mechanisms already specified (do not re-invent)

1. **Commit = resolution.** Saving/committing resolves an uncertain
   candidate state in the log into a shared agreement in the ledger
   (`~/memory/2026-10-08.md:767`).
2. **Nesting.** Active ledgers can sit inside active logs; tool calls
   write to the ledgers that live inside the cell
   (`~/memory/2026-10-08.md:767`; side-chat notes `:320`).
3. **Observer abstraction levels.** Each observer operates at the
   abstraction relevant to its seat rather than inspecting ones and zeros
   (`~/memory/2026-10-08.md:767`); cf. the boat-instrumentation stack —
   raw resistance calibrated into positions, dashboards showing gated
   readings (same entry, following lines).
4. **Negotiated time.** Cross-room events carry both rooms' local
   sequences plus an agreed universe time; disagreement stays in a
   `pending/` zone and does not block new messages; wall clock is
   advisory only [Project doc] `~/workspace/quilt-rooms/TEMPORAL-AGREEMENT.md:5`, `:63`, `:72`, `:99`.
5. **Both-sides record, each sovereign.** "A link isn't in the ledger
   until both rooms have it. Each room keeps its own copy of the ledger —
   they should match, but each is sovereign" [Project doc]
   `~/workspace/quilt-rooms/ACTIVE-LEDGER.md:44`. Log rules: append-only,
   one writer, sequence-is-time, hash-chained
   (`~/workspace/quilt-rooms/ACTIVE-LOG.md:38–45`).
6. **δ = ledger.** The ledger is the agent's transition function, not
   side bookkeeping (`~/workspace/xero-seed/NOTES-2026-10-10-zero-uno-q-architecture.md:61`, `:89`).
7. **Record primary, state derived.** State as a fold over records,
   invariant under arrival order (50-shuffle determinism measured in
   git.pp-ng) (`~/memory/2026-10-12.md:1321`).
8. **Ledger mechanics (gitpp-sheet, adopted by reference).**
   Minimum-link confidence (a conclusion's certainty = the minimum
   tension over the weakest link), score-the-claim-not-the-record, and
   the closure property (a ledger is sufficient when a verdict reproduces
   from it with no judge call). Sources are Gemini-elaborated, homed to
   `gitpp-sheet` by the 2026-10-21 sorting pass — **not Casey verbatim**:
   `~/workspace/beam-quilt/docs/proposed/gemini13.md:1`, `:506–515`,
   `:662–664`; routing in `~/workspace/beam-quilt/PROGRAM-MAP.md:70`.
   No local `gitpp-sheet` clone was found in this sweep.
9. **Optional chain hardening.** Blockchain on the ledger side as an
   application dial (traceability to inception, anonymous-key zero
   trust), sequence-not-clock (`~/memory/2026-10-08.md:351` and same-day
   entries).
10. **Movement by agreement; three views (Oct 5).** A movement happens
    only when push and pull both consent to their part; the entry is
    double (deducted here, added there) and carries its algorithm. The
    pair of records is viewable three ways — log only, ledger only, or
    synoptically — and the piping between cells is itself a first-class
    ledger cell (`~/workspace/cordis-lab/TWO_SPREADSHEETS.md:20–39`).

---

## 4. Local projects that implement pieces of it

| Directory | Piece implemented | Notes |
|---|---|---|
| `~/workspace/cordis-lab/TWO_SPREADSHEETS.md` | **The Oct 5 design capture** | Two-spreadsheet decomposition with the movement rule (agreement, double-entry, algorithm-in-entry) and the three views; maps Petri ledger → active ledger, repo/frozen state → active log. A document, not code — but the earliest concrete spec of the pair. |
| `~/workspace/quilt-rooms/` | **The two-record anatomy itself** | `ACTIVE-LOG.md` (intra-room, hash-chained, seq-is-time), `ACTIVE-LEDGER.md` (agreed links, dual local times + universe time), `TEMPORAL-AGREEMENT.md`, `ROOMS.md`, working shell template in `ROOM-TEMPLATE/` (`say.sh`, `send.sh`, `receive.sh`, `agree.sh`). Public as `SuperInstance/quilt-rooms`, tested end-to-end 2026-10-08. |
| `~/workspace/nadir-docs/` (+ `~/workspace/goals/nadir-async-ledger-repo-launch/GOAL.md`) | Directory-native double-entry **provenance ledger** for async agent work | Entries open → heartbeat → close; `ledger/open|closed|abandoned/`; reaper for stale entries; provenance edges `from_cell → to_cell`; "Commit the ledger (not the logs) and you have time-travel" (`README.md:116`). Adjacent rather than identical: its ledger tracks work units, not item readings. |
| `~/workspace/ledger-continuity/` | Claim ledger for agent recovery | Three claim types only — task / subresult / heartbeat — plus re-check-before-claiming (exactly-once recording under at-least-once execution); proven 2026-10-05, three crash variants (`README.md`, `ledger.py`, `worker.py`, `test_recovery.py`). Shows liveness must itself be a ledger entry. |
| `~/workspace/git.pp/` | Effect ledgers + claim-gated ledger writes | A task's `.fx/` effect ledger (`KEY.intent`, `KEY.result`) travels with the task (`NOTES.md:36`); the pre-receive hook rejects ledger writes from anyone not holding the task's live claim (`pre-receive:60–66`). |
| `~/workspace/seeds/quilt-cells/` | The item/cell side of the anatomy | Cell contract `INPUT → OUTPUT + WHY + COST`, costs printed on the cell (`README.md:11–16`; `QUILT.md:45–53`). The "cell" whose readings the Oct 21 log holds. |
| `~/workspace/xero-filing-prototype/` | Log/ledger via git itself | Deposit → cascade split → refine; git log as the ledger of deposits (`~/memory/2026-10-12.md`, filing-loop entry). |
| `~/workspace/synoptic-sheet/` (+ `synoptic-sheet-refresh/`) | **The synoptic view, shipped** | One sheet written row by row by agents — tasks propose/do, witnesses watch/sign, an override settles a contested key; "Nothing is ever deleted. The state at the bottom of the page is a fold of everything that was said — including the claims that lost" (`README.md:5–9`). The rendered `state.json` is refreshed from the working record and carries an honest "state as of" timestamp — record-primary/state-derived with dated readings, as a public page. Distinct from `gitpp-sheet` (the question-graph ledger repo), which remains remote-only here. |
| `~/workspace/beam-quilt/` (current programme form) | SPEC P4/P5 as written pre-restatement | P4 "sovereign state… MUST NOT read or write another patch's state"; P5 "Own log… of its transmissions and decisions" (`SPEC.md:20–25`); ontology in `docs/01`. **Flag:** the Oct 21 restatement reframes P4's "state" as perspectival readings — the SPEC has not yet absorbed that wording. |
| `~/workspace/quilt-study/` | Adjacent studies only | Jev/deadband mechanism studies (jev-net, jeviter, deadband-protocol, neyman-pearson-gap); no two-log formulation found there in this sweep. |
| `~/workspace/ai-writings-src/` | Historical echo, not doctrine | "The active log's first MVP — the proof of concept — is rust" (`11-the-salvage-manifest.md:38`); the corpus index also lists an `activelog-ai-pages` repo. Fleet/story voice — do not cite as Casey's formulation. Note the folder's `AGENTS.md` witness-log doctrine ("if it wasn't witnessed, it didn't happen") is that project's house rule, not Casey's two-items foundation. |

Not found locally: `gitpp-sheet` and `elephant` (both exist as public
repos and are routed to in `~/workspace/beam-quilt/PROGRAM-MAP.md:70`,
`:77`); the `muse-workspace` thesis stubs (`ledger-vs-log`,
`agreements-all-the-way-down`) are remote-only from this machine.

---

## 5. Bottom line for the foundation work

- The two-active-items idea is **not new on 2026-10-21** — it has a
  pre-SuperInstance name ("active log"), a dated spreadsheet-scale
  formulation with the double-entry movement rule (2026-10-05,
  cordis-lab), a room-scale formulation with a working implementation
  (2026-10-08, quilt-rooms), and a canonical agent-scale statement
  (2026-10-10, "an agent is really these two logs").
- The Oct 21 telling **generalizes and re-weights** it: items not agents;
  readings (perspectival, dated, fogged) not stored state; movements
  (deductions/contributions, SEND/RECEIVE agreement) not only contracts.
- The mechanisms a new foundation would need — commit-as-resolution,
  nesting, negotiated time, both-sides-record, state-as-fold — are
  **already specified** in the Oct 8–12 material and partly implemented.
  Tap them; don't re-derive them.
