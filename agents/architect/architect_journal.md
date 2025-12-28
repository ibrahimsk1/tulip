# Architect Journal

This journal tracks architectural decisions, changes, and analysis at each level (G0–G3).

## Format

### Refine Entry Format

Each refine entry should include:
- **Date**: When the analysis was performed
- **Level**: G0, G1, G2, or G3
- **Deltas/Gaps Reviewed**: Which deltas or gaps were analyzed
- **Objectives Checked**: Which level objectives were evaluated
- **Changes Made**: What updates were made to TDD/SPEC/API
- **Reasoning**: Why these changes were made, how they address the objectives, and any considerations

### Critique Entry Format

Each critique entry should include:
- **Date**: When the review was performed
- **Level**: G0, G1, G2, or G3
- **Changes Reviewed**: Which TDD/SPEC/API changes were reviewed
- **Checks Performed**: Which critique checks were applied
- **Issues Found**: Any contradictions, unintended behaviors, or inconsistencies
- **Actions Taken**: Simplifications, clarifications, or deferrals made
- **Reasoning**: Why these actions were taken and how they improve the architecture

---

## Entries

### Refine Entry - 2025-01-15 - G0

**Deltas/Gaps Reviewed**:
- Δ-1-goal-multiplayer: Goal changed from single-player to multiplayer with room-based matchmaking
- Δ-2-core-loop-multiplayer-flow: Core loop extended with room management steps
- Δ-12-deployment-multi-container: Deployment changed to multi-container setup

**Objectives Checked**:
- G0–O1: Clear System Boundaries (P1) - Updated architecture overview to show client/server boundaries
- G0–O2: Stable, Well-Defined Services (P1) - Documented Client Service and Server Service with clear roles
- G0–O3: Coherent System Topology (P1) - Added System Landscape section describing services and interactions
- G0–O5: Explicit Scalability Model (P1) - Documented room-based sharding model, in-memory state, horizontal scaling strategy
- G0–O10: Alignment with Game Design (P1) - Updated to match GDD v1 multiplayer requirements

**Changes Made**:
1. Updated Section 1 (Architecture Overview): Changed from "Single-player session per connection" to "Multiplayer room-based architecture" with room management
2. Added System Landscape subsection: Documented services (Client, Server), high-level data flows (room management, game input, game state), scalability model (room-based sharding, in-memory state), and communication channels
3. Updated Section 8 (Design Patterns): Changed Session Pattern from "one session per connection" to "one session per room (shared by all players)"
4. Updated Section 7 (Acceptance Criteria): Added room management, multiplayer, camera, radar, and wraparound acceptance criteria

**Reasoning**:
- System landscape changes reflect fundamental shift from single-player to multiplayer architecture
- Room-based sharding model provides clear scalability path (each room is independent)
- Session pattern change is critical architectural decision: one session per room enables shared game state for all players
- System boundaries clarified: Client handles rendering/UI, Server handles authoritative simulation and room management
- Changes align with GDD v1 requirements for multiplayer, room system, and larger world with multiple planets

### Critique Entry - 2025-01-15 - G0

**Changes Reviewed**: Section 1 (Architecture Overview), System Landscape subsection, Section 8 (Session Pattern), Section 7 (Acceptance Criteria)

**Checks Performed**:
- Contradictions with existing sections: Checked for remaining single-player references (none found)
- Unintended behaviors: Verified changes match deltas (Δ-1, Δ-2, Δ-12)
- Lower-level dependencies: Identified that Section 2 (Protocol) needs updates for room messages and snapshot format (G3 level)

**Issues Found**:
1. Section 2 (Protocol) still shows old snapshot format (single "ship", "sun") - needs G3 update to "ships" array, "planets" array, and room management messages
2. Section 2 mentions "restart" message which is removed in v1 (Δ-4-restart-removed, Δ-5-restart-control-removed)
3. Section 3 (Server Design) package structure shows `/cmd/orbitalrush` but actual codebase uses `/cmd/server` (GAP-1) - needs G1/G2 update

