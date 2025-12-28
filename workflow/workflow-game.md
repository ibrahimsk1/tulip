# Game Development Workflow

> **Note**: This document extends `workflow.md`. Read the base workflow first for common concepts.

This document provides game-specific details for the development workflow. For common concepts (agents, CUs, test-first, etc.), see `workflow.md`.

---

## Design Document: GDD (Game Design Document)

### Game Design Document (GDD)

The **Game Design Document** (`gdd_<project>_v*.md`) defines **what** the game should be: goals, core loop, scope, controls, entities, user experience, UI/visuals, win/lose conditions, and gameplay mechanics.

**Blueprint**: All GDDs must follow the structure defined in `blueprint/gdd-blueprint.md`.

**Example locations:**
- `v0/gdd_orbitalrush_v0.md` - Initial minimal playable version
- `v1/gdd_orbitalrush_v1.md` - Multiplayer rooms upgrade

### Delta Documents (Game-Specific)

For version upgrades, we create **delta documents** that track changes between GDD versions. These are maintained by the **Delta Agent** (`agents/deltas/delta_agent.yaml`):

- **Delta YAML files** (`agents/deltas/gdd_v*_to_v*.yaml`) – Structured list of changes (ADD, MODIFY, REMOVE) with delta IDs, descriptions, GDD references, importance, and status

**Delta Agent Process:**
- **Extract mode**: Identifies changes between GDD versions (gameplay mechanics, systems, entities, protocol changes)

**Purpose**: Delta documents explicitly describe what changed from the previous GDD version, providing traceability for planning and implementation.

### Gap Documents (Game-Specific)

For version upgrades, we create **gap documents** that identify missing implementation details in the TDD compared to GDD requirements:

- **Gap YAML files** (`agents/gaps/tdd_v*_to_gdd_v*.yaml`) – Structured list of gaps (MISSING, OUTDATED, INCOMPLETE) with gap IDs, implementation specifications, TDD/GDD references, priority, and status

**Purpose**: Gap documents identify actionable TDD improvements needed to support GDD requirements, focusing on implementation details that GDD doesn't specify but TDD should document.

---

## D-Loop Definitions (Game-Specific)

**IMPORTANT: D-loops are applied per service.** Each service (client, server) has its own independent progression through D-loops. Services share D0 (workspace) and D4 (protocol contracts) but have service-specific adapters.

### Server Service D-loops (Games)

1. **D0 Workspace**: Repo, tools, CI/CD setup
2. **D1 Entities**: Core data types for simulation (Ship, Planet, Pallet, World)
3. **D2 Physics**: Pure physics simulation (gravity, collisions, integrator)
4. **D3 Rules**: Game rules engine (energy system, win/lose conditions)
5. **D4 Protocol & Contracts**: Communication/interface contracts (message schemas, validation)
6. **D5 Session**: Per-room simulation session orchestration (tick loop, input queue)
7. **D6 Room Management**: Room lifecycle and player coordination
8. **D7 Transport**: WebSocket handler and message routing
9. **D8 Observability & Ops**: Cross-cutting hardening (logs, metrics, traces, health)
10. **D9 Scenarios, Perf, Anti-Cheat**: End-to-end validation (golden replays, chaos tests, perf)

### Client Service D-loops (Games)

1. **D0 Workspace**: Repo, tools, CI/CD setup
2. **D1 State**: Client-side game state management
3. **D2 App Orchestrator**: Coordinates client subsystems (net, gfx, ui, input, sim)
4. **D4 Protocol & Contracts**: Communication/interface contracts (shared with server)
5. **D5 Network**: WebSocket client and room management
6. **D6 Rendering**: PixiJS rendering system (sprites, camera, radar)
7. **D7 UI**: User interface components (main menu, room lobby, HUD)
8. **D8 Input**: Keyboard input handling
9. **D9 Observability & Ops**: Cross-cutting hardening (console logging, error handling)
10. **D10 Scenarios, Perf**: Multi-part confirmation (client integration tests, rendering tests)

**Key principle**: You cannot advance to loop `D(n+1)` until all proofs for `Dn` are green for that service. Each service progresses through its D-loops independently.

---

## Subsystem Classifications (Game-Specific)

Each CU is classified by **subsystem**:

**subsystem:**
- `model` – Simulation entities (World, Ship, Planet, Pallet, Vec2, WorldBounds)
- `physics` – Physics calculations (gravity, collisions, integrator, wraparound)
- `rules` – Game rules (energy economy, win/lose, state transitions)
- `orch` – Orchestration (session, room, tick loop, command queue)
- `proto` – Protocol (message schemas, validation)
- `client` – Client-side rendering, input, state management
- `ops` – Observability (logs, metrics, traces, health)
- `scenario` – End-to-end tests, golden replays, chaos tests

### CU Examples (Game-Specific)

**kind:**
- **`core`** – Evolves the primitives of a package (e.g., new entity fields, new physics primitive, new rules state). Must update the corresponding package spec, and only operates inside that package's core code.
  - Examples: new Ship fields, new gravity formula, new win condition
- **`feature`** – Composes existing primitives to implement gameplay features (e.g., room lobby, radar). Must not introduce new core entities, physics formulas, or rules transitions. Cannot change package specs.
  - Examples: room lobby UI, radar display, leaderboard
- **`refactor`** – Structural changes (renames, moves, mechanical cleanup) with no external behavior change. Tests must remain green; no new primitives.

**Guardrail**: Feature CUs are not allowed to define new game state structs, physics formulas, or rules state machines. Any such change must be done as a Core CU for the relevant package and reflected in the TDD + package spec.

