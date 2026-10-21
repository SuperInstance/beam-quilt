> **Provenance:** Lane A (prior-art survey), researched by a ZCode lane on the Jetson, 2026-10-21.
> **Referee status:** spot-checked by Muse before merge — five checks independently confirmed: Dec-POMDP NEXP-hardness, Laman 2D scope, account repo count (5,306 public / 4,485 non-fork), the Anthropic ARA figures, and the MIDI arithmetic.
> **Standing:** merged into beam-quilt as the foundations layer (see FOUNDATIONS.md); verdicts here are the record the architecture docs must agree with.

# Lane A — Prior-art survey for the beam-quilt programme

Compiled 2026-10-21 per `BRIEF-A-PRIOR-ART.md`. Method: primary sources only —
the repos, papers, and official docs themselves, each fetched this session;
blog summaries used as leads at most. Every factual claim carries a source
URL. Claims that could not be verified are marked **UNVERIFIED** rather than
repeated. Tags:

- **PULL** — use as-is.
- **ADAPT** — take the mechanism, rebuild the surface.
- **STUDY** — learn from, don't use.
- **REJECT** — considered, wrong for us; reason given.

The quality bar is not efficiency. A recommendation to pull means the thing
genuinely matches the constraints below; existing or popular alone earns
nothing.

## 0. The constraints we are fitting to

Distilled from the programme repos so that every judgement in Sections 1–5 is
anchored to something the design actually requires.