**Actions Taken**:
- Noted that Section 2 protocol changes are deferred to G3 level (appropriate level for contract changes)
- Noted that Section 3 package path inconsistency will be addressed at G1/G2 level
- No contradictions found in G0-level changes; all changes align with GDD v1

**Reasoning**:
- G0 changes are consistent and correctly reflect system landscape shift to multiplayer
- Lower-level details (protocol format, package paths) appropriately deferred to G3/G2 levels
- System topology is coherent: room-based sharding model is clear and scalable
- No contradictions introduced at G0 level; changes are minimal and focused on high-level architecture

### Refine Entry - 2025-01-15 - G1

**Deltas/Gaps Reviewed**:
- Δ-3-room-system: Room system with create/join by code, lobby
- Δ-3-multiplayer-support: Multiplayer support for 2-8 players per room
- Δ-3-main-menu: Main menu entry point
- GAP-1-package-path-inconsistency: Package path /cmd/orbitalrush → /cmd/server
- GAP-2-missing-room-code-algorithm: Room code generation algorithm
- GAP-8-missing-room-session-pattern: Room session pattern (one session per room)

**Objectives Checked**:
- G1–O1: Clear Bounded Contexts (P1) - Added /room package as distinct bounded context for room management
- G1–O2: Single Responsibility per Service (P1) - Room management service has single responsibility: room lifecycle and player coordination
- G1–O3: Data Ownership Is Explicit (P1) - Documented room state ownership, room-to-session mapping
- G1–O4: Service Interactions Make Sense (P1) - Documented interactions: transport → room → session → simulation
- G1–O5: Autonomy and Independence (P1) - Room service is independent bounded context, can evolve separately

**Changes Made**:
1. Updated Section 3 package structure: Fixed path `/cmd/orbitalrush` → `/cmd/server` (GAP-1)
2. Added `/room` package: Room management bounded context with room.go, manager.go
3. Updated session description: Changed from "Per-connection session" to "Per-room session (one per room)"
4. Added Room Management Service subsection: Documented responsibilities (room creation, player management, session coordination), data ownership, service interactions, lifecycle, concurrency model
5. Updated HTTP & WS section: Added room management message handling, clarified one session per room
6. Updated Physics section: Noted multiple planets (detailed in G2)
7. Updated Data Types section: Added Ship.ID, Planet type (replaces Sun), World with arrays (detailed in G2)

**Reasoning**:
- Room management is distinct bounded context with clear responsibilities and ownership
- One session per room pattern enables shared game state for all players in room
- Service interactions are clear: transport routes messages, room manages lifecycle, session runs simulation
- Package path fix aligns documentation with actual codebase structure
- Entity model changes (arrays, Planet type) noted but detailed implementation deferred to G2 level

### Critique Entry - 2025-01-15 - G1

**Changes Reviewed**: Section 3 package structure, Room Management Service subsection, HTTP & WS section, Physics section, Data Types section

**Checks Performed**:
- Contradictions with existing sections: Checked for remaining "per connection" session references (none found, correctly updated)
- Unintended behaviors: Verified room management responsibilities match deltas (Δ-3-room-system, Δ-3-multiplayer-support)
- Lower-level dependencies: Identified that entity model details (World.Ships[], World.Planets[], gravity summation) need G2-level specification

**Issues Found**:
1. Physics section mentions "multiple planets" but doesn't specify gravity summation algorithm - deferred to G2 (appropriate)
2. Data Types section shows entity changes but doesn't specify World struct details - deferred to G2 (appropriate)
3. Room code generation algorithm mentioned but not detailed - should be in room SPEC.md at G2 level

**Actions Taken**:
- No contradictions found; all "per connection" references correctly updated to "per room"
- Room management service boundaries are clear and match GDD v1 requirements
- Lower-level implementation details (gravity algorithm, entity model details) appropriately deferred to G2 level
- Room code generation algorithm should be specified in room/SPEC.md at G2 level