---

## Proof Labels (Game-Specific)

Tests are labeled to indicate their scope and purpose:
- `scope:unit|contract|integration|e2e|perf`
- `loop:d0-work|d1-physics|d2-rules|d3-orch|d4-proto|d5-adapter|d6-client|d7-ops|d8-scenario`
- `layer:sim|contract|server|client|ops|infra|scenario`
- `net:proto:v<major>` - Protocol version
- `dep:<dependency>` - Integration dependencies
- `double:<test-double>` - Test doubles used

**Example proof labels:**
- `scope:unit loop:d1-physics layer:sim` - Unit test for physics in simulation layer
- `scope:integration loop:d5-session layer:server` - Integration test for session orchestration

---

## Inside-Out Development (Game-Specific)

**Server Service:**
- Start with entities (D1)
- Add physics (D2)
- Add rules (D3)
- Define contracts (D4)
- Add session orchestration (D5)
- Add room management (D6)
- Implement transport (D7)
- Add observability (D8)
- Validate end-to-end (D9)

**Client Service:**
- Start with state management (D1)
- Add app orchestrator (D2)
- Define contracts (D4, shared with server)
- Implement network client (D5)
- Add rendering (D6)
- Add UI (D7)
- Add input handling (D8)
- Add observability (D9)
- Validate end-to-end (D10)

---

## Paradigm Highlights (Game-Specific)

**Paradigm highlights**: core principles (sim-first, contracts over code, test-first)

- **sim-first**: Simulation logic is the source of truth; all other layers adapt to it
- **contracts over code**: Protocol and API contracts are defined before implementation
- **test-first**: Always write tests before implementation

---

## File Structure Examples (Game-Specific)

```
workflow/
└── workflow-game.md                # This file

blueprint/
└── gdd-blueprint.md                # GDD structure template

v0/
├── gdd_orbitalrush_v0.md          # v0 Game Design Document
├── tdd_orbitalrush_v0.md          # v0 Technical Design Document
└── devplan_orbitalrush.md         # v0 Development Plan

v1/
├── gdd_orbitalrush_v1.md          # v1 Game Design Document
├── tdd_orbitalrush_v1.md          # v1 Technical Design Document
└── devplan_orbitalrush_v1.md      # v1 Development Plan

agents/
├── deltas/
│   └── gdd_v0_to_v1.yaml         # GDD v0 to v1 changes (structured)
└── gaps/
    └── tdd_v0_to_gdd_v1.yaml     # TDD v0 gaps for GDD v1 (structured)

server/internal/
├── sim/
│   ├── entities/SPEC.v1.md       # Simulation entities spec
│   ├── physics/SPEC.v1.md        # Physics spec
│   └── rules/SPEC.v1.md          # Game rules spec
└── proto/SPEC.v1.md              # Protocol spec

client/src/
├── core/API.md                   # Core package API reference
├── ui/API.md                     # UI package API reference
├── input/API.md                  # Input package API reference
├── gfx/API.md                    # Graphics package API reference
├── net/API.md                    # Network package API reference
└── sim/API.md                    # Simulation package API reference
```

---

## Best Practices (Game-Specific)

1. **Single source of truth for sim entities**: All game state entities (World, Ship, Planet, Pallet, WorldBounds, Vec2) live in `server/internal/sim/entities` and are defined in the model spec. No parallel world/entity structs elsewhere.

2. **Core logic only in package cores**: Physics formulas in `/sim/physics`, rules logic in `/sim/rules`, etc. Higher layers (session, room, proto, client) compose but don't re-implement these.

3. **Deterministic simulation**: Server simulation must be deterministic for replay and anti-cheat purposes.

4. **Golden replays**: Use deterministic replays to validate game state consistency.

---

## Architect Agent Inputs/Outputs (Game-Specific)

### Inputs
- `v*/gdd_<project>_v*.md` - Current game design document
- `v*/tdd_<project>_v*.md` - Current technical design document
- `agents/deltas/gdd_v*_to_v*.yaml` - Delta documents (GDD changes between versions)
- `agents/gaps/tdd_v*_to_gdd_v*.yaml` - Gap documents (TDD implementation gaps)

### Outputs
- `v*/tdd_<project>_v*.md` - Updated technical design document
- `server/**/SPEC.md` - Updated or created service/module specs
- `client/**/API.md` - Updated or created client-facing API documentation

---

## Planning Agent Inputs (Game-Specific)

- `v*/devplan_<project>_v*.md` - Master development plan
- `v*/gdd_<project>_v*.md` - Game design document (must follow `blueprint/gdd-blueprint.md`)
- `v*/tdd_<project>_v*.md` - Technical design document (must follow `blueprint/tdd-blueprint.md`)
- `agents/deltas/gdd_v*_to_v*.yaml` - Delta documents (GDD changes between versions)
- `agents/gaps/tdd_v*_to_gdd_v*.yaml` - Gap documents (TDD implementation gaps)

---

## Implementation Agent Inputs (Game-Specific)

- `v*/devplan_<project>_v*.md` - Master development plan
- `v*/gdd_<project>_v*.md` - Game design document (must follow `blueprint/gdd-blueprint.md`)
- `v*/tdd_<project>_v*.md` - Technical design document (must follow `blueprint/tdd-blueprint.md`)
- `agents/deltas/gdd_v*_to_v*.yaml` - Delta documents (GDD changes between versions)
- `agents/gaps/tdd_v*_to_gdd_v*.yaml` - Gap documents (TDD implementation gaps)

---

This workflow ensures systematic, testable, and maintainable game development with clear separation of concerns and explicit quality gates at each layer.

