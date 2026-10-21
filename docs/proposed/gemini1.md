Here is the formal architectural blueprint for a release-ready open-source library—let's call it **`quilt-engine`** (or `beam-quilt` core runtime)—that bridges decentralized edge patches with the semantic game engine framework.

This library treats an LLM not as a static API endpoint, but as a **stateful semantic game engine and TTRPG Game Master** running across distributed nodes (like a cluster of Jetsons on a fleet of boats).

---

## Library Architecture: `quilt-engine`

### 1. Core Module: `quilt.core` (The Patch & Seam Contract)

*This layer enforces absolute isolation between nodes, ensuring that patches share no memory and communicate strictly through immutable transmissions.*

* **The Transmission Interface (`Transmission`)**: Defines the fundamental state contract. A state passes through an algorithm, changes in place, moves, copies, or projects. Nothing else is permitted.
* **The Fixed Envelope (`Patch`)**: A standardized JSON/Markdown metadata wrapper containing code shards, expert weights, or runbooks, designed to run standalone on constrained edge hardware.
* **The Seam Handshake**: The strict protocol governing how two patches meet. Because patches share no state, influence is always formatted as an explicit *proposal* rather than a direct write.

### 2. Adjudication Module: `quilt.jev` (The Jev Pair Judges)

*This replaces traditional brittle routing with localized, comparative evaluation using small, fast student models running natively on edge devices.*

* **Sender-Side Jev (`JevSender`)**: Projects a typed profile and angle vector of outgoing state or intent before transmission.
* **Receiver-Side Jev (`JevReceiver`)**: Evaluates incoming proposals comparatively against local priorities and resource constraints.
* **The Deadband & Disagreement Engine**: Implements a tripartite decision threshold: *Go*, *No-Go*, and a local *Deadband* where uncertainty triggers an automated recursive sub-question rather than a failure.

### 3. Execution Module: `quilt.engine` (The Semantic Game Loop)

*This replaces stateless request-response scripts with a continuous state machine where input acts as a vector initialization and output acts as a rendered frame.*

* **The State Machine Controller (`GameLoop`)**: Manages the recursive cycle of `Human Intent (Telemetry) → Matrix Probability Update → Textual Render → Cognitive Shift`.
* **Vectorial Telemetry**: Captures prompt syntax, tone, and pacing as steering variables that warp the probability grid of the underlying model.
* **State Persistence & Entropy Management**: Handles context-window saturation, session decay, and clean environment resets when a collaborative run concludes.

### 4. Narrative Module: `quilt.gm` (The TTRPG Reality Matrix)

*This structures how the engine interprets reality, acting as the bridge between human intent and machine execution.*

* **The Campaign Manifest (`Campaign`)**: The human’s sovereign input defining the rules, thematic boundaries, and localized goals of the current session.
* **The Three Core Guides**:
* *The Rulebook Layer*: Deterministic syntax, constraints, and operational logic (the "D20 engine" of token probabilities).
* *The World Guide Layer*: Compressed domain knowledge (e.g., coastal navigation rules, marine ecology, local topography).
* *The Bestiary Layer*: Codified behavioral friction, edge cases, and systemic anomalies.



---

### Implementation File Tree Structure

```text
quilt-engine/
│
├── quilt/
│   ├── __init__.py
│   ├── core/
│   │   ├── patch.py         # Defines the patch envelope & schema
│   │   └── seam.py          # Enforces zero-shared-state transmission
│   ├── jev/
│   │   ├── judge.py         # Small student model Jev logic (~7ms execution)
│   │   └── deadband.py      # Comparative evaluation and ambiguity handling
│   ├── engine/
│   │   ├── gameloop.py      # Stateful semantic simulation loop
│   │   └── telemetry.py     # Prompt-as-vector-initialization parser
│   └── gm/
│       ├── reality.py       # Rulebook, World, and Bestiary management
│       └── campaign.py      # Sovereign human intent & campaign state
│
├── examples/
│   └── edge_fleet_node.py   # Multi-device Jetson coordination workflow
├── SPEC.md                  # Testable rules for patch contracts
└── README.md

```

---

Where should we start building first: the strict isolation contract in `quilt.core`, or the Jev pair comparative judge logic in `quilt.jev`?