**Reasoning**:
- G1 changes correctly establish room management as bounded context with clear service boundaries
- Service interactions are coherent: transport → room → session → simulation flow is logical
- Data ownership is explicit: room state owned by room manager, session owned by room
- Lower-level details appropriately deferred to G2 (module-level) where implementation specifics belong
- No contradictions introduced; changes align with G0 system landscape and GDD v1

### Refine Entry - 2025-01-15 - G2

**Deltas/Gaps Reviewed**:
- GAP-3-missing-world-wraparound-formula: World wraparound implementation placement and timing
- GAP-4-missing-multiple-planet-gravity: Multiple planet gravity summation algorithm
- GAP-5-missing-planet-generation-algorithm: Planet generation/distribution algorithm
- GAP-9-missing-world-constants: WORLD_WIDTH and WORLD_HEIGHT constant values
- GAP-11-missing-world-ships-array: World entity model change (Ship → Ships[])
- GAP-12-missing-world-planets-array: World entity model change (Sun → Planets[])
- Δ-6-gravity-multiple-planets: Multiple planets with gravity wells
- Δ-6-world-bounds-wraparound: World bounds with wraparound
- Δ-6-entity-planet: Planet entity specification
- Δ-7-entity-sun-to-planet: Entity change from Sun to Planet

**Objectives Checked**:
- G2–O1: Clear Module Boundaries (P1) - Updated SPEC.md files for entities, physics, rules, room modules with clear boundaries
- G2–O2: High Cohesion per Module (P1) - Each module has cohesive responsibilities (entities = data, physics = calculations, rules = game logic, room = lifecycle)
- G2–O3: Clean Separation of Concerns (P1) - Entities, physics, rules, room are clearly separated
- G2–O4: Directional Dependencies (P1) - Dependencies flow correctly: room → session → rules → physics → entities
- G2–O5: State and Statelessness Are Under Control (P1) - Room is stateful (rooms map), entities are data, physics/rules are stateless functions

**Changes Made**:
1. Updated entities/SPEC.md: Changed World from single Ship/Sun to Ships[]/Planets[] arrays, added Ship.ID and Planet type, added planet generation algorithm, documented world constants (WORLD_WIDTH = 2000.0 m, WORLD_HEIGHT = 2000.0 m)
2. Updated physics/SPEC.md: Changed gravity from single sun to multiple planets with summation algorithm, added world wraparound formula and implementation details, updated collision from ship-sun to ship-planet, documented world constants
3. Updated rules/SPEC.md: Updated input processing to find ship by ID, updated physics step to loop over all ships, updated collision detection to loop over all ships and planets, updated win/lose conditions for multiplayer
4. Created room/SPEC.md: Documented room management module with room lifecycle, player management, session coordination, room code generation algorithm, concurrency model

**Reasoning**:
- Entity model changes (arrays, Planet type) are fundamental G2-level changes affecting all simulation modules
- Physics changes (multiple planet gravity, wraparound) are module-level implementation details
- Rules changes (multiplayer input processing, collision loops) reflect module collaboration patterns
- Room management is new G2 module with clear boundaries and responsibilities
- All changes align with G1 service boundaries and G0 system landscape
- Module boundaries are clear: entities = data, physics = calculations, rules = game logic, room = lifecycle

### Critique Entry - 2025-01-15 - G2

**Changes Reviewed**: entities/SPEC.md, physics/SPEC.md, rules/SPEC.md, room/SPEC.md

**Checks Performed**:
- Contradictions with existing sections: Verified entity model changes are consistent across all SPEC files
- Unintended behaviors: Verified all changes match deltas and gaps (GAP-3, GAP-4, GAP-5, GAP-9, GAP-11, GAP-12)
- Lower-level dependencies: All G2 changes are self-contained within module boundaries

**Issues Found**:
- No contradictions found; all SPEC files are consistent
- Module boundaries are clear and well-defined
- Dependencies flow correctly: room → session → rules → physics → entities

