> **Provenance:** Lane E (GitHub API/commit-date lane) for the SuperInstance active log / active ledger lineage, 2026-10-21.
> **Sources:** GitHub API `created_at` fields and git commit author dates, fetched via the Oracle; inferences are labelled in the dossier.
> **Standing:** merged into beam-quilt as part of the foundations layer (see FOUNDATIONS.md, "Shelf foundations — the two active items"); these are the hard public/local anchor dates for the lineage.

# Lane E — Hard dates for the active log / active ledger lineage

All dates below are sourced to a GitHub API field (`created_at`) or a git
commit author date, fetched 2026-10-21 via `gh api` / clones on the Oracle.
Inferences are labelled **[inference]**. Complements LANE-C-SHELF.md
(mechanisms) and LANE-D-LOCAL.md (local notes, which start 2026-10-05).

## Account facts (API: `users/SuperInstance`)

- **Account created: 2024-12-29T00:56:17Z**
- Public repos at fetch time: **5,306**

## Dated timeline

| Date (UTC) | Artifact | Source |
|---|---|---|
| 2024-12-29 | SuperInstance account created | API `created_at` |
| 2025-01-14 | First repo: `fishermanscopilot` — "Automate logging, analyze streams… smart routes" (fishing domain; no active-log doctrine in README) | API `created_at`; README checked |
| 2025-08-18 | `DMLog-AI` — "session log — campaign state, character persistence" | API `created_at` |
| 2025-08-19 | **`activelog-claude`** created; first commit same day (2025-08-19T11:00:53-08:00, prompt files). README: "Private automation and orchestration layer for activelog — log classification, summarization, and insight extraction." Earliest dated artifact carrying the **ActiveLog name** | API + `git log` |
| 2025-08-23 | **`activelog`** created ("complete system"). First commit in current history is 2026-04-13 (LICENSE only) — **[inference]** history was pushed/rebuilt later; the Aug 2025 creation date stands | API + `git log` |
| 2025-08-31 | `SuperInstanceEco` created. First commit 2025-10-22 contains `docs/architecture/ActiveLog-Backend-Architecture.txt` and a README titled "SuperInstance - ActiveLog Technical Repository" | API + `git show` |
| 2026-01-01 | A same-day batch of `*Log` repos (MakerLog, PersonalLog, PlayerLog, BusinessLog, RealLog, FishingLog, StudyLog) — "log" as a naming family, not the doctrine | API |
| 2026-01-10 | `ActiveLog-TechnicalRepo` (00:58) and `ActiveLog-MVP` (01:03) created; first commits in current history dated 2026-04-13 | API + `git log` |
| 2026-02-28 | `cocapn` created (repo-first agents) | API |
| 2026-03-08 | `AI-Writings` created | API |
| 2026-05-03 | `activelog-ai-pages` created ("GitHub Pages for activelog.ai") | API |
| 2026-08-17 | `elephant` created — "Every reading is someone's reading" (the perspectival-readings line, pre-October) | API |
| 2026-09-16 | `erised` created | API |
| 2026-09-30 | `edge-ledger` created — fleet sync contract, hash chains, fork rejection | API |
| 2026-10-02 | `quilt-chrono` created — "append-only reading/writing ledger over a tiny reactive cell core" | API |
| 2026-10-03 | `naDir` created — async work as directories | API |
| 2026-10-05 | **Local record begins** (Lane D): cordis-lab two-spreadsheets note, double-entry movement rule. `cordis-lab` is **NOT FOUND** on GitHub — local-only | Lane D + API 404 |
| 2026-10-08 | **`quilt-rooms`** created 15:36:19Z; single commit 15:35:46Z, message "quilt rooms: active log vs ledger, temporal agreement protocol, working template". Repo description: "Quilt room protocol: active log vs ledger, negotiated time". First-version `ACTIVE-LOG.md` ("What's happening *in* the room. The room's private timeline") and `ACTIVE-LEDGER.md` ("What's happening *between* rooms… the set of agreed links") are present in that first commit. **Earliest GitHub artifact pairing active log vs active ledger as a system representation** | API + `git show` |
| 2026-10-10 | `quilt-cells` created — "INPUT → OUTPUT + WHY + COST" | API |
| 2026-10-13 | `synoptic-sheet` created — "a live agent ledger, rendered" | API |
| 2026-10-17 | `gitpp-sheet` created — fold-as-spreadsheet, cell-level confidence | API |

## The honest caveat — two different "ActiveLog"s

The 2025 ActiveLog artifacts are **not** the two-items doctrine. The
SuperInstanceEco architecture document describes ActiveLog as "a robust,
event-driven backend platform that integrates with BLE devices, audio
recording workflows, secure file management" on AWS microservices with
CQRS/event sourcing — a logging *product*. `activelog-claude` is a
log-analysis plugin layer over it. **[Inference]** the *name* and the
log-as-first-class-record instinct run from August 2025; the *doctrine*
(log = perspectival readings, ledger = agreed transfers) first appears
in the dated GitHub record at quilt-rooms, 2026-10-08, matching the
local notes' October 5–12 formulation window. No pre-2026 README checked
(fishermanscopilot, agent, DMLog-AI, activelog-claude, activelog,
SuperInstanceEco, bootstrap, SmartCRDT, CognitiveEngine,
lucineer-flagship, HOLOS) contains the phrase "active ledger" or the
paired concept.

