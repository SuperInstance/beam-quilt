# Deep-research lineage — SuperInstance's public Active Log / Active Ledger history

> **Provenance:** distilled on 2026-10-21 from the isolated deep-research
> report in `~/workspace/research_notes/superinstance-active-log-lineage-20261022-0024/report.md`
> and its working notes (`notes/api-core.md`, `notes/doctrine-and-sites.md`).
> **Method:** public GitHub pages and the public GitHub API, read 2026-10-21;
> dates below are from API `created_at` fields or commit objects unless a
> hedge says otherwise.
> **Scope:** this file dates the *public* record. It does not prove or
> disprove a pre-GitHub paradigm; Layer 0 in `FOUNDATIONS.md` records that
> paradigm as Casey's own statement.

## Verdict in brief

The public record separates the **name** from the **doctrine**. “ActiveLog”
is old in the SuperInstance account — first as a named repo on
**2025-08-19** — but the 2025–early-2026 ActiveLog family is conventional
activity/event logging: voice-note and business loggers, a structured
logging backend, log classification/summarization for Claude, and the
ActiveLog JSON event-envelope site. The two flagship-named repositories,
`ActiveLog-MVP` and `ActiveLog-TechnicalRepo`, are empty placeholders; the
real technical-repository content sits in `SuperInstanceEco`. None of that
is the later two-record doctrine.

The doctrine meant here — a perspectival **ACTIVE LOG** (an entity's own
state/readings, own sequence/time, no global view) paired with an
**ACTIVE LEDGER** (transfers/agreements recorded by both sides, with time
agreed rather than absolute) — is verifiably public first on
**2026-10-08**, in `quilt-rooms`. No repository named `ActiveLedger*` was
found, and no ledger-named or ledger-description-matching repository
predates **2026-05-31**.

## Timeline

| Date (UTC) | Public artifact | What it establishes — and what it does not |
|---|---|---|
| **2024-12-29** | GitHub account `SuperInstance` created (`created_at` 2024-12-29T00:56:17Z). | Earliest verified public use of the SuperInstance account name. It does not date any earlier, non-GitHub form of the paradigm. |
| **2025-01-14** | `fishermanscopilot` — first public repo. | A placeholder for a fishermen's copilot (“Automate logging…”). It carries generic logging language, not the two-record doctrine. |
| **2025-08-19** | `activelog-claude` created; the ActiveLog name enters the repo record. | A real small package for log classification, summarization and insight extraction — conventional log analytics, not a perspectival-state doctrine. See the family verdict below. |
| **2025-08-23** | `activelog` created. | Despite the “complete system” description, the checked repo was a placeholder (charter/exam/license plus a Contributor Covenant README). |
| **2025-08-31 / content dated 2025-10-22** | `SuperInstanceEco` created; root content dated 2025-10-22. | The substantive “SuperInstance - ActiveLog Technical Repository”: event-driven logging applications (voice-note, Deckboss and business loggers), multi-agent tooling, AWS deployment material, CQRS & event sourcing. The event-sourcing instinct is visible; a paired log/ledger doctrine is not stated there. The repo's `created_at` predates its root commit — see the hedges. |
| **2025-12-22** | `SmartCRDT` created. | Earliest dated distributed-state work in the checked public record (“CRDT technology for self-improving AI”). Adjacent state-convergence work; it does not use the log/ledger pairing in its description. |
| **2026-03-10** | `SuperInstance-papers` created. | Spreadsheet tiles/cells enter the named public programme: logic deconstructed into “Spread Sheet Tiles and Cells,” with instances interconnected; the papers also formalise a nested agent ⊂ harness ⊂ room ⊂ SuperInstance topology. |
| **2026-04-11** | Profile repo `SuperInstance/SuperInstance` created. | An articulated concept statement: “The spreadsheet that thinks.” Tools/agents/cells accumulate under one belief about working alongside intelligence; the same README explains Quilt through the boat use case. |
| **2026-05-31** | `lau-conservation-engine` created. | Earliest ledger-matching repo in the complete ledger enumeration: a double-entry energy ledger for agents, with budget tracking and transfer verification. A ledger precursor, not a statement of the log/ledger pair. |
| **2026-06-07** | `simulation-ledger` created. | Append-only behavioural simulation ledger with replay and differential comparison. Another ledger precursor; no paired active-log doctrine was found in it. |
| **2026-08-17** | `quilt` created. | “A spreadsheet where every cell is a live, addressable capability. The grid is the runtime.” Quilt/cell naming becomes explicit. |
| **2026-08-17** | `elephant` created. | Its description already carries the perspectival line — “Every reading is someone's reading” — months before the October doctrine statement. |
| **2026-08-23** | `saddle` created. | “Double-entry ledger per cell, frozen alignment states” — a close ledger precursor at cell granularity. |
| **2026-09-30** | `edge-ledger` created. | A fleet-state sync contract: per-node envelope hash chains, idempotent ingest, loud fork rejection, signatures, pure projections. Ledger mechanics are now close to the later programme; this still is not the named two-record doctrine. |
| **2026-10-08** | `quilt-rooms` created; single commit dated 2026-10-08T15:35:46Z. | **First public pairing of the doctrine.** Its description is “Quilt room protocol: active log vs ledger, negotiated time.” Its files separate the room's private, sequence-timed active log from the inter-room active ledger of links both sides record by agreement; agreed “universe time” is scoped to participating rooms — “Time is agreed, not absolute.” |