**Actions Taken**:
- All G2 changes are complete and consistent
- Module boundaries are clear and well-documented
- No contradictions introduced; changes align with G1 service boundaries

**Reasoning**:
- G2 changes correctly specify module-level implementation details
- Entity model changes (arrays, Planet type) are consistently documented across all affected modules
- Physics and rules changes correctly reflect multiplayer requirements
- Room management module is properly specified with clear boundaries
- All changes align with G1 service boundaries and G0 system landscape

### Refine Entry - 2025-01-15 - G3

**Deltas/Gaps Reviewed**:
- Δ-8-networking-room-messages: Room management messages (createRoom, joinRoom, leaveRoom, startMatch)
- Δ-8-networking-room-updates: Room state updates (roomState, playerJoined, playerLeft, matchStarted, matchEnded)
- Δ-8-networking-input-format: Input message format change (adds "t":"input" field)
- Δ-8-networking-snapshot-format: Snapshot format change (ships array, planets array, worldBounds, myShipId)
- Δ-4-restart-removed: Restart message removed
- Δ-5-restart-control-removed: Restart control removed

**Objectives Checked**:
- G3–O1: Contracts Match Real Behavior (P1) - Updated protocol SPEC to match GDD v1 multiplayer requirements
- G3–O2: Inputs and Outputs Are Well-Defined (P1) - All message schemas clearly defined with field types and validation rules
- G3–O3: Error Cases Are Explicit (P1) - Validation rules specified for all messages
- G3–O4: Compatibility Expectations Are Clear (P1) - Documented v0→v1 protocol changes
- G3–O5: Domain Terms Consistent with GDD/TDD (P1) - Protocol uses same domain vocabulary (room, player, ship, planet)

**Changes Made**:
1. Updated proto/SPEC.md: Added room management messages (createRoom, joinRoom, leaveRoom, startMatch), added room state messages (roomState, playerJoined, playerLeft, matchStarted, matchEnded), updated SnapshotMessage format (ships[] array, planets[] array, worldBounds, myShipId), removed RestartMessage, updated ShipSnapshot and PlanetSnapshot to include id field
2. Updated TDD Section 2 (Protocol): Added room management messages, updated snapshot format, removed restart message
3. Updated validation functions list: Added validation for all room management messages

**Reasoning**:
- Protocol changes reflect fundamental shift from single-player to multiplayer architecture
- Room management messages enable room-based matchmaking (create, join, leave, start)
- Snapshot format changes (arrays, worldBounds, myShipId) support multiplayer and larger world
- All protocol changes align with GDD v1 requirements and G2 entity model changes
- Contracts are well-defined with clear validation rules and error handling

### Critique Entry - 2025-01-15 - G3

**Changes Reviewed**: proto/SPEC.md, TDD Section 2 (Protocol)

**Checks Performed**:
- Contradictions with existing sections: Checked for remaining single-player protocol references (none found)
- Unintended behaviors: Verified all protocol changes match deltas (Δ-8-networking-*)
- Lower-level dependencies: Protocol changes align with G2 entity model (ships[], planets[])

**Issues Found**:
- No contradictions found; protocol changes are consistent
- All message schemas are well-defined with validation rules
- Protocol changes correctly reflect GDD v1 multiplayer requirements

**Actions Taken**:
- All protocol changes are complete and consistent
- Room management messages properly specified
- Snapshot format correctly updated for multiplayer
- Restart message properly removed (not in v1 scope)

**Reasoning**:
- G3 changes correctly specify contract-level details for multiplayer architecture
- Room management messages enable room-based matchmaking as specified in GDD v1
- Snapshot format changes (arrays, worldBounds, myShipId) support multiplayer and larger world
- All protocol changes align with G2 entity model and G1 service boundaries
- Contracts are well-defined with clear validation rules and error handling
- No contradictions introduced; changes are consistent across all levels (G0–G3)