## Oldest 50 repositories (API order, created ascending) — concept-term flags

Flags: **log** / **ledger** / **cell** / **quilt** / **room** / **fold** /
**contract** / **agent** / **instance** matched in name or description.

| # | Repo | Created (UTC) | Flags |
|---|---|---|---|
| 1 | fishermanscopilot | 2025-01-14 | log (desc: "Automate logging") |
| 2 | amplify-fishingtool | 2025-08-12 | — |
| 3 | agent | 2025-08-16 | agent ("Core agent framework") |
| 4 | DMLog-AI | 2025-08-18 | log |
| 5 | activelog-claude | 2025-08-19 | log |
| 6 | activelog | 2025-08-23 | log |
| 7 | SuperInstanceEco | 2025-08-31 | instance |
| 8 | DMLog | 2025-10-22 | log |
| 9 | SuperInstance-Fiction | 2025-10-22 | instance |
| 10 | ec2mud | 2025-10-26 | — |
| 11 | bootstrap | 2025-11-10 | agent (desc) |
| 12 | SmartCRDT | 2025-12-22 | — (CRDT: convergent state — adjacent, unflagged term) |
| 13 | CognitiveEngine | 2025-12-26 | — |
| 14 | lucineer-flagship | 2025-12-26 | agent (desc: "Captain agent") |
| 15 | HOLOS | 2025-12-31 | — |
| 16 | SuperInstanceCore | 2026-01-01 | instance, agent (desc) |
| 17 | SwarmOrchestration | 2026-01-01 | agent (desc) |
| 18 | Murmurer | 2026-01-01 | agent (desc) |
| 19 | Murmur | 2026-01-01 | — |
| 20 | MakerLog | 2026-01-01 | log |
| 21 | PersonalLog | 2026-01-01 | log |
| 22 | StudyLog | 2026-01-01 | log |
| 23 | UI | 2026-01-01 | — |
| 24 | SuperInstanceDocs | 2026-01-01 | instance |
| 25 | SuperInstanceExamples | 2026-01-01 | instance |
| 26 | SuperInstanceCommunity | 2026-01-01 | instance |
| 27 | SuperInstancecore1 | 2026-01-01 | instance, agent (desc) |
| 28 | PlayerLog | 2026-01-01 | log |
| 29 | BusinessLog | 2026-01-01 | log ("transactions… operational records") |
| 30 | RealLog | 2026-01-01 | log ("temporal indexing") |
| 31 | FishingLog | 2026-01-01 | log |
| 32 | Tripartite1 | 2026-01-02 | — |
| 33 | Spreader-tool | 2026-01-04 | room (desc: "PLATO rooms… deadband detection") |
| 34 | project-JEPA | 2026-01-07 | — |
| 35 | CascadeRouter | 2026-01-08 | — |
| 36 | hardware-capability-profiler | 2026-01-08 | — |
| 37 | Privacy-First-Analytics | 2026-01-08 | — |
| 38 | Sandbox-Lifecycle-Manager | 2026-01-08 | — |
| 39 | Hardware-Aware-Flagging | 2026-01-08 | — |
| 40 | Bayesian-Multi-Armed-Bandits | 2026-01-08 | — |
| 41 | equilibrium-tokens | 2026-01-08 | contract (desc: "Smart contracts") |
| 42 | Automatic-Type-Safe-IndexedDB | 2026-01-08 | — |
| 43 | JEPA-Real-Time-Sentiment-Analysis | 2026-01-08 | — |
| 44 | Agent-Lifecycle-Registry | 2026-01-08 | agent |
| 45 | In-Browser-Vector-Search | 2026-01-08 | — |
| 46 | multi-device-sync | 2026-01-08 | — |
| 47 | universal-import-export | 2026-01-08 | — |
| 48 | Vibe-Code-Agent-Gen | 2026-01-08 | agent |
| 49 | AI-Smart-Notifications | 2026-01-08 | — |
| 50 | MPC-Orchestration-Optimization | 2026-01-08 | — |

No repo in the oldest 50 carries **ledger**, **cell**, **quilt**, or
**fold** in name or description. The "log" family is present from the
account's first repo onward (Jan–Aug 2025); the ledger pairing arrives
with quilt-rooms in Oct 2026.

## Method notes

- Repo creation dates are GitHub `created_at` (repository object
  creation), which can postdate local work — Lane D's local notes
  (2026-10-05) predate quilt-rooms' creation by three days.
- First-commit dates come from clones on the Oracle (`git log --reverse
  --format=%aI`). Where first-commit dates are much later than
  `created_at` (activelog, ActiveLog-*), the visible history was likely
  pushed later; both dates are reported rather than reconciled.
- README term checks used the API readme endpoint on the plausible
  pre-2026 candidates only, per brief.