## ActiveLog family verdict

The deep-research lane enumerated the complete `activelog` repository
search for the account (10 results) and read the plausible carriers. The
family is genuinely Casey's programme by branding — a Cocapn-fleet family
of logging tools under the ActiveLog name — but **not** an early statement
of the two-active-items doctrine:

- `activelog-claude` (2025-08-19) is real but conventional: log
  classification, summarization and insight extraction, orchestrating
  monitoring/storage pieces.
- `activelog` (2025-08-23) is a placeholder despite “complete system.”
- `ActiveLog-TechnicalRepo` (2026-01-10) and `ActiveLog-MVP`
  (2026-01-10) are **empty placeholders** — only the same small root-file
  set was found, despite descriptions promising core technical
  documentation and an activity-tracking application.
- The real technical-repository content is in `SuperInstanceEco`
  (created 2025-08-31; root content 2025-10-22): its README is literally
  rooted at `ActiveLog-TechnicalRepo/`, and its substance is logging apps,
  event-driven microservices, CQRS & event sourcing — not the later
  perspectival log / agreed-ledger pair.
- `activelog-backend` (2026-01-10) contains real code, but it is a
  conventional structured-logging library (store/query/alert).
- `activelog-ai-pages` (2026-05-03) and the live `activelog.ai` site
  define ActiveLog as a JSON **event envelope** (`alv`, `dev`, `seq`, `ts`,
  `mono`, `type`, `body`). That site text was fetched on 2026-10-21; the
  exact commit that introduced the wording was not diffed, and the example
  event timestamp `2025-02-14T12:00:00Z` in the site payload is
  illustrative, **not** a publication date.

So: the family's verdict is **branding and a record-keeping instinct, not
the doctrine**. Do not cite any 2025 ActiveLog artifact as evidence that
the paired active-log/active-ledger anatomy was already publicly stated.

## The `ctothed` predecessor reference — UNVERIFIED

`SuperInstanceEco`'s October 2025 README points readers to
`github.com/ctothed/SuperInstance` and gives the contact `@ctothed`. That
suggests the project may previously have lived under a `ctothed`
account/repo. On 2026-10-21, however, `https://api.github.com/users/ctothed`
returned **404**; the account does not currently resolve, and no dates or
contents for any predecessor home could be verified. Treat any claim that
the programme predates December 2024 *via `ctothed`* as an inference from
a dead link — **not** a verified date.

## Hedges and limits — kept intact

- **No full-text grep of all 5,306 repositories was possible.** GitHub's
  code-search API returned 401 unauthenticated and was not retried. The
  “earliest” findings therefore rest on the oldest-first repository
  listing, the complete `activelog` enumeration (10/10), the complete
  `ledger` enumeration (27/27), and README/doc reads of every plausible
  carrier the report identified. A doctrine statement buried in an
  arbitrarily named repository's interior files before 2026-10-08 cannot
  be absolutely excluded; no name, description, title or indexed text
  found points to one.
- **API rate limits did not block the core checks.** The report's caveat
  is coverage, not rate: the search API's `sort=created` parameter was
  unreliable, so each quoted date was taken from the individual repo's own
  `created_at` field.
- **`created_at` is repository-object creation, not conception.** It can
  postdate local work, and `SuperInstanceEco` shows the reverse wrinkle:
  created 2025-08-31 but with root content dated 2025-10-22, consistent
  with an empty repo created first or history pushed later. Both dates are
  carried rather than reconciled.
- **AI-Writings was not used to move the earliest date.** The repo was
  verified as created 2026-03-08 and its root listing includes
  ledger/room/cell-titled pieces, but individual pieces were not opened or
  individually dated in this research. Sampled history showed May 2026
  commits; no AI-Writings commit predating the repo's March 2026 creation
  was found.
- **Two mission phrases were not found verbatim publicly.** “Contracts as
  algorithms” and “deductions and contributions” did not appear in any
  examined public artifact or web-indexed page for this user. If they exist
  publicly, the report's evidence says they are in un-indexed interior
  files.
- **The verified public timeline is about 22 months, not a public-years
  record.** That is a statement about dated public artifacts only. It does
  not settle the age of the private, pre-GitHub paradigm Casey describes;
  that paradigm is handled as Layer 0 in `FOUNDATIONS.md`, explicitly as
  his statement.