- **Sovereignty.** SPEC rule P4: a patch "MUST NOT read or write another
  patch's state directly." Cross-patch influence happens only as a typed
  transmission (payload + type + sender profile + exactly one ending of
  MUTATE / MOVE / COPY / PROJECT + cost budget, rules T1–T3) that the
  receiver is free to refuse. No shared state; no coordinator a patch must
  obey. Source: [beam-quilt SPEC.md](https://raw.githubusercontent.com/SuperInstance/beam-quilt/main/SPEC.md)
- **Receiver-side comparative judging.** Seam handshake §3: the receiver
  judges "comparatively, against everything else competing for its
  attention" — accept above the go band, refuse below the no-go band, and a
  **deadband** in between whose honest outcome is "ask another question."
  Sender/receiver divergence is logged, never averaged away silently.
- **Scope distillation.** "Distill the scope, not the model": decompose a
  question at fixed, typed angles until every sub-question fits a small,
  fast judge (jev-semantic's student judge is 6.8M params, ~7 ms on ARM).
  Sources: [beam-quilt README](https://raw.githubusercontent.com/SuperInstance/beam-quilt/main/README.md)
  (jev-semantic and its student judge are listed there).
- **Edge hardware.** Runs on low-resource clusters of heterogeneous devices
  the owner already has; nodes appear and vanish.
- **Owner consent.** Rules C1–C3: enrolment/departure are owner acts; every
  crossing is logged and owner-readable; any patch — and separately the
  whole quilt — must halt without the halted thing's cooperation.
- **Recording.** chrono-tensor: every state change is a 4D event
  `[tau/tick, kappa/track 0–15, intent, theta/pressure]` plus provenance and
  cost, in one scrubbable multi-track stream with ring-buffer GC and lossy
  downmixing. chrono-flight records and plays back (scrub, mute, solo,
  branch view) and explicitly does **not** promise deterministic re-run of
  LLM-based nodes. Sources: [chrono-tensor README](https://raw.githubusercontent.com/SuperInstance/chrono-tensor/main/README.md),
  [chrono-flight README](https://raw.githubusercontent.com/SuperInstance/chrono-flight/main/README.md)
- **Cheap, auditable gates.** Rules G2–G3: "demonstrably formula-shaped"
  gate decisions SHOULD be replaced by the explicit equation, kept honest by
  a cheap judge on spot-check duty.
- **Runtime.** continuum-runtime (Rust): patches as independent processes
  over a shared event stream, woken by declared event triggers rather than
  polling; the one privileged "boundary driver" may amplify patterns but
  never command. Source: [continuum-runtime README](https://raw.githubusercontent.com/SuperInstance/continuum-runtime/main/README.md)

Additional programme context (from
[PROGRAM-MAP.md](https://raw.githubusercontent.com/SuperInstance/beam-quilt/main/PROGRAM-MAP.md)):
ideas carry statuses SETTLED / PROPOSED / SPECULATIVE / PARKED that gate
building; **"uncalibrated latent seams" are PARKED** (Section 5 below answers
what calibration would actually take); "dynamic deadband" is PROPOSED; the
boundary driver is recorded as "a limit on what an orchestrator may be"
(amplify momentum, never command); the "hologram" alternative was rejected in
beam-quilt docs/07 for sharding. quilt-engine's build order names a
TreeSplitter/ReassemblyEngine — the scope-distillation mechanism this survey's
Section 5 speaks to.

Programme repos read for this survey, all accessed 2026-10-21:
[beam-quilt](https://github.com/SuperInstance/beam-quilt) (README, SPEC.md),
[quilt-engine](https://github.com/SuperInstance/quilt-engine) (README),
[chrono-tensor](https://github.com/SuperInstance/chrono-tensor) (README),
[chrono-flight](https://github.com/SuperInstance/chrono-flight) (README),
[continuum-runtime](https://github.com/SuperInstance/continuum-runtime)
(README). All five are at "design study / scaffolding" stage, pushed
2026-10-21; only chrono-tensor carries a license (MIT) — the others have no
LICENSE file (per GitHub repo API), worth fixing before anything is "pulled"
from them.

---

## 1. Distributed / pooled inference over unreliable home hardware

Domain-level finding: every system here is **one shared model sharded across
machines** — layer spans (Petals, prima.cpp, vLLM pipeline parallelism),
memory-proportional tensor placement (llama.cpp RPC), or master/worker splits
(exo, vLLM tensor parallelism). Churn is handled either not at all (llama.cpp
RPC aborts; exo hangs; prima.cpp undocumented), by client-side
retry-and-recompute onto replacement peers (Petals), or by external
supervision restarting replicas (vLLM + Ray). None treats the receiver as a
sovereign party that may refuse an input, and none logs influence in
owner-readable form.

### Petals (bigscience-workshop/petals) — STUDY
- **Mechanism:** Layer/pipeline sharding over volunteer peers: each server
  loads a contiguous span of transformer blocks; a client-side
  `RemoteSequential` chains spans via gRPC, optionally server-to-server.
  Block location is discovered through hivemind's DHT; no central
  coordinator in the swarm itself. Also supports prompt tuning / LoRA-style
  fine-tuning by shipping gradients along the same chain. Per-session
  attention caches live on the servers that own each span.
- **Maturity:** Repo API shows pushed_at 2024-09-07 — no code push in ~2
  years (10,653 stars, not archived). README still advertises Llama 3.1
  405B; petals.dev live with network stats. Papers: ACL 2023 demo
  (arXiv:2209.01188), NeurIPS 2023. Effectively dormant research code.
- **License:** MIT (repo API).
- **Key questions:** (a) Layer/pipeline sharding, one process per block
  span; tensor parallelism is not part of the runtime. (b) Node loss
  mid-inference is handled client-side, verified in code: failing peers go
  into a `Blacklist` with 2× backoff; routing is re-derived from the DHT;
  on failure during generation the session retries with backoff up to
  `max_retries`, then closes, picks new spans, transfers `history` to
  replacement sessions, and logs "remote attention caches from block {i} to
  {j} will be regenerated" — recovery by recompute on new peers. If no
  server holds a needed block: `MissingBlocksError`. (c) KV/attention caches
  live server-side keyed by session id; only per-token hidden states move
  over the wire after the first step; there is no cache migration, only
  regeneration from client-kept hidden-state history.
- **Claim vs reality:** "BitTorrent-style" is accurate for discovery, but
  fault tolerance is per-request retry-plus-recompute, not transparent
  self-healing — the InferenceSession docstring itself warns it is "not
  fault-tolerant out of the box."
- **Tag reason:** The closest prior embodiment of beam-quilt's churn model:
  independent owner-run processes, DHT discovery, receiver-side
  re-derivation of routing, explicit ban/backoff instead of a coordinator,
  and recompute-as-recovery when a peer vanishes — all verified in code, and
  the cache-regeneration behavior is exactly the honest fallback beam-quilt's
  seam arbitration would need. But it is dormant, Python/PyTorch-heavy
  (wrong weight class for ~7 ms ARM judges), and its unit of sovereignty is
  a layer span of one shared model, not independent patches with typed,
  refusable transmissions. Pull the sequence-manager pattern, not the
  system.
- **Sources:** [repo API](https://api.github.com/repos/bigscience-workshop/petals) ·
  [README](https://raw.githubusercontent.com/bigscience-workshop/petals/main/README.md) ·
  [petals.dev](https://petals.dev/) · [arXiv:2209.01188](https://arxiv.org/abs/2209.01188) ·
  [sequence_manager.py](https://raw.githubusercontent.com/bigscience-workshop/petals/main/src/petals/client/routing/sequence_manager.py) ·
  [inference_session.py](https://raw.githubusercontent.com/bigscience-workshop/petals/main/src/petals/client/inference_session.py)

### exo (exo-explore/exo) — REJECT
- **Mechanism:** Turns a set of (primarily Apple Silicon) machines into one
  inference cluster: peer discovery via Zenoh, MLX as engine, and
  topology-aware placement choosing pipeline or tensor splits. Source tree
  has `src/exo/master/` (placement, main) and `src/exo/worker/` — an
  explicit master/worker split, plus `--no-worker` "coordinator-only nodes
  that handle networking and orchestration." RDMA over Thunderbolt 5
  requires full-mesh cabling and matching macOS versions.
- **Maturity:** Repo API: pushed_at 2026-10-13, 47,998 stars, very active
  but Apple-first (macOS app needs macOS 26.2+; Linux CPU-only per README).
  README's own TODO shows basics still open (EXO_BOOTSTRAP_PEERS "is
  currently broken"; continuous-batching prefill blocking decode).
- **License:** Apache-2.0 (repo API).
- **Key questions:** (a) Pipeline and tensor parallelism with
  heterogeneity-aware placement; previews exposed at `/instance/previews`.
  (b) Node loss mid-inference: no documented recovery anywhere fetched —
  README and docs/api.md are silent; TODO.md contains no node-failure
  recovery, resharding, or leader-election item. Issue #1137 (closed by PR
  #1620) shows the reality: an inter-node communication hang pegs GPUs at
  100%, and killing exo "results in a GPU lock" with memory never released
  until reboot — no graceful teardown. (c) KV-cache handling: UNVERIFIED —
  not described in README, docs, or TODO fetched.
- **Claim vs reality:** "Run frontier AI locally" cluster marketing vs a
  master/worker design with coordinator-only nodes, an undocumented failure
  story, and RDMA gated on a full TB5 mesh of same-version Macs — clusters
  effectively static and homogeneous by requirement.
- **Tag reason:** exo assumes a managed cluster with a placement master and
  persistent membership — the inverse of beam-quilt's sovereignty rule (no
  mandatory coordinator) and owner-consent churn (nodes vanish mid-work).
  Its failure mode (hang, GPU lock, reboot) is precisely what beam-quilt's
  receiver-refusal and deadband design must avoid, and nothing in its docs
  addresses refusable inputs or owner-readable influence logs. The only
  transferable artifact is the placement-preview API idea (make sharding
  proposals inspectable before commitment).
- **Sources:** [repo API](https://api.github.com/repos/exo-explore/exo) ·
  [README](https://raw.githubusercontent.com/exo-explore/exo/main/README.md) ·
  [src/exo](https://github.com/exo-explore/exo/tree/main/src/exo) ·
  [master/](https://github.com/exo-explore/exo/tree/main/src/exo/master) ·
  [issue #1137](https://github.com/exo-explore/exo/issues/1137) ·
  [TODO.md](https://raw.githubusercontent.com/exo-explore/exo/main/TODO.md)

### prima.cpp (OpenCPIL/prima.cpp) — STUDY
- **Mechanism:** llama.cpp fork for 30–70B models across "laptops, desktops,
  phones, and tablets": pipelined-ring parallelism — devices form a logical
  ring, each owning a layer window; for one token the hidden state cycles
  the ring k times, and each device sends only FP32 activations ("4e
  bytes") to its neighbor. A HiGHS-based scheduler (Halda) assigns layers
  from measured compute/disk/RAM and can keep a device as a zero-workload
  relay if removing it would break the ring. Weights stream from disk via
  mmap with prefetch to overlap I/O.
- **Maturity:** Repo API: pushed_at 2026-09-06, 113 stars, MIT; official
  repo linked from the arXiv page (2504.08791, v3 dated 2026-07-04; ICLR
  2026 per repo topics). CUDA + Apple Metal only. Research-scale adoption.
- **License:** MIT (repo API).
- **Key questions:** (a) Ring pipeline parallelism over layer windows — not
  tensor parallelism. (b) Node loss mid-inference: not handled — the paper
  describes no fault-tolerance or crash-recovery mechanism; the only
  churn-adjacent behavior is repartitioning "whenever the task queue is
  empty" and keeping weak/relay devices in the ring; unexpected failure
  mid-run is UNVERIFIED beyond the absence of any mechanism. (c) KV cache:
  per-layer KV lives on the device that owns the layer; nothing about KV
  moves over the network — only activations. No migration/eviction story
  beyond disk streaming of weights.
- **Claim vs reality:** Marketing targets exactly "heterogeneous home
  devices," and the ring/relay/weak-device-removal story is real, but
  "first distributed inference system for consumer home clusters" quietly
  means a fixed, cooperative cluster: a dead node breaks the ring with no
  recovery, unlike the churn-tolerance implied by the home-cluster framing.
- **Tag reason:** Hardware-wise this is beam-quilt's neighborhood (mixed
  ARM/phones over Wi-Fi, disk-constrained, heterogeneity-aware placement),
  and Halda plus ring-relay logic is genuinely valuable prior art for
  splitting work across unreliable owner hardware. But its execution model
  hard-codes continuous ring connectivity over one shared model — no
  refusal, no consent, no per-node sovereignty, and a vanished node stops
  the world. beam-quilt's typed-angle scope distillation (many small
  judges) is a different decomposition axis than layer windows: study the
  placement math and I/O overlap, don't pull.
- **Sources:** [repo API](https://api.github.com/repos/OpenCPIL/prima.cpp) ·
  [README](https://raw.githubusercontent.com/OpenCPIL/prima.cpp/main/README.md) ·
  [arXiv:2504.08791](https://arxiv.org/abs/2504.08791) ·
  [paper HTML v3](https://arxiv.org/html/2504.08791v3)

### Hivemind / SWARM (learning-at-home/hivemind) — STUDY
- **Mechanism:** PyTorch library for decentralized *training* over
  volunteers: a DHT ("Distributed Hash Table allows connecting computers in
  a decentralized network"), decentralized gradient averaging (Moshpit
  SGD), decentralized MoE layers, and training primitives tolerant to
  slow/unresponsive peers ("forward and backward passes succeed even if
  some nodes are unresponsive"). The companion SWARM parallelism algorithm
  (arXiv:2301.11913) builds "temporary randomized pipelines between nodes
  that are rebalanced in case of failure" — Petals is the inference system
  built on top.
- **Maturity:** Repo API: pushed_at 2026-01-11, 2,542 stars, MIT; README
  self-describes as "active development stage"; latest tag 1.1.12
  (2026-01-03). The original `swarm` repo (the BLOOM-176B demo) is gone:
  the GitHub API and web URL both return 404 and the org no longer lists
  it. Search results report it was renamed to Petals in late 2022 — that
  rename is UNVERIFIED against a live GitHub page; what is verified is that
  the URL is dead and the org no longer lists it.
- **License:** MIT (repo API for hivemind).
- **Key questions:** (a) Split: data-parallel workers with decentralized
  averaging plus MoE expert partitioning; SWARM parallelism chains model
  shards into temporary pipelines (not a fixed pipeline graph). (b) Node
  loss handled by design, for training: "succeed even if some nodes are
  unresponsive" plus "rebalanced in case of failure"; pass chains re-form
  around departed nodes. (c) KV cache: not applicable — training library.
- **Claim vs reality:** "Decentralized" holds up better than most: no
  master node, DHT membership. But it is a training substrate whose famous
  demo (swarm) has silently disappeared from GitHub — "thousands of
  volunteers" is historical evidence, not a living system.
- **Tag reason:** The strongest architectural precedent for beam-quilt's
  operating envelope: unreliable, individually-owned nodes, no coordinator,
  and failure handled by *re-forming the collaboration topology around*
  departed peers rather than restoring them — philosophically aligned with
  receiver-side refusal and owner departure as a normal event. But it
  solves a different problem (gradient averaging for one shared model) in
  heavyweight Python. Adopt the mental model (membership as gossip,
  redundancy over repair); don't adopt the code.
- **Sources:** [repo API](https://api.github.com/repos/learning-at-home/hivemind) ·
  [README](https://raw.githubusercontent.com/learning-at-home/hivemind/master/README.md) ·
  [arXiv:2301.11913](https://arxiv.org/abs/2301.11913) ·
  [tags](https://github.com/learning-at-home/hivemind/tags) ·
  [dead swarm URL](https://github.com/learning-at-home/swarm) ·
  [org listing](https://github.com/learning-at-home)

### llama.cpp distributed mode = RPC backend (ggml-org/llama.cpp, tools/rpc) — REJECT
- **Mechanism:** The distributed mode is the ggml RPC backend:
  `ggml-rpc-server` on each worker exposes its compute devices; the main
  node runs `llama-cli`/`llama-server` with `--rpc host:port,...` and
  llama.cpp "distributes model weights and the KV cache across all
  available devices — both local and remote — in proportion to each
  device's available memory" (override via `--tensor-split`). That is
  memory-proportional tensor/buffer placement across a static worker list,
  master holds the model file. Optional worker-side tensor cache; TCP with
  optional RDMA.
- **Maturity:** Repo API: pushed 2026-10-21, 131,943 stars, MIT. The RPC
  README states it is "currently in a proof-of-concept development stage.
  As such, the functionality is fragile and insecure" and "Never run the
  RPC server on an open network or in a sensitive environment!" No other
  distributed mode exists in the current repo; any older
  distributed/streaming mode: UNVERIFIED (nothing in the current repo).
- **License:** MIT (repo API).
- **Key questions:** (a) Not layer sharding proper: arbitrary ggml tensors
  and KV-cache chunks placed across workers proportionally to free memory
  (backend-level offload, not model-level parallelism). (b) Node loss
  mid-inference: no reconnection or fault tolerance documented; empirically
  the client aborts — issue #15055 shows the client printing "Remote RPC
  server crashed or returned malformed response" and dying with SIGABRT on
  a socket failure, with no retry path. (c) KV cache is sharded across
  workers with the weights; movement over the wire is per-op tensors; no
  migration or eviction story.
- **Claim vs reality:** "Distributed LLM inference" oversells a fragile,
  insecure PoC with static membership: the failure story is client abort,
  and the security posture is explicitly "never expose it."
- **Tag reason:** This is shared-state distribution, not sovereignty: a
  master reaches into remote devices' memory via an unauthenticated,
  unlogged channel and dies outright when a worker vanishes — the exact
  anti-pattern of receiver-judged, refusable transmissions with
  owner-readable logs. (llama.cpp itself remains relevant to beam-quilt as
  a local inference engine on ARM boards; the distributed RPC mode
  specifically does not.)
- **Sources:** [repo API](https://api.github.com/repos/ggml-org/llama.cpp) ·
  [tools/rpc README](https://raw.githubusercontent.com/ggml-org/llama.cpp/master/tools/rpc/README.md) ·
  [examples listing](https://api.github.com/repos/ggml-org/llama.cpp/contents/examples) ·
  [issue #15055](https://github.com/ggml-org/llama.cpp/issues/15055)

### vLLM multi-node (vllm-project/vllm) — REJECT
- **Mechanism:** Datacenter multi-GPU/multi-node serving: Megatron-style
  tensor parallelism (needs NVLink-class links), pipeline parallelism with
  uneven layer splits, data-parallel attention + expert parallel for MoE.
  Multi-node uses Ray as the default executor, started via head/worker
  containers. Disaggregated prefill moves prefill KV from prefill instances
  to decode instances over connectors (NIXL/UCX, LMCache, Mooncake, shared
  storage) via `--kv-transfer-config` — explicitly "experimental."
- **Maturity:** Repo API: pushed 2026-10-21, 94,820 stars, Apache-2.0;
  extremely active, widely deployed in production serving. The docs' own
  warnings: "Keep the shells running these commands open; closing any shell
  terminates the cluster," and disagg prefill "DOES NOT improve
  throughput."
- **License:** Apache-2.0 (repo API).
- **Key questions:** (a) All of tensor/pipeline/data/expert parallelism
  plus prefill/decode disaggregation — the broadest split portfolio of the
  six. (b) Node loss mid-inference: recovery is delegated to the
  surrounding layer — Ray Serve "periodically health-checks each Serve
  deployment replica and restarts it on failure" (Ray docs), i.e., replica
  restart, not mid-flight continuity; elastic repartitioning of a running
  engine is not offered. (c) KV cache: the most developed story anywhere —
  per-instance paged KV with explicit cross-instance KV transfer for
  disaggregation over nine connector types; that is migration of cache
  between roles, not between churning heterogeneous nodes.
- **Claim vs reality:** vLLM's own docs are honest that elastic/fault
  behavior lives outside vLLM (Ray/KubeRay supervision, replica restart) —
  but "multi-node" in practice means clusters of identical GPUs with
  InfiniBand/RDMA, where a dead node is an ops incident, not a normal
  Tuesday.
- **Tag reason:** Zero constraint match with beam-quilt: GPU-class
  homogeneous nodes, fast interconnects assumed, Ray head-node orchestration
  (a mandatory coordinator), restart-based fault handling, and no notion of
  per-owner consent, refusable inputs, or logged influence. Its one
  exportable idea is the disaggregated-KV connector architecture — typed,
  role-to-role cache transfer with pluggable transports — a useful design
  reference if beam-quilt ever needs to move context between judges.
- **Sources:** [repo API](https://api.github.com/repos/vllm-project/vllm) ·
  [parallelism & scaling docs](https://docs.vllm.ai/en/latest/serving/parallelism_scaling.html) ·
  [disagg prefill docs](https://docs.vllm.ai/en/latest/features/disagg_prefill.html) ·
  [Ray Serve fault tolerance](https://docs.ray.io/en/latest/serve/production-guide/fault-tolerance.html)

## 2. Actor / process runtimes and supervision

Domain-level finding: the only system where process sovereignty — isolated
state, copied messages, refusable reception, crash-and-restart — is the native
substrate rather than a layered feature is the BEAM. The other four all anchor
on a datacenter artifact beam-quilt cannot host: Akka's commercial license,
Orleans' durable central membership table, Temporal's mandatory database
service, Ray's head-node GCS.

### Erlang/OTP and the BEAM (erlang/otp) — PULL
- **Mechanism:** Processes each own a private heap (a fresh process costs
  327 words) GC'd independently; "All data in messages sent between Erlang
  processes is copied," so there is no shared mutable state — single-writer
  by construction. Per-process mailboxes with selective receive ("A receive
  expression will select the first message... that matches"); sends are
  asynchronous. Supervisors keep children alive via one_for_one /
  one_for_all / rest_for_one strategies with MaxR-restarts-in-MaxT-seconds
  escalation that terminates the supervisor itself and bubbles up the tree.
  The code server keeps two module generations (current + old); hot swap
  via `code_change`. Distributed Erlang forms a transitive full mesh via
  epmd; `global` keeps replicated name tables with a user-supplied resolve
  function for name clashes on partition heal.
- **Maturity:** Repo pushed 2026-10-21, 12,353 stars; latest release
  OTP-29.1.2 (2026-10-14). Production use claimed "in telecoms, banking,
  e-commerce, computer telephony and instant messaging" on erlang.org
  (companies not named on that page; specific deployments UNVERIFIED this
  session).
- **License:** Apache-2.0 — verified in the repo API and the LICENSE file
  itself.
- **Key questions:** Borrowables: per-process heaps + message copying
  (patch sovereignty is native here, not bolted on), mailboxes/selective
  receive (a patch's inbox is a natural seam-arbitration chokepoint —
  refuse-by-not-receiving is idiomatic), supervision trees with
  restart-intensity escalation, monitors/links as typed failure
  propagation, two-version code swap. Datacenter assumptions to avoid:
  distributed Erlang's full mesh and its cookie security model — the docs
  themselves admit cookie auth is "not in a cryptographically secure
  manner" and inter-node traffic is cleartext without explicit TLS, i.e.,
  uniform-trust clusters; `global`'s default partition-heal resolver
  `random_exit_name/3` "randomly selects one of the pids... and kills the
  other one" — exactly the wrong default for owner-consent nodes.
  net_ticktime gives a documented, tunable liveness boundary, and OTP 25's
  `prevent_overlapping_partitions` behavior — disconnecting nodes because
  overlapping partitions "might cause the internal state of global to
  become inconsistent" — is the best documented account anywhere of what
  netsplits do to names.
- **Claim vs reality:** none found — the docs are unusually candid (the
  security warnings are in the official distributed-Erlang page). One
  nuance: the famous "let it crash" phrase does not appear in the current
  supervision docs; the philosophy is present implicitly through the
  MaxR/MaxT escalation design.
- **Tag reason:** beam-quilt's README already models patches on BEAM
  processes, and this is the one system where the core mechanism (isolated
  per-process state, copied messages, no shared memory) is the load-bearing
  design rather than a feature. The supervision, mailbox, monitor, and
  two-version hot-swap mechanics transfer directly to continuum-runtime's
  sovereign patches, with Apache-2.0 licensing and a 30-year paper trail.
  The pull is selective: take the process model and failure semantics
  whole, and reject the distribution layer (full mesh, cookie trust,
  kill-the-loser global resolution), which assumes exactly the
  uniform-trust, stable-connectivity cluster that edge hardware is not.
- **Sources:** [repo API](https://api.github.com/repos/erlang/otp) ·
  [releases](https://api.github.com/repos/erlang/otp/releases/latest) ·
  [LICENSE](https://raw.githubusercontent.com/erlang/otp/master/LICENSE.txt) ·
  [supervision principles](https://www.erlang.org/doc/design_principles/sup_princ.html) ·
  [gen_server](https://www.erlang.org/doc/man/gen_server.html) ·
  [processes](https://www.erlang.org/doc/system/ref_man_processes.html) ·
  [efficiency guide: processes](https://www.erlang.org/doc/efficiency_guide/processes.html) ·
  [distributed Erlang](https://www.erlang.org/doc/system/distributed.html) ·
  [net_kernel](https://www.erlang.org/doc/man/net_kernel.html) ·
  [global](https://www.erlang.org/doc/man/global.html) ·
  [code server](https://www.erlang.org/doc/man/code.html)

### Akka (akka/akka) — REJECT (see Apache Pekko sub-entry: STUDY)
- **Mechanism:** An actor is "a container for State, Behavior, a Mailbox,
  Child Actors and a Supervisor Strategy," hidden behind an ActorRef that
  survives restarts and relocation; no mailbox scanning — "the current
  behavior must always handle the next dequeued message." Supervision
  offers exactly three directives (Resume keeping state, Restart clearing
  state, Stop); restart swaps a fresh Behavior into the existing ActorRef
  and "the restart is not visible outside of the actor itself," but the
  failing message "will be lost." Delivery guarantees are documented as
  precisely two: "at-most-once delivery, i.e. no guaranteed delivery" and
  per-sender-receiver ordering (not transitive); dead letters are
  best-effort.
- **Maturity:** Repo pushed 2026-10-16, 13,280 stars; latest release
  v2.10.24 (2026-10-09) — actively shipping. Production-use claims:
  UNVERIFIED in docs fetched this session.
- **License:** Business Source License 1.1 — verified from the LICENSE file
  itself: "Licensor: Lightbend, Inc.", Licensed Work "Akka 2.10.24", Change
  Date 2029-10-09, Change License Apache 2.0; the Additional Use Grant only
  permits limited akka-streams binary use inside Play Framework. Production
  use is restricted until the Change Date.
- **Key questions:** Borrowables: the delivery-guarantee documentation is
  the field's best articulation of "the only meaningful way for a sender to
  know whether an interaction was successful is by receiving a
  business-level acknowledgement" — directly relevant to beam-quilt's
  logged, refusable transmissions; also DeathWatch (unidirectional
  monitors) and the resume/restart/stop triage. Datacenter assumptions:
  JVM ecosystem; streaming/cluster tooling built for datacenter fleets;
  ownership now assumes commercial licensing.
- **Claim vs reality:** The repo self-description — "A platform to build
  and run apps that are elastic, agile, and resilient" — coexists with a
  license that forbids most production use until 2029 and docs that
  guarantee nothing about delivery and drop the failing message on the
  floor during supervision. "Resilient" is conditional on the application
  building its own ack/retry layer, which the delivery-reliability page
  states plainly.
- **Tag reason:** The license alone is disqualifying for a sovereign,
  owner-governed system: BSL 1.1 until 2029-10-09 means beam-quilt could
  not legally embed or depend on Akka in deployment, and its delivery and
  supervision semantics are a JVM re-implementation of mechanisms
  beam-quilt gets more directly from Erlang/OTP. The correct relationship
  is documentary: the message-delivery-reliability page should be read as a
  design reference for seam arbitration's honest-failure stance.
- **Sub-entry: Apache Pekko — STUDY.** Verified from repo API (pushed
  2026-10-20, 1,653 stars, Apache-2.0) and README ("a fork of Akka 2.6.x,
  prior to the Akka project's adoption of the Business Source License";
  JDK 17+). Latest stable 1.x number UNVERIFIED (tags show v2.0.0-M4
  milestone as newest). It is the legitimate Apache-2.0 continuation, but
  JVM-only and implements the same mailbox/supervision model already
  sourced from Erlang/OTP — hence STUDY, not a pull candidate for a
  Rust-based continuum-runtime.
- **Sources:** [akka repo API](https://api.github.com/repos/akka/akka) ·
  [akka releases](https://api.github.com/repos/akka/akka/releases/latest) ·
  [akka LICENSE (BSL 1.1)](https://raw.githubusercontent.com/akka/akka/main/LICENSE) ·
  [supervision docs](https://doc.akka.io/docs/akka/current/general/supervision.html) ·
  [message-delivery-reliability docs](https://doc.akka.io/docs/akka/current/general/message-delivery-reliability.html) ·
  [actors docs](https://doc.akka.io/docs/akka/current/general/actors.html) ·
  [pekko repo API](https://api.github.com/repos/apache/pekko) ·
  [pekko README](https://raw.githubusercontent.com/apache/pekko/main/README.md)

### Microsoft Orleans (dotnet/orleans) — STUDY
- **Mechanism:** Virtual actors ("grains") with stable logical identities;
  "at most one activation of a grain exists at any point in time," identity
  separate from activation — idle activations are removed and a later call
  re-activates the grain; the runtime "routes each call to an existing
  activation or activates the grain on demand." Turn-based per-activation
  execution gives single-threaded state handling without locks.
  Clustering requires a durable `IMembershipTable` (SQL, DynamoDB,
  Cassandra, ZooKeeper, Cosmos DB, Azure Table, Consul, Redis) as "a
  rendezvous point for silos to find each other"; silos probe 10 peers
  every 10 s, 3 missed probes = suspicion, 2 suspicions in 3 min = dead
  (~15 s detection). A partitioned, eventually-consistent grain directory
  maps grain identity to owning silo; placement happens at activation.
- **Maturity:** Repo pushed 2026-10-21, 10,908 stars; latest release
  v10.4.0 (2026-10-03); the cluster-management doc states the membership
  protocol "has proven effective in production with up to 200 silos" with
  defaults "hand-tuned during production usage in Azure" — Orleans' own
  docs.
- **License:** MIT — verified from repo API and the LICENSE file.
- **Key questions:** Borrowables: the identity-persists/activation-is-ephemeral
  split is a clean model for patches whose identity survives process
  restarts on unreliable nodes; turn-based execution matches single-writer
  patch state; the membership protocol's accuracy-over-completeness
  property — "partitioning from the table never causes Orleans to
  mistakenly declare a silo dead" — is a well-documented design for flaky
  networks. Datacenter assumptions: the durable external membership table
  is the central-store assumption beam-quilt explicitly forbids; "once a
  silo is declared dead... an infrastructure must be in place to restart
  the silo" (Kubernetes/Azure assumed); new silos "must validate two-way
  connectivity to every other active silo" before joining.
- **Claim vs reality:** The single-activation guarantee is softer than
  marketed: the default grain directory "allows occasional duplicate
  activations during cluster instability"; the strongly-consistent
  directory that fixes this is explicitly "in **preview**" in Orleans 10.0.
  To its credit, the overview is candid that the runtime "doesn't
  automatically replicate application state" and "calls in progress can
  fail and callers must handle that outcome."
- **Tag reason:** Orleans solves a problem beam-quilt deliberately does not
  have — transparently placing and single-activating a global namespace of
  entities — and its solution routes through exactly the artifacts the
  sovereignty constraint bans: a durable shared membership table and a
  cluster-wide directory. The virtual-actor identity/activation split,
  turn-based execution, and the probe/suspicion failure-detection tuning
  are worth adapting into continuum-runtime's trigger-and-arrival model,
  but the runtime itself is a datacenter citizen (.NET, Azure-tuned
  defaults, restart infrastructure).
- **Sources:** [repo API](https://api.github.com/repos/dotnet/orleans) ·
  [releases](https://api.github.com/repos/dotnet/orleans/releases/latest) ·
  [LICENSE](https://raw.githubusercontent.com/dotnet/orleans/main/LICENSE) ·
  [overview](https://dotnet.github.io/orleans/docs/overview/) ·
  [cluster management](https://learn.microsoft.com/en-us/dotnet/orleans/implementation/cluster-management) ·
  [grain directory](https://learn.microsoft.com/en-us/dotnet/orleans/host/grain-directory) ·
  [grain placement](https://learn.microsoft.com/en-us/dotnet/orleans/grains/grain-placement)

### Temporal (temporalio/temporal) — STUDY
- **Mechanism:** Durable execution via event sourcing: a workflow emits
  Commands and the service records Events in an append-only History that
  "is the source of truth for everything that happens in the Workflow." On
  any resume, Temporal "starts the Workflow code from the beginning,
  replays the Event History step by step," and each re-emitted Command must
  match the Event at the same position — a mismatch yields a
  `_non-deterministic_` error. Determinism rule: "any time your Workflow
  code is executed it makes the same Workflow API calls in the same
  sequence, given the same input." Everything impure — "API calls, LLM/AI
  invocations, database queries, and other external interactions" — must go
  through Activities, which "execute outside the replay path" and are not
  re-executed during replay; SDKs provide replay-safe time and randomness.
  Deployment: "A Temporal Service's only required dependency for basic
  operation is the Persistence database" (Cassandra/PostgreSQL/MySQL;
  SQLite "only for development and testing").
- **Maturity:** Repo pushed 2026-10-21, 23,890 stars; latest release
  v1.32.1 (2026-10-08). Own docs claim executions "can run—and keep
  running—for years, even if the underlying infrastructure fails."
- **License:** MIT — verified from repo API and the LICENSE file.
- **Key questions:** What makes replay deterministic: workflow code must be
  a pure function of (input, history) — commands matched positionally
  against recorded events, with replay-safe replacements for time and
  randomness. What breaks it, per the docs: branching on "a local time
  setting or a random number," direct I/O in workflow code, and any
  unversioned code change that alters the command sequence while
  executions are open. This maps one-to-one onto beam-quilt's model-call
  nondeterminism: an LLM call is exactly the kind of impure operation
  Temporal forces outside the replay core, and the command/event
  position-matching discipline is the honest way to detect drift — a
  deadband/ask-a-narrower-question policy is the layer Temporal lacks.
  Datacenter assumptions: a dedicated database cluster plus a server fleet
  is the minimum deployment; SQLite is dev-only.
- **Claim vs reality:** The "runs for years" headline is conditional on a
  discipline the same docs document failing: the workflow-task-errors
  reference catalogues replay-breaking failures from ordinary code
  evolution — durability holds only while versioning hygiene is maintained.
- **Tag reason:** Temporal's server is datacenter infrastructure and its
  workers obey a replay contract that assumes code evolves under versioning
  governance — both incompatible with owner-consent, intermittently
  present edge nodes. But it is the only system in this survey whose core
  technical problem — keeping re-execution honest when the logic contains
  nondeterminism — is beam-quilt's model-call problem, and its documented
  answer (position-matched command/event replay, activities as the impure
  boundary, explicit versioning gates) should be adapted into how patches
  log and re-derive their state from transmissions. Study the determinism
  machinery intensely; do not deploy the platform.
- **Sources:** [repo API](https://api.github.com/repos/temporalio/temporal) ·
  [releases](https://api.github.com/repos/temporalio/temporal/releases/latest) ·
  [LICENSE](https://raw.githubusercontent.com/temporalio/temporal/main/LICENSE) ·
  [workflow definition](https://docs.temporal.io/workflow-definition) ·
  [workflows](https://docs.temporal.io/workflows) ·
  [workflow task errors](https://docs.temporal.io/references/workflow-task-errors) ·
  [persistence](https://docs.temporal.io/self-hosted-guide/persistence)

### Ray (ray-project/ray) — STUDY
- **Mechanism:** Actors are "a stateful worker (or a service)": each gets a
  dedicated worker process whose methods "run serially" — single-writer per
  actor at the OS-process level, but chosen and scheduled by the cluster
  ("For each actor, Ray chooses a node to run it on"). Failure handling is
  opt-in: by default "Ray actors won't be restarted and actor tasks won't
  be retried"; on restart "its state will be recreated by rerunning its
  constructor." The distributed object store recovers lost objects by
  lineage reconstruction — "re-executing the task that previously created
  the value," recursively — but "objects created by ray.put are not
  recoverable," and lineage memory is capped (`RAY_max_lineage_bytes`,
  default 1 GB). Ownership dominates: non-detached actors fate-share with
  their creator ("if the owner of an actor dies, then the actor will also
  fate-share with the owner"), and "Ray can automatically recover from data
  loss but not owner failure." The head node hosts the GCS: "By default,
  the GCS isn't fault tolerant because it stores all data in memory. If it
  fails, the entire Ray cluster fails."
- **Maturity:** Repo pushed 2026-10-21, 44,101 stars; latest release
  ray-2.59.0 (2026-10-02); repo description repositions Ray as "an AI
  compute engine." Production-use claims: UNVERIFIED in pages fetched.
- **License:** Apache-2.0 — verified from repo API and the LICENSE file.
- **Key questions:** Borrowables: lineage reconstruction
  (recompute-from-provenance instead of replication) is directly relevant
  to rebuilding patch state from a logged event stream, as is the
  named/detached-actor concept (identity decoupled from creator's
  lifetime). Datacenter assumptions: the head-node GCS is a default single
  point of total cluster failure; escaping it requires external Redis or an
  "alpha, Linux only" embedded store. Owner-based fate sharing and central
  scheduler placement directly contradict patch sovereignty.
- **Claim vs reality:** "Resilient" as a framework pillar meets a
  documented default where one head-node process failure kills the entire
  cluster, and "fault tolerant" actors lose all state on restart unless the
  application checkpoints externally. The GCS and fault-tolerance pages
  state these limits plainly, so the gap is between marketing framing and
  documented defaults, not hidden.
- **Tag reason:** Ray presumes a datacenter: a head node with in-memory
  control state whose default failure mode is total cluster loss, an
  ownership model where a creator's death deletes dependents, and central
  placement — anathema to sovereign, owner-consented patches on unreliable
  edge hardware. Its genuinely valuable idea for beam-quilt is lineage
  reconstruction over a logged event stream, conceptually adjacent to
  continuum-runtime's design anyway (patches re-derivable from logged
  transmissions): study the lineage semantics and its documented failure
  boundaries (ray.put, owner death, lineage caps) and build the edge-native
  version rather than pull the platform.
- **Sources:** [repo API](https://api.github.com/repos/ray-project/ray) ·
  [releases](https://api.github.com/repos/ray-project/ray/releases/latest) ·
  [LICENSE](https://raw.githubusercontent.com/ray-project/ray/master/LICENSE) ·
  [actors](https://docs.ray.io/en/latest/ray-core/actors.html) ·
  [actor fault tolerance](https://docs.ray.io/en/latest/ray-core/fault_tolerance/actors.html) ·
  [object fault tolerance](https://docs.ray.io/en/latest/ray-core/fault_tolerance/objects.html) ·
  [GCS fault tolerance](https://docs.ray.io/en/latest/ray-core/fault_tolerance/gcs.html)

## 3. Agent frameworks and arbitration

Domain-level finding: across all five frameworks, work moves by **sender
decision** (OpenAI handoffs, Letta subagent tool calls) or **orchestrator
decision** (AutoGen's group-chat manager, CrewAI's hierarchical manager,
LangGraph's compiled edges) — never by receiver-side comparative admission,
and no framework documents a deadband whose honest outcome is to return a
narrower question. In the gating literature (§3b) the combination beam-quilt
wants — receiver-side comparative scoring with an explicit
ask-a-narrower-question outcome — also appears unclaimed.

### 3a. Frameworks

### AutoGen (microsoft/autogen) — REJECT
- **Mechanism:** v0.4 was an async-first, event-driven rewrite in three
  layers: Core (pub/sub runtime where agents subscribe/publish to typed
  topics), AgentChat (turn-based teams: `RoundRobinGroupChat`,
  `SelectorGroupChat`), and Extensions. In group chat, a Group Chat Manager
  selects the next speaker (LLM-based selector by default, overridable with
  a deterministic `selector_func`) and sends a `RequestToSpeak`; the chosen
  agent publishes to a shared topic everyone sees. The README now carries a
  banner: "Project archived and no longer maintained… Microsoft is not
  providing bug fixes, security fixes, new features, or support." Successor
  is Microsoft Agent Framework (microsoft/agent-framework: graph-based
  workflows with "checkpointing, streaming, human-in-the-loop, and
  time-travel," built-in OpenTelemetry, MIT).
- **Maturity:** Contradictory signals worth flagging: the GitHub API shows
  pushed_at 2026-10-16, 61,515 stars, `archived: false` — but the README
  itself declares the project archived/no-longer-maintained and warns
  against production use (the GitHub archived flag lags the README's own
  banner). Successor repo microsoft/agent-framework: pushed 2026-10-21,
  14,254 stars, active.
- **License:** Repo-root LICENSE is CC-BY-4.0 (verified via license API);
  README states code is MIT and documentation CC-BY-4.0.
- **Key questions:** Routing is orchestrator-decided: a manager picks who
  speaks next; "candidate_func" narrows the eligible set. No refusal
  mechanism is documented in AgentChat — the turn is imposed and the
  selection broadcast. At the Core layer an agent can silently not publish
  after `RequestToSpeak`, but the docs note the chat then stalls — refusal
  is stall, not arbitration. No deadband/abstain analog. No built-in
  logging/replay of routing decisions.
- **Claim vs reality:** The README markets a scale/extensibility/
  robustness rewrite while simultaneously declaring the project dead.
- **Tag reason:** AutoGen's arbitration point is a central manager that
  participants cannot refuse except by silence, and work moves over a
  shared broadcast topic (shared state, not typed bilateral transmissions)
  — directly incompatible with beam-quilt's sovereignty and receiver-side
  comparative admission. Even the Core pub/sub layer, the closest analog
  to typed transmissions, gives receivers no comparative judge, no
  deadband, no divergence logging. Nothing here is worth pulling that the
  successor doesn't redo better.
- **Sources:** [repo API](https://api.github.com/repos/microsoft/autogen) ·
  [license API](https://api.github.com/repos/microsoft/autogen/license) ·
  [README](https://raw.githubusercontent.com/microsoft/autogen/main/README.md) ·
  [selector group chat docs](https://microsoft.github.io/autogen/stable/user-guide/agentchat-user-guide/selector-group-chat.html) ·
  [group chat design pattern](https://microsoft.github.io/autogen/stable/user-guide/core-user-guide/design-patterns/group-chat.html) ·
  [agent-framework repo API](https://api.github.com/repos/microsoft/agent-framework)

### LangGraph (langchain-ai/langgraph) — STUDY
- **Mechanism:** A low-level orchestration library ("inspired by Pregel and
  Apache Beam"): a `StateGraph` with developer-written node functions and
  edges; "nodes do the work, edges tell what to do next."
  `add_conditional_edges` calls a routing function (plain code; may call an
  LLM) to pick the next node(s); `Send` spawns nodes with per-invocation
  state; `Command` mixes state updates with `goto` control flow. A
  checkpointer persists exact graph state per `thread_id`; `interrupt()`
  pauses anywhere inside a node and resumes via `Command(resume=...)`.
- **Maturity:** Repo pushed 2026-10-21, 43,668 stars, MIT, active
  (langgraph 1.2.16 mid-Oct 2026). README production claim: "Trusted by
  companies shaping the future of agents – including Klarna, Replit,
  Elastic" — vendor claim in their own README.
- **License:** MIT (repo API).
- **Key questions:** Routing is decided by the graph — static edges or a
  routing function evaluated on the sending side/centrally; "a node becomes
  `active` when it receives a new message (state)" and there is no
  documented way for a node to decline execution once activated. The only
  abstain-like behavior is halting ("nodes with no incoming messages vote
  to `halt`"). Notably, the interrupts guide documents a "Validating human
  input" pattern that loops back via a conditional edge to re-ask "a
  narrower, refined question" — a documented bounce-back-as-
  narrower-question, though aimed at humans, not at refusing
  agent-to-agent work. No built-in logging of routing decisions as such;
  checkpointing gives full state replay.
- **Claim vs reality:** "Durable execution" and resumability are real and
  documented; the docs honestly warn that node re-runs on resume require
  idempotent side effects before `interrupt()`.
- **Tag reason:** LangGraph is the only surveyed framework whose docs
  explicitly codify a loop that bounces work back as a narrower question
  (the deadband's honest outcome), plus checkpointed, resumable,
  inspectable state — concepts adjacent to seam arbitration. But its
  arbitration geometry is inverted relative to beam-quilt: routing is
  graph/sender-decided, nodes cannot refuse incoming edges, and state flows
  through a shared, merged schema rather than typed transmissions between
  sovereign processes. Pull the interrupt-validation-loop and
  checkpoint/resume patterns as design input; do not pull the shared-state
  runtime.
- **Sources:** [repo API](https://api.github.com/repos/langchain-ai/langgraph) ·
  [README](https://raw.githubusercontent.com/langchain-ai/langgraph/main/README.md) ·
  [graph API docs](https://docs.langchain.com/oss/python/langgraph/graph-api) ·
  [interrupts docs](https://docs.langchain.com/oss/python/langgraph/interrupts) ·
  [docs root](https://docs.langchain.com/oss/python/langgraph/)

### CrewAI (crewAIInc/crewAI) — REJECT
- **Mechanism:** Two primitives: Crews (role/goal/backstory agents plus
  tasks) and Flows (event-driven pipelines with `@start`/`@listen`/`@router`
  decorators). Crew process is sequential or hierarchical: "a manager
  agent coordinates the crew, delegating tasks and validating outcomes,"
  with a mandatory `manager_llm`; the manager "allocates tasks among crew
  members based on their roles and capabilities" and "evaluates outcomes."
  Agent-to-agent delegation is a permission toggle — `allow_delegation`,
  "Delegation is now disabled by default."
- **Maturity:** Repo pushed 2026-10-21, 59,963 stars, MIT, active.
  README claims "over 100,000 developers certified via community courses" —
  vendor claim.
- **License:** MIT (repo API).
- **Key questions:** Routing is orchestrator-decided (manager LLM) or
  statically ordered or router-function-decided. The docs describe no
  mechanism for an agent to refuse a delegated task; the receiving-side
  flow is simply not documented. No deadband/abstain analog.
  `step_callback` exists to "monitor and log agent interactions," but no
  replay of routing decisions is documented.
- **Claim vs reality:** "Production-ready" is repeated throughout the
  README, but the hierarchical-process delegation the marketing centers on
  is disabled by default, and the docs do not document the receiving side
  of a delegation at all.
- **Tag reason:** CrewAI's hierarchical mode is precisely the anti-pattern
  beam-quilt defines itself against: a manager process that assigns and
  validates others' work — an orchestrator crew members must obey — with
  zero receiver-side refusal, no comparative admission, and no deadband.
  The role/task decomposition vocabulary is generic; nothing matches the
  sovereignty, edge-hardware, or seam-arbitration constraints specifically
  enough to justify pulling.
- **Sources:** [repo API](https://api.github.com/repos/crewAIInc/crewAI) ·
  [README](https://raw.githubusercontent.com/crewAIInc/crewAI/main/README.md) ·
  [crews docs](https://docs.crewai.com/concepts/crews) ·
  [agents docs](https://docs.crewai.com/concepts/agents) ·
  [hierarchical process](https://docs.crewai.com/learn/hierarchical-process)

### Letta (letta-ai/letta, formerly MemGPT) — ADAPT
- **Mechanism:** Stateful agents whose memory is "a git repository owned by
  the agent, which MemFS projects onto whatever computer the agent is
  working on"; files under `system/` are "in the system prompt every turn,"
  everything else stays out of context, and "the agent edits it as it
  learns" (self-editing memory). A parent agent launches subagents via a
  specialized subagent tool, each "in a new subprocess" — blocking by
  default, or background with a completion notification injected into the
  conversation. Background "dreaming" subagents "review recent
  conversations, consolidate lessons, and update memory without
  interrupting active work." Development has moved to `letta-ai/letta-code`
  (Node/TypeScript; the retired V1 API server lives on an archive branch).
- **Maturity:** letta-ai/letta pushed 2026-10-18, 25,219 stars;
  letta-code active (Apache-2.0, ~3.7k stars per GitHub page) — a mid-
  rewrite project. No production-use claims of its own in the fetched
  README.
- **License:** Apache-2.0 — verified from the letta-ai/letta repo API and
  the letta-code GitHub page.
- **Key questions:** Routing is sender-decided: the parent's LLM decides
  to launch a subagent via a tool call; no refusal mechanism is documented.
  Memory sharing between agents is real but is shared state by design
  (shared git repositories). No deadband/abstain analog; closest is
  dreaming/sleep-time consolidation deferring memory updates. Strong edge
  story: the CLI "runs agents in-process. All agent state… stays on-device,
  and no Letta account is required"; self-hosting runs with no Docker or
  Postgres, local inference via "Ollama, LM Studio, or llama.cpp" (ARM not
  explicitly addressed — UNVERIFIED). Agent memory is an owner-readable
  git history, but routing decisions themselves are not logged as
  transmissions.
- **Claim vs reality:** The classic MemGPT framing (core memory blocks,
  `core_memory_append`-style tools) has quietly become a git-filesystem
  model in current docs; older descriptions circulating online no longer
  match the shipped architecture.
- **Tag reason:** The strongest constraint match in the framework survey
  on the sovereignty/edge side: agents are processes with on-device,
  git-versioned, owner-readable state; subagents are isolated subprocesses;
  sleep-time consolidation is a real analog for deferred background work.
  What it lacks is exactly the seam: launches are sender-decided tool calls
  with no receiver admission, and inter-agent sharing is via common memory
  rather than refusable typed transmissions. Adapt the memory-as-git-repo +
  subprocess isolation + on-device state model; do not adopt its delegation
  model.
- **Sources:** [repo API](https://api.github.com/repos/letta-ai/letta) ·
  [README](https://raw.githubusercontent.com/letta-ai/letta/main/README.md) ·
  [letta-code](https://github.com/letta-ai/letta-code) ·
  [memory docs](https://docs.letta.com/agent-sdk/memory/index.md) ·
  [subagents docs](https://docs.letta.com/configuration/subagents/index.md) ·
  [self-hosting docs](https://docs.letta.com/self-hosting/index.md)

### OpenAI Agents SDK (openai/openai-agents-python) — STUDY
- **Mechanism:** A single-loop multi-agent SDK: agents run under a
  `Runner`; "Handoffs are represented as tools to the LLM," so the sending
  agent's model decides to transfer control by emitting a tool call — with
  `handoff()` as the code-decided exception that always transfers.
  Guardrails are the admission layer: input guardrails "run only for the
  first agent in the chain" (blocking mode means "the agent never executes"
  if the tripwire fires), output guardrails run after the last agent, and
  tool guardrails may "skip the call, replace the output with a message, or
  raise a tripwire." Tracing is "enabled by default," exporting spans to
  the OpenAI backend, with replaceable processors.
- **Maturity:** Repo pushed 2026-10-21, 30,202 stars, MIT; v0.24.0
  (Oct 15, 2026, dated in its changelog). No third-party production claims
  found in the fetched README.
- **License:** MIT (repo API).
- **Key questions:** Routing is sender-decided: the handoff fires when the
  sender's LLM makes the tool call; the receiving agent cannot refuse —
  the only gates are sender-side (`is_enabled`, `on_handoff`
  authorization). The genuine partial analog to beam-quilt's seam is the
  input-guardrail pattern: a cheap, fast classifier screening incoming work
  before an expensive model runs, able to tripwire-refuse and halt
  execution — a receiver-side admission judge, but only at run entry,
  binary, not comparative across competing work, and with no
  deadband/narrow-the-question outcome. Cloud-API-first with
  telemetry-on-by-default to OpenAI's backend — a sovereignty/consent
  inversion for owner-readable logging. Tracing does record handoff and
  guardrail spans, the closest thing to routing-decision logging here.
- **Claim vs reality:** The README markets multi-agent workflows, but the
  docs concede the chain is shallow: input guardrails guard only the first
  agent, output guardrails only the last, because "Handoffs stay within a
  single run" — the guardrail protection marketed as a workflow feature is
  entry/exit only.
- **Tag reason:** Nothing about the SDK's process model fits beam-quilt —
  sender-LLM-decided handoffs, single-run chains, default telemetry to a
  vendor backend. But two ideas deserve study: the guardrail as an
  explicitly-cheap model arbitrating what expensive work gets admitted (the
  cost asymmetry underlying scope distillation), and tool guardrails'
  three-valued outcome (skip / replace / reject), the nearest thing to a
  graded refusal in any surveyed framework.
- **Sources:** [repo API](https://api.github.com/repos/openai/openai-agents-python) ·
  [README](https://raw.githubusercontent.com/openai/openai-agents-python/main/README.md) ·
  [handoffs docs](https://openai.github.io/openai-agents-python/handoffs/) ·
  [guardrails docs](https://openai.github.io/openai-agents-python/guardrails/) ·
  [tracing docs](https://openai.github.io/openai-agents-python/tracing/)

### 3b. Gating prior art (selective prediction → MoE routing)

Headline answer to the brief's key question: **no prior art in any cluster
gives the receiver comparative decision rights with an explicit
ask-a-narrower-question outcome.** Every gate is sender/model-side, every
abstention is terminal or silent fall-through, and the only true third
outcome (Mozannar & Sontag's defer logit) competes against a single
designated expert on the sender's terms. beam-quilt's combination appears
unclaimed by this literature.

### Selective prediction / abstention (Chow 1970; Geifman & El-Yaniv 2017; SQuAD 2.0 / Rajpurkar et al. 2018) — STUDY
- **Mechanism:** Chow (1970) characterizes the error–reject tradeoff and
  "describes an optimum rejection rule" based on posterior confidence.
  Geifman & El-Yaniv (2017) instantiate for DNNs: softmax response
  κ(x) = max_j f(x|j), selection g_θ(x) = 1 iff κ(x) ≥ θ; the SGR
  algorithm binary-searches θ on a held-out labeled set using a tight
  binomial bound so true selective risk exceeds the target with
  probability ≤ δ. SQuAD 2.0 (2018): models predict P(unanswerable) plus an
  answer distribution; at test time they abstain "whenever their predicted
  probability that a question is unanswerable exceeds some threshold,"
  tuned on the dev set.
- **Maturity:** Chow: IEEE Trans. Information Theory 1970 (DOI
  10.1109/TIT.1970.1054406, verified on IBM Research page). Geifman &
  El-Yaniv: NIPS 2017 (papers.nips.cc) + arXiv:1705.08500. SQuAD 2.0: ACL
  2018 + arXiv:1806.03822.
- **License:** Chow n/a (paywalled IEEE); the others arXiv
  nonexclusive-distrib/1.0; SQuAD dataset CC BY-SA 4.0 per the paper.
- **Key questions:** (a) Single-threshold gate in all three. (b) The
  reject region is a band below one threshold — a hard floor, not a
  deadband with a third action; inside it, pure abstain, no question
  refinement. (c) The decider is the model itself (sender-side
  self-assessment); no receiver-side scoring. (d) Nothing turns abstention
  into a narrower question — SQuAD abstention is terminal. (e)
  Calibration: Chow assumes known posteriors; Geifman needs a held-out
  i.i.d. labeled set plus the binomial bound; SQuAD needs a labeled dev
  set.
- **Claim vs reality:** Geifman & El-Yaniv's "guarantee" is a
  distribution-bound over the calibration draw — it silently assumes the
  calibration set is i.i.d. with test; it is marginal, not per-instance.
  The full text also credits softmax-response rejection to Cordella 1995 /
  De Stefano 2000 — the lineage is older and less DNN-specific than
  marketing of the 2017 paper suggests.
- **Tag reason:** The mathematical foundation for beam-quilt's go/no-go
  bands — Chow's cost-model framing plus SGR's risk-controlled threshold
  calibration are exactly the "formula-shaped gate decisions" rule G2
  wants made explicit, and the binomial-bound calibration is pull-able for
  setting band edges cheaply and auditably. But none of it is comparative
  across competing inputs, none has an explicit deadband with a third
  outcome, and the decision right sits with the sender. STUDY, not PULL:
  adopt the calibration math, reject the single-threshold/binary-abstain
  shape.
- **Sources:** [IBM Research page (Chow)](https://research.ibm.com/publications/on-optimum-recognition-error-and-reject-tradeoff) ·
  [arXiv:1705.08500](https://arxiv.org/abs/1705.08500) ·
  [NIPS 2017 page](https://papers.nips.cc/paper/7073-selective-classification-for-deep-neural-networks) ·
  [arXiv:1806.03822](https://arxiv.org/abs/1806.03822)

### Conformal prediction (Vovk et al., ALRW; Angelopoulos & Bates) — ADAPT
- **Mechanism:** Split conformal: fix a nonconformity score, compute scores
  on a held-out calibration set of size n, take the ⌈(n+1)(1−α)⌉/n
  empirical quantile as threshold, and output the set of labels scoring
  below it. The guarantee: the prediction set contains the true label with
  probability ≥ 1−α — distribution-free over the model, but marginal over
  the calibration draw, not per-instance. The (n+1) finite-sample
  correction makes coverage exact; the book version (ALRW) proves validity
  in the online setting under exchangeability.
- **Maturity:** Vovk, Gammerman, Shafer, *Algorithmic Learning in a Random
  World*, Springer, 1st ed. 2005, 2nd ed. Dec 2022 (verified on the
  authors' official site alrw.net). Angelopoulos & Bates tutorial:
  arXiv:2107.07511 (v1 2021, v6 2022) — preprint/tutorial, not
  peer-reviewed.
- **License:** Book: Springer copyright (no open license found on the
  fetched site). Tutorial: arXiv nonexclusive-distrib/1.0.
- **Key questions:** (a) Not comparative — a threshold on one score per
  label, formed independently per label. (b) No explicit deadband, but set
  size > 1 is an honest ambiguity signal — natural raw material for
  beam-quilt's "hold" band; nothing in the framework prescribes an action
  inside the band. (c) Decider is model-side; no receiver role. (d) No
  refinement outcome anywhere in the framework. (e) Requires
  exchangeability of calibration and test data plus a labeled calibration
  set; the book site is explicit that guarantees rest "on data
  exchangeability."
- **Claim vs reality:** "Distribution-free" is true about the model but
  quietly assumes exchangeability — under distribution shift the coverage
  claim silently degrades; coverage is also marginal, so a particular
  receiver's particular input gets no per-instance promise.
- **Tag reason:** Conformal quantiles are the cheapest auditable way to put
  statistically honest edges on beam-quilt's go band and no-go band from a
  small calibration set — a direct G2 fit (formula-shaped band edges,
  spot-checkable). ADAPT rather than PULL because beam-quilt must invert
  the arrangement: the receiver's scores over competing inputs are what
  need banding, the guarantee must survive sender/receiver divergence
  (non-exchangeable across judges — divergence should be logged, which
  conformal explicitly does not model), and the ambiguous middle should
  trigger a narrower question rather than a bigger output set.
- **Sources:** [alrw.net (book site)](https://www.alrw.net/) ·
  [arXiv:2107.07511](https://arxiv.org/abs/2107.07511)

### Classifier cascades / early exit (Viola–Jones 2001; CALM, Schuster et al. 2022) — STUDY
- **Mechanism:** Viola–Jones: a "degenerate decision tree" of boosted
  stages; each subwindow must pass every stage; stage thresholds are
  "adjusted to minimize false negatives" (first stage: 100% detection,
  ~40% false-positive rate with 2 features), so early stages are cheap and
  nearly recall-perfect. CALM: per-token, per-layer exit when confidence
  c_i^t ≥ λ, via softmax response, hidden-state saturation, or a trained
  early-exit classifier; λ is calibrated on an i.i.d. calibration set via
  the Learn then Test risk-control framework (Hoeffding/Hoeffding–Bentkus
  p-values, λ=1 fallback).
- **Maturity:** Viola–Jones: CVPR 2001 (fetched PDF header). CALM:
  arXiv:2207.07061, NeurIPS 2022 Oral (arXiv comments field).
- **License:** Viola–Jones PDF: course-hosted copy, no license shown
  (n/a). CALM: arXiv nonexclusive-distrib/1.0.
- **Key questions:** (a) Viola–Jones: single threshold per stage, two
  outcomes; CALM's softmax measure is comparative (top-1 minus top-2
  margin) but the exit decision is a single threshold on that score.
  (b) No abstain band in either: VJ stages emit pass/reject only; CALM
  tokens failing the check simply "compute the next representation" —
  uncertainty buys more compute, never abstention. (c) Sender-side in both
  (the model allocates its own compute). (d) CALM is the nearest
  structural analogue to "ask a narrower question": unresolved tokens
  recurse to a deeper, more expensive refinement instead of abstaining —
  but refinement is of the model's own compute, not of the question posed.
  (e) VJ: validation-set tuning, later stages trained on earlier false
  positives; CALM: i.i.d. calibration set with LTT statistical guarantees.
- **Claim vs reality:** none found for CALM's mechanics (its risk-control
  calibration is explicit about the i.i.d. assumption); for VJ, the
  "real-time" claim is engineering-conditional and end-to-end recall is
  the product of stage recalls — a compounding cost the original framing
  underplays.
- **Tag reason:** The transferable idea is CALM's
  uncertainty-purchases-refinement loop: work that fails the cheap judge's
  go band is not refused outright but escalated to a narrower/deeper check
  — precisely the deadband semantics beam-quilt wants. But both systems
  keep the decision sender-side and inside one model, with single
  thresholds and no comparative scoring across competing inputs: design
  inspiration for the deadband's interior behavior, not for receiver-side
  arbitration.
- **Sources:** [Viola–Jones PDF](https://www.cs.cmu.edu/~efros/courses/LBMV07/Papers/viola-cvpr-01.pdf) ·
  [arXiv:2207.07061](https://arxiv.org/abs/2207.07061) ·
  [CALM HTML](https://arxiv.org/html/2207.07061v2)

### Learning to defer / learning with rejection (Cortes et al. 2016; Mozannar & Sontag 2020) — ADAPT
- **Mechanism:** Cortes, DeSalvo & Mohri: jointly learn a predictor h and
  a rejection function r with loss L = 1_{y·h(x)≤0}·1_{r(x)>0} + c(x)·1_{r(x)≤0};
  confidence-based rejection is a special case; convex surrogates with
  Rademacher complexity guarantees. Mozannar & Sontag: a single softmax
  over K classes plus a deferral logit ⊥ — cross-entropy over 𝒴∪{⊥} plus a
  term that fires when the expert is correct; inference defers iff
  max_y g_y(x) ≤ g_⊥(x); Theorem 1: the surrogate is convex, upper-bounds
  0-1 system loss, and its minimizer matches the Bayes classifier and
  Bayes rejector.
- **Maturity:** Cortes et al.: author PDF at cs.nyu.edu (fetched full
  text); venue UNVERIFIED by fetch this session (ACM DL 403, Springer
  blocked, Semantic Scholar empty). Mozannar & Sontag: ICML 2020, PMLR
  v119 pp. 7076–7087 (verified on proceedings.mlr.press) +
  arXiv:2006.01862.
- **License:** n/a for both papers (no license shown on fetched pages).
- **Key questions:** (a) Genuinely comparative in Mozannar & Sontag —
  deferral competes with every class inside one softmax; Cortes et al. is
  a learned threshold-function, not comparative. (b) Deferral is an
  explicit third outcome, but a point decision, not a band. (c)
  Sender-side: the classifier holds the decision right and hands off; the
  expert is a designated fallback, never a judge of competing inputs.
  (d) No recursive refinement — one hop to a single expert; multiple
  experts explicitly future work. (e) Cortes: labeled training sample,
  known rejection cost; Mozannar & Sontag: samples of the expert's
  decisions are required to train the deferral logit.
- **Claim vs reality:** The consistency guarantee holds only for α = 1 of
  the weighted variant — the paper itself states α ≠ 1 breaks
  consistency; and both frameworks quietly assume the expert's labels are
  observable i.i.d. samples, unrealistic for expensive receivers.
- **Tag reason:** The closest prior art to beam-quilt's deadband
  semantics: an explicit third outcome (defer) that competes inside a
  single comparative score, with a consistency theorem telling you exactly
  when the comparative decision matches the Bayes-optimal arbitration.
  ADAPT because beam-quilt must (i) generalize the single designated
  expert to N competing senders (the paper defers only to one expert —
  multiple experts is future work), (ii) move the decision right to the
  receiver, and (iii) replace one-hop deferral with the deadband's
  "narrow the question" outcome. The loss shape (cross-entropy +
  expert-correctness term) is a credible starting formula for the
  receiver's comparative score, per G2.
- **Sources:** [Cortes et al. PDF](https://cs.nyu.edu/~mohri/pub/rej.pdf) ·
  [arXiv:2006.01862](https://arxiv.org/abs/2006.01862) ·
  [PMLR v119 page](https://proceedings.mlr.press/v119/mozannar20b.html)

### MoE routing (Switch Transformer; Expert-Choice; Mixtral; Soft MoE) — ADAPT
- **Mechanism:** Switch: router softmax over N experts, top-1; load-
  balancing loss α·N·Σᵢ fᵢ·Pᵢ (α = 10⁻²); expert capacity with overflow
  tokens "passed directly to the next layer through the residual
  connection" (<1% drop rate). Expert-Choice (Zhou et al. 2022): each
  expert picks its top k tokens (c=2); no auxiliary loss, perfect balance
  by construction; a token may get any number of experts including zero.
  Mixtral 8x7B: always exactly 2 of 8 experts, weighted sum, no threshold.
  Soft MoE: no hard routing — dispatch D_ij = softmax over tokens per slot,
  fully differentiable, "immune to token dropping and expert unbalance."
- **Maturity:** Switch: arXiv:2101.03961 + JMLR vol. 23 (2022), article
  120 (verified on jmlr.org). Expert-Choice: arXiv:2202.09368. Mixtral:
  arXiv:2401.04088 (Jan 2024), weights Apache 2.0. Soft MoE:
  arXiv:2308.00951, ICLR 2024.
- **License:** Switch: arXiv nonexclusive-distrib/1.0. Expert-Choice: CC
  BY-NC-SA 4.0 (non-commercial — relevant if importing code). Mixtral:
  paper CC BY 4.0, weights Apache 2.0. Soft MoE: CC BY 4.0.
- **Key questions:** (a) Comparative everywhere hard routing exists —
  softmax over experts, argmax/top-k; MoE routers are the only cluster
  where scoring alternatives and picking is native. (b) No deadband
  anywhere: Switch has no minimum gate probability (top-1 always chosen);
  Mixtral's masking only zeroes non-selected experts; the "bands" are
  capacity/buffer artifacts, not score bands. (c) Strictly sender/router-
  side, decided inside the producing layer; the receiving experts never
  judge the token. (d) Nothing refines the question: Switch overflow
  tokens pass through unchanged via residual; Expert-Choice tokens with
  zero experts "simply pass through with less compute" — a no-expert-wins
  outcome exists (the closest thing to a no-go band in the cluster), but
  it is silent fall-through, not an explicit third outcome. (e) No
  calibration set — routing learned end-to-end; balance enforced by
  auxiliary loss (Switch) or by construction (Expert-Choice, Soft MoE).
- **Claim vs reality:** Switch's "simple and efficient" routing silently
  drops tokens under load (residual pass-through), and the paper's own
  No-Token-Left-Behind rerouting variant "gave no empirical benefit" — the
  arbitration failure mode is real but hidden in throughput numbers. Soft
  MoE "solves" routing by dissolving the discrete decision — honest, but
  it removes exactly the auditable pick beam-quilt needs.
- **Tag reason:** MoE routers are the strongest existing machinery for
  beam-quilt's comparative core: every input is scored against all
  alternatives (softmax), a winner is picked with an auditable,
  formula-shaped rule (top-1/top-2), and divergence-from-uniform is
  explicitly measured and penalized (fᵢ vs Pᵢ load-balancing loss) rather
  than averaged away — a direct template for logging sender/receiver score
  divergence per G3, plus engineered failure semantics (overflow →
  residual passthrough) that beam-quilt can convert from silent drop into
  an explicit deadband outcome. ADAPT, not PULL: the router must be
  re-sided (receiver, not sender layer), the capacity-factor band must
  become a score deadband with "narrow the question" as the in-band
  action, and Soft MoE serves as the counterfactual showing what is lost
  (auditability) when hard picks are softened away.
- **Sources:** [arXiv:2101.03961](https://arxiv.org/abs/2101.03961) ·
  [Switch HTML](https://arxiv.org/html/2101.03961v2) ·
  [JMLR page](https://www.jmlr.org/papers/v23/21-0998.html) ·
  [arXiv:2202.09368](https://arxiv.org/abs/2202.09368) ·
  [arXiv:2401.04088](https://arxiv.org/abs/2401.04088) ·
  [arXiv:2308.00951](https://arxiv.org/abs/2308.00951)

## 4. Event systems, recording, replay

Domain-level finding: the industry has shipped every individual beam-quilt
recording mechanic — multi-channel ring buffers with discard/overwrite policy
(LTTng), per-key lossy compaction with stable addressing (Kafka), named
reader cursors over append-only journals (Chronicle Queue), ring-buffer trace
mode and parallel event-lane UIs (WinDbg TTD), typed cross-process context
envelopes (OpenTelemetry) — but never assembled them into one owner-consented,
fleet-wide scrubbable multi-track record; the event-store products that come
closest (KurrentDB, Axon Server) are drifting toward restricted licenses and
central-cluster assumptions. Equally consistent is the replay boundary: every
honest deterministic-replay system (rr, Undo, TTD) achieves determinism only
by hermetic capture — serializing threads, intercepting every syscall,
excluding GPU/external inputs — which independently validates chrono-flight's
refusal to promise re-execution of LLM-based nodes.

### EventStoreDB (now KurrentDB) — STUDY
- **Mechanism:** An event-sourcing database: one globally ordered append-only
  event log; "streams" are logical views — each append writes to the log plus
  an index entry, so billions of fine-grained streams are virtual tracks over
  one physical log. Appends use optimistic concurrency (expected revision), no
  locks. Retention via per-stream `$maxAge`/`$maxCount`/`TruncateBefore`
  metadata plus a background **scavenge** that physically removes eligible
  events. Projections keep their own checkpoint and ordering streams to
  resume in the same order after restarts. Clustered, gRPC/HTTP access.
- **Maturity:** Repo EventStore/EventStore pushed 2026-10-20, 5,873 stars,
  active; latest release v26.2.3 (via kurrent-io/KurrentDB releases). Docs
  serve v26.0 and 25.2 LTS.
- **License:** **Kurrent License v1** — verified independently for this
  survey from the GitHub license endpoint: source-available; grants
  use/copy/derive but restricts offering it as a hosted/managed service and
  tampering with license-key enforcement. Per their docs, from v24.10 the
  OSS/commercial split was replaced by this license, one binary, license
  keys gating enterprise features.
- **Key questions:** (a) Multi-track: not physical tracks — one interleaved
  global log with indexed virtual streams; no mute/solo, no per-track ring
  buffer. (b) Replay: no re-execution guarantee at all — it is a store; only
  projections' restart ordering is managed. (c) Append-only log + index +
  background scavenge; multi-writer via optimistic concurrency. (d)
  Datacenter-leaning: K8s operator, cloud docs, cluster framing; server
  processes, not an edge library.
- **Claim vs reality:** "Single binary anyone can access and use" is true,
  but encryption-at-rest, Kafka connectors, auto-scavenge, stream policies,
  OAuth/LDAPS, and the OTLP endpoint all require a paid license key — stated
  in the same doc, so candid, but "open" is doing heavy lifting. Note the
  docs domain moved: docs.eventstore.com no longer resolved this session;
  everything lives on docs.kurrent.io.
- **Tag reason:** The one load-bearing idea for chrono-tensor is
  architectural: KurrentDB proves that "many logical tracks" can be an index
  over ONE append-only physical log rather than many buffers — exactly the
  shape of "one scrubbable multi-track stream per fleet." Scavenging by
  stream metadata is the closest shipped analog to lossy
  downmixing-with-addressability. But the system fails the constraints:
  source-available license with hosting restrictions is a sovereignty
  problem fleet-wide; it assumes a central always-on DB cluster, not
  unreliable ARM home nodes; and it offers nothing on owner-consentable
  transmissions or replay honesty. Study the index-over-log and scavenge
  designs; don't adopt the database.
- **Sources:** [repo API](https://api.github.com/repos/EventStore/EventStore) ·
  [license endpoint](https://api.github.com/repos/EventStore/EventStore/license) ·
  [v24.10 what's new](https://docs.kurrent.io/server/v24.10/quick-start/whatsnew) ·
  [concepts](https://docs.kurrent.io/getting-started/concepts) ·
  [streams feature](https://docs.kurrent.io/server/v26.0/features/streams) ·
  [KurrentDB releases](https://github.com/kurrent-io/KurrentDB/releases/latest)

### Axon (AxonFramework + AxonServer) — REJECT
- **Mechanism:** Two parts with sharply divergent licensing. **Axon
  Framework** ("Framework for Evolutionary Message-Driven Microservices on
  the JVM") implements CQRS/event-sourcing patterns — aggregates,
  command/query/event buses — and can bind to Axon Server as its event
  store. **Axon Server** is AxonIQ's purpose-built "system of record" event
  store plus message router; its docs cover clustering, multi-context,
  tiered storage, and gRPC APIs. The framework repo has moved to
  **AxonIQ/axoniq-framework** ("Axoniq Framework," v5.4.x line).
- **Maturity:** Framework repo pushed 2026-10-21, 3,625 stars, Apache-2.0,
  active; latest release v5.4.1. Axon Server development continues (docs
  v2026.1) — but not in public.
- **License:** **Framework: Apache-2.0** (repo API). **Axon Server:
  commercial/proprietary** — both old source locations return 404 (verified
  independently for this survey: github.com/AxonFramework/AxonServer is a
  404 page), and AxonIQ's Terms of Service state Axon Server is licensed
  under AxonIQ plans, 14-day evaluation from first download, then a paid
  subscription priced on throughput capacity.
- **Key questions:** (a) Multi-track: no — concept set is
  aggregates/streams/buses; nothing like parallel scrubbable tracks over
  one timeline. (b) Replay: framework supports event replay into rebuilt
  aggregates (standard event sourcing), but no determinism guarantees for
  handlers; details beyond docs nav structure UNVERIFIED. (c) Storage: Axon
  Server internals closed-source; tiered storage and a config database per
  docs nav; details UNVERIFIED. (d) Server-centric; JVM framework binds to
  a Java service stack.
- **Claim vs reality:** The licensing history: Axon Server was originally
  pitched alongside the open framework, but the source is now gone from
  GitHub and the ToS are unambiguously commercial. "AxonIQ open source"
  messaging today applies to the framework only.
- **Tag reason:** The opposite of beam-quilt's constraints in every
  dimension that matters: the interesting half (the event store/router) is
  closed-source commercial software with capacity-based subscriptions, no
  edge story, a JVM framework binding, and no multi-track or replay-limit
  model — while the open half is opinionated CQRS scaffolding whose value
  is aggregate lifecycle management, not recording. The one cautionary
  lesson is institutional: a "framework stays open, server goes commercial"
  split is exactly the governance risk a sovereign, owner-consented
  architecture must avoid depending on.
- **Sources:** [framework repo API](https://api.github.com/repos/AxonFramework/AxonFramework) ·
  [dead AxonServer URL](https://github.com/AxonFramework/AxonServer) ·
  [axoniq-framework releases](https://github.com/AxonIQ/axoniq-framework/releases/latest) ·
  [docs](https://docs.axoniq.io/) ·
  [terms of service](https://www.axoniq.io/legal/terms-of-service)

### OpenTelemetry (tracing data model) — ADAPT
- **Mechanism:** A vendor-neutral spec + SDKs + Collector for telemetry. A
  **span** carries trace ID, span ID, parent span ID, name, start/end
  timestamps, attributes (typed key-value), timestamped **events**,
  **links** (causal ties across separate traces), status, and kind.
  **Context propagation** serializes span context (plus baggage) across
  process/service boundaries — the mechanism that stitches distributed
  traces together. The **Collector** is a receivers → processors →
  exporters relay that stores nothing. The spec deliberately does not
  define storage, retention, query, or backends.
- **Maturity:** "OpenTelemetry is a CNCF graduated project" (homepage,
  fetched); traces Stable in nearly every language SDK; logs mixed;
  profiles development-only. Spec repo active.
- **License:** Apache-2.0 — verified from the
  open-telemetry/opentelemetry-specification repo LICENSE. (Individual
  repos across the org not each verified.)
- **Key questions:** (a) Multi-track: a trace is a tree of spans rendered
  on one interleaved timeline; parallel sibling spans look like tracks, and
  span kinds/links give causal structure — but there is no session-level
  "16 parallel named tracks with mute/solo" concept. (b) Replay: entirely
  out of scope — the spec defines observation, not re-execution. (c)
  Storage model: undefined by spec; the Collector is explicitly a
  pass-through pipeline. (d) No datacenter assumption: agent mode is
  explicitly for running beside the service; works on edge, but the
  ecosystem's gravity (gateways, backends) is central-cluster.
- **Claim vs reality:** none found — the docs are unusually explicit about
  what the spec does not cover (storage, backends), and the primer even
  warns against interpolating values between measurement points.
- **Tag reason:** The only system here whose core primitive matches
  beam-quilt's sovereignty rule directly: a typed, propagated context that
  makes every cross-process influence explicit and attributable (trace/
  span IDs + links = provenance light; attributes + events = intent/cost
  carriers). Adapting W3C-propagated span context as the envelope for
  typed cross-patch transmissions buys interop with a large tooling
  ecosystem at the edge via agent-mode collectors, without importing any
  storage commitments. What it cannot provide is the recording target
  itself: no multi-track scrubbable stream, no retention, no replay
  semantics — chrono-tensor still has to define all of that. Adapt the
  envelope, not the backend.
- **Sources:** [traces concepts](https://opentelemetry.io/docs/concepts/signals/traces/) ·
  [observability primer](https://opentelemetry.io/docs/concepts/observability-primer/) ·
  [collector](https://opentelemetry.io/docs/collector/) ·
  [status](https://opentelemetry.io/status/) ·
  [spec repo](https://github.com/open-telemetry/opentelemetry-specification)

### Kafka log compaction — STUDY
- **Mechanism:** Topics partition into totally-ordered append-only
  commit-log segments; records keep immutable offsets. Besides time/size
  retention, the **compact** cleanup policy runs background log-cleaner
  threads that recopy tail segments keeping only the latest record per key
  (offsets never change, order preserved, head stays dense); tombstones
  (null payload) delete a key. This gives stream/table duality: the log is
  both the change stream and (after compaction) a per-key state table from
  which consumers rebuild state — the docs cite Samza using this for state
  recovery. Coordination is KRaft (Raft quorum over an internal metadata
  log); ZooKeeper mode is gone in 4.x.
- **Maturity:** kafka.apache.org lists releases through 4.3; ASF project
  with decades of production use implied by its own operations docs.
- **License:** Apache License v2 — stated in the kafka.apache.org page
  footer.
- **Key questions:** (a) Multi-track: partitions are parallel logs, not
  tracks over one timeline; no unified scrub surface across partitions.
  (b) Replay: replay means a consumer re-reading the log; no re-execution
  semantics; ordering guarantee is per-partition only. (c) Storage:
  append-only segments + time/size retention and/or per-key compaction;
  replication via in-sync replicas (non-Byzantine failure model). Not
  single-writer: leaders per partition, followers pull. (d) Datacenter
  assumption: a broker cluster with JVM processes and a 3-or-5 controller
  quorum — a poor fit for unreliable edge nodes.
- **Claim vs reality:** none found — the design doc is precise about
  guarantees and corner cases (stale values before compaction settles,
  unclean leader election tradeoffs documented since 0.11).
- **Tag reason:** Compaction is the best-documented semantics in this
  survey for what beam-quilt calls lossy downmixing: bounded-space
  retention of the latest value per key that (1) preserves every offset as
  a stable address, (2) never reorders, and (3) still lets a fresh consumer
  reconstruct current state from the tail. That is precisely the property
  set a fleet summary track needs (tau-addressable, order-stable,
  rebuildable). Kafka the system is a central-cluster JVM broker — exactly
  the conditions home ARM clusters don't offer. Study the compaction
  contract and encode it in chrono-tensor's downmix; reject the broker.
- **Sources:** [design docs](https://kafka.apache.org/42/design/design/) ·
  [KRaft operations](https://kafka.apache.org/42/operations/kraft/) ·
  [homepage](https://kafka.apache.org/)

### LTTng — ADAPT
- **Mechanism:** Linux tracing stack: kernel tracer (loadable modules),
  userspace library (LTTng-UST), session daemon + controller (LTTng-tools).
  A **tracing session** contains one or more **channels**; each channel
  owns **per-CPU ring buffers** subdivided into sub-buffers, with two
  policies: **discard** (drop new events when full) or **overwrite**
  (flight-recorder: new overwrites oldest). Kernel and userspace tracing
  share one session. Traces are written in **CTF** (Common Trace Format;
  each channel emits one data stream per CPU), with rotation producing
  "trace chunks" (archives), snapshot extraction from flight-recorder
  buffers, and network streaming to a remote relay daemon. Buffer ownership
  is per-user/per-process/per-CPU/per-channel with access control on
  channels.
- **Maturity:** lttng.org/docs v2.14 current stable; mirrors active
  (lttng-modules pushed 2026-09-22, lttng-ust 2026-09-14). 20+ year
  lineage, developed by Efficios.
- **License:** Per-component, verified from LICENSE files: LTTng-modules —
  LGPL-2.1/GPL-2.0 dual (some files MIT); LTTng-UST — LGPL-2.1-only core
  (MIT public headers, GPL-2.0-only control lib); LTTng-tools — LGPLv2.1
  and GPLv2, docs CC BY 4.0.
- **Key questions:** (a) Multi-track: the genuine article — parallel
  channels (and per-CPU streams) inside one session over one
  wall-clock-correlated timeline, kernel and userspace together. No
  built-in mute/solo UI, but channels are individually enabled/disabled.
  (b) Replay: none, and honest about it — docs explicitly warn traces are
  sequences of measurements, not exact reproductions, and warn against
  interpolating values between events. (c) Ring buffers by design, with the
  discard/overwrite choice explicit; rotation to chunk archives;
  snapshot-to-disk from flight-recorder mode; can stream off-box. (d)
  Edge-friendly: Linux-only but embedded-oriented; UST lets unprivileged
  users trace their own processes with per-user buffers; kernel tracing
  needs modules/root.
- **Claim vs reality:** none found — overhead claims and the
  "don't interpolate" caveat are stated in the official docs themselves.
- **Tag reason:** The closest shipped prior art to chrono-tensor's
  recording target: a session object that owns multiple named channels,
  each an independently-enabled ring buffer with an explicit
  discard-vs-overwrite (flight-recorder) policy, serialized to an open
  multi-stream format with rotation and snapshots — effectively
  "multi-track, scrubbable, ring-buffer-GC, per-track downmix policy"
  decades before beam-quilt named those properties. Its consent model
  (per-user/per-process buffer ownership, access-controlled channels,
  tracing your own processes without root) maps almost 1:1 to
  owner-readable influence and edge sovereignty on Linux/ARM. Adapt the
  session/channel/ring-buffer/CTF semantics as chrono-tensor's skeleton;
  leave behind kernel-module instrumentation and the C/C++-centric
  tooling around LLM nodes it knows nothing about.
- **Sources:** [docs v2.14](https://lttng.org/docs/v2.14/) ·
  [docs root + licensing](https://lttng.org/docs/) ·
  [lttng-modules LICENSE](https://raw.githubusercontent.com/lttng/lttng-modules/master/LICENSE) ·
  [lttng-ust LICENSE](https://raw.githubusercontent.com/lttng/lttng-ust/master/LICENSE) ·
  [lttng-modules repo](https://api.github.com/repos/lttng/lttng-modules) ·
  [lttng-ust repo](https://api.github.com/repos/lttng/lttng-ust)

### Chronicle Queue (OpenHFT/Chronicle-Queue) — ADAPT
- **Mechanism:** Broker-less embedded Java journal: append-only sequences
  persisted to memory-mapped `.cq4` files (off-heap, OS page cache as the
  cache), no insert/delete in the middle. Writers use thread-local
  **Appenders** (single-writer is the fast path); readers use lock-less
  read-only **Tailers** that see every message in order, never consume, and
  can be **named** so their position persists across restarts; replay can
  start at any index. Files roll per **cycle** (typically daily), retained
  "forever" by default — cleanup is your job via a StoreFileListener; no
  built-in compaction or ring buffer. NFS/SAN unsupported (needs real
  mmap).
- **Maturity:** Repo pushed 2026-10-18, 3,823 stars, active; CalVer
  releases — latest chronicle-queue-2026.7 (2026-10-06). An Enterprise
  edition (replication, encryption) layers on the same Apache core.
- **License:** Apache-2.0 — verified two ways: repo API metadata and the
  LICENSE file via the license endpoint. (Worth re-verifying at vendoring
  time; OpenHFT licensing has historically been a moving target.)
- **Key questions:** (a) Multi-track: one queue is one interleaved sequence
  per cycle; tracks would be modeled as multiple queues; **named tailers**
  are per-reader cursors — the closest analog to per-view scrub positions,
  and "read without consuming" supports mute/solo-style independent views,
  but there is no session-level multi-track timeline. (b) Replay:
  deterministic re-reading of the journal in order (data replay); zero
  claims about re-executing producers. (c) Append-only, memory-mapped,
  cycle-rolled files; no automatic GC/ring buffer — "stores everything" is
  the design point. (d) No datacenter assumption — an embedded library,
  Linux-tuned; ARM support not stated: UNVERIFIED.
- **Claim vs reality:** Performance claims (sub-microsecond 99%ile,
  "outperforms Kafka by an order of magnitude") are self-reported vendor
  benchmarks in the README — not independently verified. "Stores everything
  to disk" also means unbounded growth unless you build retention
  yourself.
- **Tag reason:** If beam-quilt had a JVM-facing patch store, Chronicle
  Queue would be a credible PULL candidate (permissive license, tiny
  footprint, actively shipped); for a Rust/Python programme it is ADAPT
  three ideas: memory-mapped append-only cycles as patch-local journals
  (P5), named tailers as persistent per-owner scrub cursors (each sovereign
  reader keeps its own position without consuming — very close to
  receiver-may-refuse), and cycle-rolling as the retention seam where
  downmixing/archival hooks in. Its gaps versus chrono-tensor are the flip
  side of its simplicity: one interleaved sequence, no multi-track session,
  no built-in ring-buffer GC, and Java-only.
- **Sources:** [repo](https://github.com/OpenHFT/Chronicle-Queue) ·
  [repo API](https://api.github.com/repos/OpenHFT/Chronicle-Queue) ·
  [releases](https://api.github.com/repos/OpenHFT/Chronicle-Queue/releases/latest) ·
  [license endpoint](https://api.github.com/repos/OpenHFT/Chronicle-Queue/license)

### Mozilla rr (rr-debugger/rr) — STUDY
- **Mechanism:** Linux record/replay debugger for process trees: under
  ptrace, rr records a process tree and captures "all inputs to those
  processes from the kernel" plus nondeterministic CPU effects, then
  replays with identical control flow, memory and registers. Re-execution
  is made deterministic by emulating a single-core machine — **threads are
  serialized onto one core**; shared memory with processes outside the
  recording tree is disabled; **every syscall must be known** to rr or
  recording breaks. Reverse execution is implemented with restartable
  checkpoints (restore an earlier snapshot, run forward again). Chaos mode
  deliberately scrambles scheduling between runs to expose hidden
  nondeterminism.
- **Maturity:** Repo pushed 2026-10-21, 10,711 stars, unarchived; latest
  release 5.10.0 (2026-10-15); originated at Mozilla, now sponsored by
  Pernosco. Requires kernel ≥ 4.7; Intel/AMD/some AArch64 (Neoverse N1,
  Apple M-series).
- **License:** MIT for the core, but the repo is a multi-license
  combination (MIT-style core with Mozilla/VMware copyright headers, plus
  GPLv2 third-party bits) — GitHub classifies it "Other"; verified via the
  license endpoint. Net: usable, but the combined file must be read before
  vendoring.
- **Key questions:** (a) Multi-track: single trace per recording; gdb lets
  you switch among recorded processes/threads within one tree — one
  interleaved event stream, not parallel named tracks. (b) Replay
  requirements, precisely: hermetic capture of every kernel input;
  single-core serialization; no foreign shared memory; all syscalls known;
  specific CPU families. Specifics of rdtsc/GPU handling: UNVERIFIED this
  session. (c) Durable, portable on-disk traces plus periodic checkpoints
  for fast backward seeks. (d) Assumes a developer workstation with
  x86/Apple-Silicon-class CPU — not a fleet/edge service.
- **Claim vs reality:** none found — rr's docs are the gold standard for
  stating costs up front (~1.2× slowdown on Firefox test suites,
  serialization, syscall coverage).
- **Tag reason:** rr matters here as the rigorous articulation of the
  replay limit beam-quilt embraces rather than promises: deterministic
  re-execution is purchasable only by hermetically sealing the system —
  serializing parallelism, banning outside shared memory, intercepting
  every syscall, pinning CPU families. LLM inference over network APIs is
  exactly the kind of uncapturable outside input this boundary excludes —
  empirical support for chrono-flight's "no deterministic re-execution of
  LLM nodes" honesty. Nothing here is pullable into an agent runtime
  (ptrace, gdb, single-process trees): study the failure taxonomy and the
  checkpoint-and-replay-forward trick for scrubbing, then design the
  black-box recorder without rr's hermetic ambitions.
- **Sources:** [rr-project.org](https://rr-project.org/) ·
  [repo](https://github.com/rr-debugger/rr) ·
  [FAQ](https://github.com/rr-debugger/rr/wiki/FAQ) ·
  [license endpoint](https://api.github.com/repos/rr-debugger/rr/license) ·
  [releases](https://github.com/rr-debugger/rr/releases/latest)

### Undo (UndoDB / LiveRecorder) — REJECT
- **Mechanism:** Commercial reversible debugging: LiveRecorder records a
  Linux process's execution into a self-contained, portable recording; UDB
  replays deterministically, stepping backward/forward and reconstructing
  full process state at any point. The disclosed mechanism: dynamic JIT
  instrumentation records **only nondeterministic inputs**, with ~99% of
  program state reconstructed on demand; memory footprint ~2× the process
  via OS copy-on-write; ~1.5–5× per-thread slowdown. Docs add Snapshots,
  an Event log, and explicit Limitations sections; multiple communicating
  processes can be recorded and debugged together. An "Undo AI" MCP server
  lets coding agents query recordings.
- **Maturity:** Actively maintained commercial docs (Suite 10.2.0);
  homepage names production customers (SAP, AMD, Synopsys, Cadence,
  Siemens, Juniper, Palo Alto Networks) — vendor's own claims, no repo to
  audit.
- **License:** **Commercial only** — per the undo.io homepage (free trials,
  EULA; no open-source offering indicated). Exact license text not fetched:
  UNVERIFIED beyond "commercial."
- **Key questions:** (a) Multi-track: per-process recordings; multi-process
  recording exists (parallel per-process histories debugged together), but
  no shared scrubbable multi-track stream concept. (b) Replay:
  deterministic by construction — record nondeterminism only, reconstruct
  the rest; documented hard stop: **GPU-executed code cannot be recorded**
  (only the CPU side of GPU interactions) — the exact analog of
  beam-quilt's replay limit at the hardware boundary. (c) Storage:
  recording files with snapshots and an event log; internal format
  UNVERIFIED. (d) Linux-only, x86/x86_64/ARM64; no root or kernel modules
  — embedded-friendly in principle.
- **Claim vs reality:** Homepage marketing ("100x faster root cause
  analysis") is backed only by a self-run token-cost benchmark; capability
  claims are internally consistent with the disclosed mechanism, but
  nothing is independently auditable — closed source.
- **Tag reason:** UndoDB is polished, production proof that "record only
  the nondeterminism, reconstruct state on demand" scales commercially — a
  mechanism lesson beam-quilt can cite — but as an artifact it violates
  the constraints outright: closed-source commercial licensing (no
  sovereignty, no owner audit), per-process scope rather than a fleet-wide
  typed stream, no owner-consent model, and 2×-memory/5×-slowdown
  recording. Since rr already documents the same determinism theory openly,
  there is no unique pullable value; take the GPU-boundary honesty and the
  snapshot/event-log recording structure as design input, and reject the
  product.
- **Sources:** [undo.io](https://undo.io) ·
  [docs.undo.io](https://docs.undo.io)

### WinDbg Time Travel Debugging (TTD) — STUDY
- **Mechanism:** Microsoft's user-mode record/replay: TTD injects into a
  Windows process and records full instruction-level execution into a
  `.run` trace file encoding "less than one byte/instruction on average";
  replay is read-only with backward/forward stepping and backward
  breakpoints. On open, an `.idx` index (~2× trace size, built from
  automatic **keyframes**) is generated for fast random access; traces are
  queryable via LINQ over debugger data-model objects, and a **Timelines**
  UI renders parallel event lanes (breakpoints, memory writes, calls/
  returns, exceptions) over the recording. Recording modes: launch, attach
  (admin rights), and `-monitor` (a driver auto-records every start of a
  named binary until reboot).
- **Maturity:** Microsoft Learn docs actively maintained (TTD.exe page
  dated 2026-06-11); distributed as MSIX bundles for x64, x86, and ARM64;
  TTD.exe supports Windows Server 2016–2025.
- **License:** Proprietary Microsoft — evidenced by the EULA acceptance
  flow and copyright banners; formal terms not fetched: UNVERIFIED beyond
  "proprietary, EULA-gated."
- **Key questions:** (a) Multi-track: the Timelines feature is a true
  multi-track scrub surface — distinct parallel lanes over one process
  timeline — but per single process; `-children` records a process tree
  into **separate .run files**, one per process (a
  fleet-of-sovereign-recordings shape, though WinDbg opens only one at a
  time). (b) Replay: full deterministic re-execution of the recorded
  user-mode process; hard limits documented: user-mode only, no kernel,
  Protected Process Light unrecordable, cannot detach once attached.
  (c) Storage: append-style growing `.run` **plus an explicit ring-buffer
  mode** (`-ring` + `-maxFile`, default 2 GB, max 32 GB, keeps only the
  last portion) — a shipped flight-recorder GC; index files are
  regenerable side-cars. (d) Windows-only, admin-required, 5×–20×+
  recorded-process slowdown — desktop/dev-machine conditions, not edge,
  though traces are portable across CPU types.
- **Claim vs reality:** Small but real: the overview says TTD "is efficient
  and works to add as little overhead as possible," while the TTD.exe page
  states plainly that recording is invasive at 5×–20×+ and traces grow to
  gigabytes in minutes — the honest numbers live one page deeper than the
  pitch.
- **Tag reason:** TTD is prior art for two beam-quilt mechanics worth
  copying exactly: a first-class **ring-buffer recording mode** with
  bounded size that keeps only the tail (chrono-tensor's ring-buffer GC,
  shipped), and **per-process separate trace files** for a recorded
  process tree (sovereignty-shaped storage, even if undeveloped). Its
  Timelines UI — mute/solo-like parallel event lanes — is the closest
  existing UX to chrono-flight's scrub/mute/solo/branch. Everything else
  is disqualifying: proprietary Windows-only tooling, admin elevation,
  invasive injection, 5–20× overhead, and full-determinism ambitions
  beam-quilt explicitly declines. The docs' own PII warning ("recordings
  may contain personally identifiable or security related information") is
  the best consent-rule C2 precedent in this survey.
- **Sources:** [TTD overview](https://learn.microsoft.com/en-us/windows-hardware/drivers/debugger/time-travel-debugging-overview) ·
  [TTD record](https://learn.microsoft.com/en-us/windows-hardware/drivers/debugger/time-travel-debugging-record) ·
  [TTD.exe reference](https://learn.microsoft.com/en-us/windows-hardware/drivers/debugger/time-travel-debugging-ttd-exe-command-line-util)

## 5. Question decomposition and cross-model representation

Latent-seam headline: **passing rich internal representations between
genuinely different models is NOT established.** Every positive stitching
result required a small but trained translation layer fit on paired
activations from the same task-distribution data run through both networks
(ImageNet-scale in Lenc & Vedaldi; task train sets with 5K–25K+ samples in
Bansal et al.), identity transfer fails catastrophically (top-1 error >99%),
cross-architecture stitching is admitted to be "tricky," and no LLM
demonstration exists in any of the verified papers. For beam-quilt, typed
textual payloads at the seam are the only evidence-backed design; learned
latent translators remain a calibration-data-dependent research bet — which
is exactly why PROGRAM-MAP parks "uncalibrated latent seams."

### 5a. Question decomposition (scope-distillation prior art)

### Least-to-most prompting (Zhou et al., arXiv:2205.10625) — STUDY
- **Mechanism:** The same LLM does everything, in two prompted stages.
  Stage 1: a decomposition prompt with constant exemplars turns the
  question into an ordered list of subquestions. Stage 2: subquestions are
  answered sequentially, each prompt carrying prior subquestion/answer
  pairs as context; the original question is appended as the final
  subproblem. Decomposition is free-form generated text — no typed schema,
  no search or backtracking, each subproblem solved once in order.
- **Maturity:** ICLR 2023 (arXiv comments field); v1 May 2022, v3 Apr
  2023. No official code repo listed on the arXiv page.
- **License:** n/a for the paper (no license note on the arXiv page).
- **Key questions:** Who decomposes: the solver LLM itself, few-shot.
  Typed/fixed-angle: no — exemplar-defined, free-form. Search/backtracking:
  none. Stop condition: none explicit — granularity is implicitly set by
  prompt exemplars. Headline result: 99%+ on SCAN (incl. length split)
  with 14 exemplars vs 16% for chain-of-thought, on GPT-3
  code-davinci-002.
- **Claim vs reality:** Popularly folded into "chain-of-thought
  improvements"; the authors themselves report that decomposition prompts
  "typically don't generalize well across different domains" and that
  decomposition is the bottleneck (nearly every GSM8K failure was solvable
  with a manual decomposition).
- **Tag reason:** Direct prior art for scope distillation in that it
  demonstrates narrow subquestions raise reliability dramatically (the
  SCAN result is the cleanest "narrow scope beats CoT" datapoint). But it
  is the structural opposite of beam-quilt on two axes: decomposition is
  free-form and per-domain (the authors say prompts don't transfer), and
  there is no termination criterion for "subquestion small enough." Study
  it for the easy-to-hard evidence and the failure taxonomy (error
  propagation through the sequential chain); beam-quilt's fixed typed
  angles and small-judge stop condition are precisely the gaps this paper
  leaves open.
- **Sources:** [arXiv:2205.10625](https://arxiv.org/abs/2205.10625) ·
  [HTML](https://ar5iv.labs.arxiv.org/html/2205.10625)

### Decomposed prompting / DecomP (Khot et al., arXiv:2210.02406) — ADAPT
- **Mechanism:** A decomposer LLM emits a structured "prompting program" —
  tuples ((f1, Q1, A1), ..., (fk, Qk, Ak)) — where each sub-query is
  routed to a NAMED sub-task handler fi from an auxiliary function
  library. Handlers can be few-shot LLM prompts, recursively decomposed
  prompts, or symbolic tools (e.g., Elasticsearch retrieval); control
  operators (foreach, project_values) and back-references exist.
  Sub-questions are generated iteratively from prior answers
  (lazy/incremental), contrasted by the authors with least-to-most's
  one-shot identification of all subproblems.
- **Maturity:** ICLR 2023 camera-ready (arXiv comments field); v1 Oct
  2022, v2 Apr 2023. Official code: github.com/allenai/DecomP (Apache-2.0,
  Python 3.8 + OpenAI API key).
- **License:** Paper CC BY 4.0 (arXiv); repo Apache-2.0 (GitHub sidebar).
- **Key questions:** Typed vs free-form: typed/structured — handler names
  are a de facto type system binding each sub-question to a capability,
  the closest published analogue to beam-quilt's typed angles and typed
  payloads. Stop condition: recursive decomposition to a base case (list
  reversal splits until length ~3, where CoT alone is accurate) — an
  explicit "small enough" rule, though per-task hand-designed.
  Backtracking: none; iteration is forward. Limitations: weaker models
  degrade to "close to zero"; only large models "are reliably able to
  produce the required structured outputs."
- **Claim vs reality:** An ID trap worth recording: the commonly cited ID
  2210.02435 is an unrelated software-defect-prediction paper (fetched to
  check); DecomP is arXiv:2210.02406. The nickname "eager decomposition"
  appears in commentary but not in the paper or repo — UNVERIFIED
  terminology; the paper's own contrast is iterative vs one-shot.
- **Tag reason:** The single closest prior-art match to beam-quilt's scope
  distillation: typed decomposition with named handlers mirrors fixed
  typed angles dispatching to patches, recursive decomposition-to-base-case
  is exactly the "narrow enough for a small judge" stopping rule
  beam-quilt needs, and swapping LLM handlers for symbolic ones is the
  seam abstraction sovereign processes want. The honest adaptation cost:
  DecomP's reliability rests on very large models producing the structured
  programs, while small models fail at exactly that — beam-quilt's bet is
  inverting this (fixed typed angles remove the need for the decomposer to
  be smart, since the type system is static rather than generated).
- **Sources:** [arXiv:2210.02406](https://arxiv.org/abs/2210.02406) ·
  [HTML](https://ar5iv.labs.arxiv.org/html/2210.02406) ·
  [DecomP repo](https://github.com/allenai/DecomP) ·
  [negative ID check](https://arxiv.org/abs/2210.02435)

### Tree of Thoughts (Yao et al., arXiv:2305.10601) — STUDY
- **Mechanism:** A single pretrained LM, prompted into three roles: thought
  generator (i.i.d. sampling or a "propose" prompt), evaluator (scalar
  value 1–10, sure/likely/impossible, or voting), and search controller
  (BFS keeping b best states per step, or DFS with pruning and
  backtracking). Thoughts are coherent intermediate units sized per task
  by the human designer. No training; stopping is by hand-set limits.
- **Maturity:** NeurIPS 2023 camera-ready (arXiv comments field). Official
  repo github.com/princeton-nlp/tree-of-thought-llm — MIT, 6.1k stars,
  GPT-4 via API.
- **License:** Repo MIT (GitHub sidebar).
- **Key questions:** Who decomposes: the same LM, per-task prompted.
  Typed/fixed: free-form, task-specific schema designed by humans.
  Search/backtracking: yes — this entry's differentiator. Stop condition:
  fixed depth/step limits plus LM value judgments; the "small enough to
  sample, big enough to evaluate" thought-sizing rule is the closest prior
  statement of beam-quilt's narrowness condition but is a design heuristic,
  not a runtime check. Cost (author-reported): ToT "might not be
  necessary" for tasks GPT-4 already solves; GPT-3.5 ToT reaches only 19%
  (generation is the bottleneck); the evaluator sometimes prunes correct
  solutions.
- **Claim vs reality:** Popularly "GPT-4 jumps to 74% on Game of 24"
  implies a capability unlock; the paper itself frames it as a search
  scaffold whose gains are confined to tasks needing exploration, at
  5–100× more generated tokens than chain-of-thought.
- **Tag reason:** The counter-example pole: search/backtracking over
  free-form thoughts buys accuracy by spending 5–100× tokens on a frontier
  model — the opposite trade of scope distillation, which buys reliability
  by shrinking the question until a 6.8M-param judge suffices. Two things
  are worth studying: its explicit thought-sizing principle is the
  best-articulated prior statement of the narrowness condition, and its
  DFS-pruning/backtracking semantics may map onto patch orchestration when
  a sub-scope fails. Its per-task human prompt engineering and GPT-4
  dependency are disqualifiers for direct use on edge hardware.
- **Sources:** [arXiv:2305.10601](https://arxiv.org/abs/2305.10601) ·
  [HTML](https://ar5iv.labs.arxiv.org/html/2305.10601) ·
  [repo](https://github.com/princeton-nlp/tree-of-thought-llm)

### 5b. Model stitching / representation alignment (the latent seam question)

### Lenc & Vedaldi stitching layers (arXiv:1411.5908, CVPR 2015) — STUDY
- **Mechanism:** Origin of model stitching. A learned map E between
  intermediate representations of two frozen CNNs, "a bank of linear
  filters" (plus resampling if spatial dims differ), trained by
  backpropagating the reference task loss (ILSVRC12 classification)
  through the frozen halves — only E trains. Compatibility is declared
  when the "Franken-CNN" (bottom of net 1 → stitch → top of net 2)
  performs near the original. Crucially, identity stitching fails with
  top-1 error >99%: raw features of differently parameterized nets are
  NOT directly interchangeable.
- **Maturity:** CVPR 2015, pp. 991–999 (verified on the CVF open-access
  page); arXiv v1 Nov 2014, v2 Jun 2015. No code listed on the arXiv
  page.
- **License:** n/a (no license note on arXiv page).
- **Key questions:** (a) Calibration data: paired activations from the
  SAME inputs — the ILSVRC12 training set pushed through both frozen
  networks, supervised by the frozen top network's loss; ImageNet-scale,
  not a handful of samples. (b) Across architectures/runs: same
  architecture trained separately on ImageNet, Places, and mixes —
  different training runs/datasets, same architecture; no
  cross-architecture pairs. (c) Language models: none — CNNs only.
  (d) Findings: "Conv1 and Conv2 are interchangeable in all cases"; deeper
  layers are task-specific.
- **Claim vs reality:** Often cited under the title "…similarity and their
  compatibility"; the current arXiv page and the CVPR paper are titled
  "…Equivariance and Equivalence." And stitching here is a measurement
  instrument for equivalence, never proposed as a deployment mechanism for
  passing features between systems.
- **Tag reason:** The foundational datum for the latent seam question, and
  it is a caution: the very first stitching result already shows that
  internal representations do NOT pass usefully without a learned
  translator, and that the translator needs full task-distribution data
  fit through both networks. Its layer-granularity finding (early layers
  generic, deep layers task-specific) predicts where a latent seam between
  heterogeneous models would be most and least plausible.
- **Sources:** [arXiv:1411.5908](https://arxiv.org/abs/1411.5908) ·
  [HTML](https://ar5iv.labs.arxiv.org/html/1411.5908) ·
  [CVF open access](https://openaccess.thecvf.com/content_cvpr_2015/html/Lenc_Understanding_Image_Representations_2015_CVPR_paper.html)

### Revisiting Model Stitching (Bansal, Nakkiran, Barak, arXiv:2106.07682, NeurIPS 2021) — STUDY
- **Mechanism:** Formalizes stitching as a representation-comparison tool:
  connect net A's bottom layers to net B's top layers via a deliberately
  low-capacity trainable layer — 1×1 conv with BatchNorm for CNNs, a
  token-wise linear 768×768 map for transformers — fit on the downstream
  task's train set, penalty estimated on test. Kernel-size ablation barely
  matters; the design intent is that "stitching is not learning."
- **Maturity:** NeurIPS 2021 (verified on proceedings.neurips.cc); v1 Jun
  2021. No code repo listed on the arXiv page.
- **License:** n/a (no license note on arXiv page).
- **Key questions:** (a) Calibration data: the task's own training set run
  through network A (sample-scaling experiments at 5K/10K/25K; CIFAR/
  ImageNet scale) — paired same-distribution activations, thousands of
  samples minimum, plus a test split. (b) Across architectures/runs: same
  architecture different seeds (ResNet-18/164, Myrtle CNN, ViT) — stitch
  at all layers with ~3% error penalty; different objectives (supervised
  vs SimCLR/SwAV/DINO) — up to ~5%; width mismatch works asymmetrically;
  cross-architecture is admitted to be "tricky and requires a careful
  choice of the stitching family" (authors' stated limitation). (c)
  Language models: none — one ViT-on-CIFAR-5m experiment is the only
  transformer; the authors explicitly leave NLP comparison as future work.
  (d) Headline: "stitching connectivity" — SGD minima are mutually
  stitchable — plus stitching exposing things CKA cannot.
- **Claim vs reality:** Often summarized as "halves of different neural
  networks can be combined" — the fine print is that the pairs are
  overwhelmingly same-architecture vision models, the stitch layer must be
  trained on the task distribution, and the authors' own conclusion flags
  compute cost and cross-architecture fragility.
- **Tag reason:** The strongest existence proof relevant to the latent
  seam — trained low-capacity maps can make independently trained networks
  interoperate at activation level with small loss, i.e., latent seams are
  not absurd in principle. But every load-bearing condition cuts against
  beam-quilt's setting: calibration data from the task distribution fed
  through both networks, demonstrated within architecture families in
  vision, never on LLMs. This is the reference result defining exactly
  what a latent seam would cost (a trained translator plus paired
  activation data) — which is why typed textual payloads remain the
  evidence-backed default.
- **Sources:** [arXiv:2106.07682](https://arxiv.org/abs/2106.07682) ·
  [HTML](https://ar5iv.labs.arxiv.org/html/2106.07682) ·
  [NeurIPS proceedings](https://proceedings.neurips.cc/paper/2021/hash/01ded4259d101feb739b06c399e9cd9c-Abstract.html)

### "Model Stitching and Fusion" (Musgrave, Belongie, Lim) — REJECT (UNVERIFIED — could not locate)
- **Mechanism / Maturity / License / Key questions:** UNVERIFIED. Could not
  be located: five distinct searches found no arXiv, OpenReview, or
  proceedings entry with this title by these authors; Kevin Musgrave's
  personal site (fetched) lists only other papers; his GitHub profile
  shows no stitching/fusion repo; the Semantic Scholar API returned HTTP
  429 twice. The brief's description of a "critical/deflationary result"
  about what actually makes stitching work is therefore NOT repeated here
  as fact. Closest verifiable adjacent line: Hernandez, Dangovski, Lu,
  Soljacic, "Model Stitching: Looking For Functional Similarity Between
  Representations" (arXiv:2303.11277), which extends stitching across
  different architectures/shapes and reports an asymmetry — conv stitching
  succeeds when the sender's stitch point is later than the receiver's —
  rather than a blanket positive or negative. Search also surfaced leads
  (NOT fetched, not sources): "Model Stitching by Functional Latent
  Alignment" (arXiv:2505.20142) and "Revisiting Model Stitching In the
  Foundation Model Era" (arXiv:2603.12433).
- **Claim vs reality:** The gap is between the survey commission's
  confident description and the failure to verify the paper exists under
  that title/author list. Possible explanations (different title,
  venue-only release, withdrawn) are themselves UNVERIFIED.
- **Tag reason:** Tagged REJECT on verification discipline, not merit: no
  claim enters the design record without a fetched primary source. If the
  deflationary result exists it matters (it would directly temper
  Bansal-era stitching optimism for latent seams), so the action item is
  to re-locate it — likely from the authors' institutional pages or
  OpenReview author search — before allowing it to influence the seam
  design.
- **Sources (attempted/fetched):** [kevinmusgrave.com](https://kevinmusgrave.com/) ·
  [GitHub profile](https://github.com/KevinMusgrave) ·
  adjacent verified: [arXiv:2303.11277](https://arxiv.org/abs/2303.11277)

### The Platonic Representation Hypothesis (Huh, Cheung, Wang, Isola, arXiv:2405.07987) — STUDY
- **Mechanism:** A position paper arguing that representations across AI
  models — different objectives, data, modalities — are converging toward
  "a shared statistical model of reality." Evidence is kernel-level:
  larger/better vision and language models assign increasingly similar
  pairwise distance structure to corresponding data points. Proposed
  driver: scaling plus task generality shrinks the space of viable
  representations.
- **Maturity:** ICML 2024 position paper (project page; the arXiv comments
  field lists only the project page, not a venue). Code:
  github.com/minyoungg/platonic-rep.
- **License:** Repo shows NO license file — reuse terms unclear. Paper n/a
  beyond arXiv listing.
- **Key questions:** Speaks to the seam question only indirectly: it
  argues different models' representation GEOMETRY (similarity structure
  over the same data) aligns with scale — it does not claim
  coordinate-level interchangeability or that one model's activations can
  be consumed by another. Closest-miss lesson on merging: task arithmetic
  (Ilharco et al., arXiv:2212.04089, ICLR 2023) composes "task vectors"
  (fine-tuned minus pretrained weights) — but the vectors come from the
  SAME pretrained base, i.e., merging presupposes an existing shared
  parameterization, precisely what two different patch models would not
  share.
- **Claim vs reality:** Popularly compressed to "all models learn the same
  representation"; the authors' own caveats: text cannot capture all image
  details, the PMI theory "is a starting point," and they themselves raise
  counterarguments (modality-unique knowledge, specialist systems that
  don't need world models).
- **Tag reason:** The right intellectual frame for the latent seam
  question — and an honest one: different models are becoming statistically
  aligned in how they relate data points, which is necessary-but-not-
  sufficient for passing latents. Combined with Lenc & Vedaldi's
  identity-stitch failure and Bansal's trained-translator requirement, it
  supports hoping that low-capacity translators between heterogeneous
  models might get cheaper as models converge, while giving zero
  demonstration that a representation from model A is consumable by model
  B without calibration.
- **Sources:** [arXiv:2405.07987](https://arxiv.org/abs/2405.07987) ·
  [project page](https://phillipi.github.io/prh/) ·
  [repo](https://github.com/minyoungg/platonic-rep) ·
  [task arithmetic](https://arxiv.org/abs/2212.04089)

## Pull list

The short list of things genuinely worth pulling — each with what it would
replace in our designs. Everything else above is either STUDY (read the docs,
steal the idea, skip the artifact) or REJECT.

1. **PULL — BEAM process & supervision semantics** (Erlang/OTP,
   Apache-2.0; §2). *Replaces:* continuum-runtime inventing patch lifecycle
   and failure semantics from scratch, and SPEC P4/P6 needing a reference
   implementation strategy. Take whole: per-process heaps with copied
   messages, selective-receive mailboxes (a patch's inbox is the natural
   seam-arbitration chokepoint), supervision trees with MaxR/MaxT
   escalation, monitors/links, two-generation hot code swap. Explicitly do
   NOT take: distributed Erlang's full-mesh, cookie-trust,
   kill-the-loser-`global` layer — it assumes the uniform-trust cluster
   edge hardware is not.

2. **ADAPT — LTTng session/channel/ring-buffer semantics** (LGPL/GPL
   per-component, docs CC BY 4.0; §4). *Replaces:* chrono-tensor's
   from-scratch model of "one scrubbable multi-track stream." Session =
   fleet stream; kappa track = channel (independently enabled/disabled =
   mute/solo); per-channel discard-vs-overwrite policy = our ring-buffer
   GC choice made explicit; rotation/chunk archives + snapshot mode =
   downmix extraction; per-user/per-process buffer ownership = the shipped
   analog of owner-consented recording. Rebuild the surface; keep the
   semantics.

3. **ADAPT — Kafka's log-compaction contract** (Apache-2.0 docs; §4).
   *Replaces:* chrono-tensor's downmixing rule, currently named but not
   specified. Encode the compaction properties as the downmix contract:
   addresses (tau) never change, order preserved, latest-value-per-key,
   tombstones for deletion, current state rebuildable from the tail alone.

4. **ADAPT — OpenTelemetry span context / W3C context propagation**
   (Apache-2.0; §4). *Replaces:* a bespoke provenance encoding inside
   quilt-engine's TransmissionEnvelope. Trace/span IDs + links are
   provenance-light; attributes/events carry intent and cost; propagation
   formats give interop with existing edge tooling. Adapt the envelope,
   not the backend (OTel defines no storage, no replay, no tracks).

5. **ADAPT — Chronicle Queue's named tailers and mmap append-only cycles**
   (Apache-2.0; §4). *Replaces:* P5 patch-local log mechanics and
   chrono-flight's per-view playback cursors. Named, persistent,
   read-without-consume tailers are exactly "each sovereign reader keeps
   its own scrub position"; cycle-rolling is the retention seam where
   downmix/archive hooks in. Rebuild in Rust (continuum-runtime) rather
   than vendoring Java.

6. **ADAPT — conformal quantiles + SGR binomial-bound calibration**
   (arXiv:1705.08500, arXiv:2107.07511; §3b). *Replaces:* hand-set go/no-go
   thresholds in JevPair with formula-shaped band edges set from a small
   calibration set — a direct G2 fit with an auditable statistical story.
   Required inversions: band the *receiver's* scores, not a model's
   self-assessment; treat sender/receiver divergence as
   non-exchangeability to be logged, not noise to be absorbed; set-size
   growth becomes "ask a narrower question," not a bigger output set.

7. **ADAPT — Mozannar & Sontag's K+1 comparative-defer loss + Switch
   Transformer's fᵢ-vs-Pᵢ divergence logging** (arXiv:2006.01862,
   arXiv:2101.03961; §3b). *Replaces:* an ad-hoc receiver score and the
   unspecified divergence-logging step of seam handshake §4. The
   cross-entropy-over-classes-∪-{⊥} shape is the credible starting
   formula for the receiver's comparative pick; Switch's
   dispatched-fraction vs mean-probability term is the template for
   logging divergence-from-uniform per G3. Our extension (unclaimed in the
   literature): N competing senders, receiver-side decision rights, and a
   deadband whose in-band action is scope distillation.

8. **ADAPT — Temporal's position-matched command/event replay discipline**
   (docs, MIT platform we won't run; §2). *Replaces:* any naive "replay"
   notion in chrono-flight/continuum-runtime. Log transmissions so that
   re-derivation is positionally checkable; treat every model call as an
   activity — recorded, never re-executed; detect drift as a positional
   mismatch rather than pretending determinism. This *enforces* the
   replay limit instead of contradicting it.

9. **ADAPT — DecomP's typed decomposition with a base-case stop rule**
   (arXiv:2210.02406, code Apache-2.0; §5a). *Replaces:* quilt-engine's
   TreeSplitter designing decomposition semantics from scratch. Keep the
   named-handler type system and the decompose-until-small-enough
   recursion; invert the bet — our typed angles are static (fixed
   schema), not LLM-generated programs, so the decomposer needn't be the
   smartest model in the fleet.

Smaller, still-worth-taking patterns (mechanisms, not systems): Letta's
memory-as-git-repo (owner-readable, versioned patch state; §3a); Petals'
blacklist → reroute → regenerate-caches sequence plus Hivemind/SWARM's
"rebalance the topology around the departed node" (the two proven churn
answers; §1); WinDbg TTD's ring-recording mode, keyframe index, and
Timelines lanes (chrono-flight UI precedent; §4); rr's cost-up-front
documentation style as the honesty bar for our own claims (§4).

### Build our own (closest misses and what they teach)

- **The seam itself.** Receiver-side comparative admission with a deadband
  whose honest outcome is a narrower question is unclaimed across agent
  frameworks AND the gating literature (§3 synthesis). Closest misses:
  defer-logit (third outcome, but single designated expert, sender-side),
  CALM (refinement, but inside one model's compute), LangGraph's
  interrupt-validation loop (narrower question, but human-facing and
  graph-decided), OpenAI guardrails (cheap admission, but binary and
  entry-only). The misses triangulate the design: keep defer's comparative
  first-class third outcome, CALM's refinement-not-abstain, the loop's
  narrower-question semantics — and re-side all of it to the receiver.
- **The fleet flight recorder.** Every mechanic exists somewhere (§4) but
  never assembled owner-consented and fleet-wide. Assemble from adapted
  parts (items 2–5 above); the assembly — consent boundaries, per-patch
  logs into one multi-track fleet stream, playback without re-execution
  promises — is ours.
- **Latent seams between different models: keep parked.** Stitching works
  only with a trained low-capacity translator fit on paired activations
  from the task distribution run through both networks (5K–25K+ samples in
  the kindest, same-architecture, vision-only results; no LLM
  demonstration anywhere; identity transfer fails catastrophically) (§5b).
  The un-park condition is concrete: name the calibration corpus, the
  stitch-point granularity, and the acceptance test first. Typed textual
  payloads remain the evidence-backed seam.
- **Churn-tolerant pooling of home hardware for inference.** No system in
  §1 fits sovereignty (all shard one shared model; none refuse; none log
  influence), and our decomposition axis — scope distillation across many
  small judges — is different from layer-window sharding anyway. Learn
  prima.cpp's heterogeneity-aware placement and Petals' recovery
  sequence; build the sovereign version.

---

*End of dossier. Compiled by Lane A (ZCode), 2026-10-21. Every claim above
carries its fetched source or an UNVERIFIED marker; verification spot-checks
(Kurrent/Akka/Axon/AutoGen/rr licensing) were re-run independently against
the primary sources after the domain research.*
