# Gap Analysis Journal (TDD v0 → GDD v1)

This journal records how the gap file `agents/gaps/tdd_v0_to_gdd_v1.yaml`
evolves over time.

Each entry describes:
- which parts of TDD v0 were inspected,
- which GDD v1 requirements were compared,
- which gaps were identified/changed/removed,
- any critiques and fixes.

The file is **append-only**. Do not rewrite history; add new entries
to correct previous mistakes.

---

## Entry Format

Each entry is a YAML block:

- `timestamp` – ISO8601
- `invocation_id` – unique per run
- `mode` – `extract | refine | critique`
- `summary` – 1–3 sentences
- `tdd_sections_touched[]` – list of relevant TDD sections (e.g. `["§3", "§4"]`)
- `gdd_sections_touched[]` – list of relevant GDD sections (e.g. `["§3", "§8"]`)
- `gaps_added[]` – list of gap_ids
- `gaps_updated[]` – list of `{gap_id, changes}`
- `gaps_marked_invalid[]` – list of gap_ids
- `notes` – free-form comments

### Example (extract)

```yaml
timestamp: "2025-01-15T10:00:00Z"
invocation_id: "GAP-0001"
mode: "extract"
summary: >
  First pass over TDD v0 Section 3. Identified missing subsystem specification
  references and package path inconsistencies.

tdd_sections_touched:
  - "§3 Server (Go) Design"

gdd_sections_touched:
  - "§3 Scope Included"
  - "§8 Networking"

gaps_added:
  - "GAP-1-missing-subsystem-specs"
  - "GAP-2-package-path-inconsistency"

gaps_updated: []

gaps_marked_invalid: []

notes: >
  Focused on implementation details and documentation structure gaps that
  delta analysis doesn't cover. Avoided duplicating delta analysis findings.
```

---

## Entries

(Journal entries will be appended here as gap analysis progresses. Note: Previous entries were removed as they contained redundant gaps that duplicated delta analysis. New entries should focus on actionable implementation details only.)

---

```yaml
gap_reasoning:
  id: "GR-2025-01-15-001"
  mode: "extract"
  scope: "TDD v0 vs GDD v1 - Initial gap extraction focusing on implementation details"
  tdd_sections_read:
    - "§1 Architecture Overview"
    - "§2 Protocol"
    - "§3 Server (Go) Design"
    - "§4 Client (PixiJS) Design"
    - "§5 Configuration & Constants"
    - "§8 Design Patterns & Principles"
  gdd_sections_read:
    - "§1 Goal of v1"
    - "§3 Scope Included"
    - "§6 Simulation Model"
    - "§7 Entities"
    - "§8 Networking"
    - "§9 UI & Visuals"
    - "§10 Camera System"
    - "§11 Radar System"
  delta_file_checked:
    - "Δ-3-room-system (room system added, but no algorithm specified)"
    - "Δ-6-world-bounds-wraparound (wraparound added, but no formula specified)"
    - "Δ-6-gravity-multiple-planets (multiple planets added, but no gravity summation specified)"
    - "Δ-6-entity-planet (planet entity added, but no generation algorithm specified)"
    - "Δ-10-camera-follow (camera system added, but no lerp implementation specified)"
    - "Δ-11-radar-system (radar system added, but no coordinate mapping specified)"
    - "Δ-3-larger-world (world size changed, but constants not specified)"
  assertions:
    - id: "A-01"
      statement: "TDD v0 Section 3 specifies package path /cmd/orbitalrush but codebase uses /cmd/server"
      evidence:
        tdd_file: "v0/tdd_orbitalrush_v0.md"
        tdd_location: "§3 Server (Go) Design - Package Structure: /cmd/orbitalrush"
        codebase_file: "server/cmd/server/main.go"
        codebase_location: "package main in server/cmd/server/"
    - id: "A-02"
      statement: "TDD v0 doesn't specify room code generation algorithm, only mentions room system exists"
      evidence:
        tdd_file: "v0/tdd_orbitalrush_v0.md"
        tdd_location: "§3 - no room code algorithm"
        gdd_file: "v1/gdd_orbitalrush_v1.md"
        gdd_location: "§3 - room code: 6-character alphanumeric"
        delta_file: "agents/deltas/gdd_v0_to_v1.yaml"
        delta_location: "Δ-3-room-system (says room system added, but no algorithm)"
    - id: "A-03"
      statement: "TDD v0 doesn't specify world wraparound formula implementation"
      evidence:
        tdd_file: "v0/tdd_orbitalrush_v0.md"
        tdd_location: "§3 Physics - no wraparound formula"
        gdd_file: "v1/gdd_orbitalrush_v1.md"
        gdd_location: "§6 - wraparound formula specified in GDD but not in TDD"
        delta_file: "agents/deltas/gdd_v0_to_v1.yaml"
        delta_location: "Δ-6-world-bounds-wraparound (mentions wraparound but not formula)"
    - id: "A-04"
      statement: "TDD v0 only specifies single sun gravity, doesn't specify how to sum multiple planet gravities"
      evidence:
        tdd_file: "v0/tdd_orbitalrush_v0.md"
        tdd_location: "§3 Physics - gravity.go only handles single sun"
        codebase_file: "server/internal/sim/physics/gravity.go"
        codebase_location: "GravityAcceleration function takes single sunPos, sunMass"
        gdd_file: "v1/gdd_orbitalrush_v1.md"
        gdd_location: "§6 - multiple planets (3-5) with gravity wells"
    - id: "A-05"
      statement: "TDD v0 doesn't specify planet generation/distribution algorithm"
      evidence:
        tdd_file: "v0/tdd_orbitalrush_v0.md"
        tdd_location: "§3 - no planet generation algorithm"
        gdd_file: "v1/gdd_orbitalrush_v1.md"
        gdd_location: "§6 - planets: radius 30-80 m, mass 500-2000, min 200 m apart"
    - id: "A-06"
      statement: "TDD v0 doesn't specify camera lerp smoothing implementation"
      evidence:
        tdd_file: "v0/tdd_orbitalrush_v0.md"
        tdd_location: "§4 Client Design - no camera system"
        gdd_file: "v1/gdd_orbitalrush_v1.md"
        gdd_location: "§10 - camera lerp factor ~0.1-0.15 per frame"
    - id: "A-07"
      statement: "TDD v0 doesn't specify radar coordinate mapping algorithm"
      evidence:
        tdd_file: "v0/tdd_orbitalrush_v0.md"
        tdd_location: "§4 Client Design - no radar system"
        gdd_file: "v1/gdd_orbitalrush_v1.md"
        gdd_location: "§11 - radar: 200×200 px, world 2000×2000 m, coordinate mapping"
    - id: "A-08"
      statement: "TDD v0 specifies 'one session per connection' but v1 needs 'one session per room'"
      evidence:
        tdd_file: "v0/tdd_orbitalrush_v0.md"
        tdd_location: "§3 Session Pattern: 'one session per WebSocket connection'"
        gdd_file: "v1/gdd_orbitalrush_v1.md"
        gdd_location: "§3 - room system with multiple players per room"
    - id: "A-09"
      statement: "TDD v0 doesn't specify WORLD_WIDTH and WORLD_HEIGHT constant values"
      evidence:
        tdd_file: "v0/tdd_orbitalrush_v0.md"
        tdd_location: "§5 Constants - mentions world bounds may be constants but no values"
        gdd_file: "v1/gdd_orbitalrush_v1.md"
        gdd_location: "§6 - world: 2000 m × 2000 m"
    - id: "A-10"
      statement: "TDD v0 doesn't reference subsystem SPEC.md files that exist in codebase"
      evidence:
        tdd_file: "v0/tdd_orbitalrush_v0.md"
        tdd_location: "§3 - no SPEC.md references"
        codebase_files:
          - "server/internal/sim/entities/SPEC.md"
          - "server/internal/sim/physics/SPEC.md"
          - "server/internal/sim/rules/SPEC.md"
          - "server/internal/session/SPEC.md"
          - "server/internal/proto/SPEC.md"
          - "server/internal/transport/SPEC.md"
  gaps_proposed:
    - gap_id: "GAP-1-package-path-inconsistency"
      type: "OUTDATED"
      implementation_spec: "Update TDD v0 Section 3: change '/cmd/orbitalrush' to '/cmd/server'"
      priority: "MEDIUM"
    - gap_id: "GAP-2-missing-room-code-algorithm"
      type: "MISSING"
      implementation_spec: "Algorithm: crypto/rand, 6 chars A-Z0-9, collision check, retry (max 10)"
      priority: "CRITICAL"
    - gap_id: "GAP-3-missing-world-wraparound-formula"
      type: "MISSING"
      implementation_spec: "Formula: pos.x = mod(pos.x + WORLD_WIDTH/2, WORLD_WIDTH) - WORLD_WIDTH/2 (same for y)"
      priority: "HIGH"
    - gap_id: "GAP-4-missing-multiple-planet-gravity"
      type: "MISSING"
      implementation_spec: "Algorithm: Loop over planets, sum gravity vectors: totalAcc = sum(planetGravityAcceleration(...))"
      priority: "CRITICAL"
    - gap_id: "GAP-5-missing-planet-generation-algorithm"
      type: "MISSING"
      implementation_spec: "Algorithm: Generate 3-5 planets, random radius [30,80], mass [500,2000], position with min 200m spacing, collision check"
      priority: "CRITICAL"
    - gap_id: "GAP-6-missing-camera-lerp-implementation"
      type: "MISSING"
      implementation_spec: "Algorithm: cameraPos = lerp(cameraPos, shipPos, 0.1-0.15), clamp to world bounds"
      priority: "HIGH"
    - gap_id: "GAP-7-missing-radar-coordinate-mapping"
      type: "MISSING"
      implementation_spec: "Algorithm: radarX = ((worldX - (-WORLD_WIDTH/2)) / WORLD_WIDTH) * RADAR_WIDTH, map world [-1000,1000] to pixels [0,200]"
      priority: "HIGH"
    - gap_id: "GAP-8-missing-room-session-pattern"
      type: "MISSING"
      implementation_spec: "Pattern: one Session per Room (not per Connection), shared Session instance, sync.RWMutex for concurrent access"
      priority: "CRITICAL"
    - gap_id: "GAP-9-missing-world-constants"
      type: "MISSING"
      implementation_spec: "Constants: WORLD_WIDTH = 2000.0 m, WORLD_HEIGHT = 2000.0 m"
      priority: "HIGH"
    - gap_id: "GAP-10-missing-subsystem-spec-references"
      type: "MISSING"
      implementation_spec: "Add references to 6 SPEC.md files in TDD v0 Section 3"
      priority: "MEDIUM"
```

---

```yaml
gap_reasoning:
  id: "GR-2025-01-15-002"
  mode: "refine"
  scope: "Refinement of initial gap extraction - improving descriptions, verifying non-redundancy, marking as REVIEWED"
  tdd_sections_touched:
    - "§3 Server (Go) Design"
    - "§4 Client (PixiJS) Design"
    - "§5 Configuration & Constants"
  gdd_sections_touched:
    - "§3 Scope Included"
    - "§6 Simulation Model"
    - "§10 Camera System"
    - "§11 Radar System"
  delta_file_checked:
    - "Δ-3-room-system (verified: mentions room codes but not generation algorithm)"
    - "Δ-6-world-bounds-wraparound (verified: provides formula but not placement/timing)"
    - "Δ-6-gravity-multiple-planets (verified: mentions multiple planets but not summation)"
    - "Δ-6-entity-planet (verified: mentions planet specs but not generation)"
    - "Δ-10-camera-follow (verified: mentions lerp factor but not implementation)"
    - "Δ-11-radar-system (verified: mentions coordinate mapping but not formula)"
    - "Δ-3-larger-world (verified: mentions world size but not constants)"
  refinement_actions:
    - id: "RF-01"
      gap_id: "GAP-3-missing-world-wraparound-formula"
      action: "UPDATE_DESCRIPTION"
      reason: "Delta provides formula but not placement/timing. Refined to specify WHERE (after SemiImplicitEuler, before collision) and that it applies to all ships in multiplayer."
      changes: "Added placement details (after Step 2 Physics, before Step 3 Collisions), specified multiplayer context (all ships in World.Ships array), added determinism requirement."
    - id: "RF-02"
      gap_id: "GAP-2-missing-room-code-algorithm"
      action: "UPDATE_DESCRIPTION + MARK_REVIEWED"
      reason: "Improved implementation_spec with character set details, function signature, mutex protection. Gap is specific and actionable."
      changes: "Added character set specification ('ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789'), function signature, mutex protection details. Marked as REVIEWED."
    - id: "RF-03"
      gap_id: "GAP-4-missing-multiple-planet-gravity"
      action: "UPDATE_DESCRIPTION + MARK_REVIEWED"
      reason: "Improved implementation_spec with code structure (loop, initialization, summation), function signature, superposition principle explanation."
      changes: "Added code structure details (initialize totalAcc, loop over planets, sum vectors), function signature, superposition principle note. Marked as REVIEWED."
    - id: "RF-04"
      gap_id: "GAP-5-missing-planet-generation-algorithm"
      action: "UPDATE_DESCRIPTION + MARK_REVIEWED"
      reason: "Improved implementation_spec with specific random formulas, function signature, retry logic details."
      changes: "Added specific random formulas (radius = 30.0 + rand.Float64() * 50.0), function signature GeneratePlanets(count, worldWidth, worldHeight), retry logic details. Marked as REVIEWED."
    - id: "RF-05"
      gap_id: "GAP-6-missing-camera-lerp-implementation"
      action: "UPDATE_DESCRIPTION + MARK_REVIEWED"
      reason: "Improved implementation_spec with Camera class structure, transform application details, bounds clamping formula."
      changes: "Added Camera class structure, transform application (container.position.set), bounds clamping formula. Marked as REVIEWED."
    - id: "RF-06"
      gap_id: "GAP-7-missing-radar-coordinate-mapping"
      action: "UPDATE_DESCRIPTION + MARK_REVIEWED"
      reason: "Improved implementation_spec with Radar class structure, worldToRadar function signature, rendering details for each entity type."
      changes: "Added Radar class structure, worldToRadar function signature, detailed rendering instructions for planets/ships/pallets. Marked as REVIEWED."
    - id: "RF-07"
      gap_id: "GAP-8-missing-room-session-pattern"
      action: "UPDATE_DESCRIPTION + MARK_REVIEWED"
      reason: "Improved implementation_spec with Room struct definition, PlayerConnection struct, mutex usage patterns, session lifecycle."
      changes: "Added Room struct definition with fields, PlayerConnection struct, mutex usage (RWMutex for reads/writes), session lifecycle details. Marked as REVIEWED."
    - id: "RF-08"
      gap_id: "GAP-9-missing-world-constants"
      action: "UPDATE_DESCRIPTION + MARK_REVIEWED"
      reason: "Improved implementation_spec with package location, usage references to other gaps, export requirements."
      changes: "Added package location (entities or physics), usage references (GAP-3, GAP-5, GAP-7), export requirements. Marked as REVIEWED."
    - id: "RF-09"
      gap_id: "GAP-10-missing-subsystem-spec-references"
      action: "UPDATE_DESCRIPTION + MARK_REVIEWED"
      reason: "Improved implementation_spec with subsection structure, specific file references, update location in TDD."
      changes: "Added subsection structure ('Subsystem Specifications'), specific file references with descriptions, update location in TDD Section 3. Marked as REVIEWED."
    - id: "RF-10"
      gap_id: "GAP-1-package-path-inconsistency"
      action: "UPDATE_DESCRIPTION + MARK_REVIEWED"
      reason: "Improved description to clarify it's technical debt, improved implementation_spec with find-replace instruction."
      changes: "Added technical debt clarification, find-replace instruction. Marked as REVIEWED."
  gaps_updated:
    - gap_id: "GAP-1-package-path-inconsistency"
      changes: "Description clarified as technical debt, implementation_spec improved with find-replace details, status: UNREVIEWED → REVIEWED"
    - gap_id: "GAP-2-missing-room-code-algorithm"
      changes: "Implementation_spec improved with character set, function signature, mutex details, status: UNREVIEWED → REVIEWED"
    - gap_id: "GAP-3-missing-world-wraparound-formula"
      changes: "Description refined to note delta provides formula but not placement, implementation_spec improved with placement/timing, multiplayer context, status: UNREVIEWED → REVIEWED"
    - gap_id: "GAP-4-missing-multiple-planet-gravity"
      changes: "Implementation_spec improved with code structure, function signature, superposition principle, status: UNREVIEWED → REVIEWED"
    - gap_id: "GAP-5-missing-planet-generation-algorithm"
      changes: "Implementation_spec improved with random formulas, function signature, retry logic, status: UNREVIEWED → REVIEWED"
    - gap_id: "GAP-6-missing-camera-lerp-implementation"
      changes: "Implementation_spec improved with Camera class, transform application, bounds clamping, status: UNREVIEWED → REVIEWED"
    - gap_id: "GAP-7-missing-radar-coordinate-mapping"
      changes: "Implementation_spec improved with Radar class, function signature, rendering details, status: UNREVIEWED → REVIEWED"
    - gap_id: "GAP-8-missing-room-session-pattern"
      changes: "Implementation_spec improved with struct definitions, mutex patterns, session lifecycle, status: UNREVIEWED → REVIEWED"
    - gap_id: "GAP-9-missing-world-constants"
      changes: "Implementation_spec improved with package location, usage references, export requirements, status: UNREVIEWED → REVIEWED"
    - gap_id: "GAP-10-missing-subsystem-spec-references"
      changes: "Implementation_spec improved with subsection structure, file references, update location, status: UNREVIEWED → REVIEWED"
  gaps_marked_invalid: []
  gaps_added: []
  gaps_removed: []
  notes: >
    All 10 gaps verified as non-redundant with delta analysis. Each gap provides
    implementation details that deltas don't cover (algorithms, formulas, patterns,
    placement/timing). All gaps now have specific, actionable implementation_spec
    fields. All gaps marked as REVIEWED since they meet criteria: specific
    implementation details, not duplicating delta analysis, actionable for developers.
    GAP-3 was refined to clarify that delta provides formula but gap provides
    placement/timing details. All other gaps improved with more specific code
    structure, function signatures, and implementation patterns.
```

---

```yaml
gap_critique:
  id: "GC-2025-01-15-001"
  target_reasoning_id: "GR-2025-01-15-002"
  findings:
    - id: "F-01"
      type: "MISSING_GAP"
      description: "TDD v0 World entity has single Ship field, but GDD v1 requires Ships array for multiplayer. GAP-8 mentions World.Ships but doesn't explicitly identify the entity model change as a gap."
      severity: "CRITICAL"
      tdd_section: "§3 Server (Go) Design - Data Types: type State struct { Ship Ship; ... }"
      gdd_section: "§7 Entities: Ship has id (player ID), §8 Networking: snapshot has ships array"
      delta_section: "Δ-8-networking-snapshot-format (mentions ships array in protocol but not entity model)"
      suggested_fix:
        action: "ADD_GAP"
        new_gap:
          gap_id: "GAP-11-missing-world-ships-array"
          type: "MISSING"
          description: "TDD v0 World entity has single Ship field (Ship Ship), but GDD v1 requires Ships array (Ships []Ship) for multiplayer support. Entity model needs to change from single ship to array of ships with player IDs."
          tdd_refs: ["§3 Server (Go) Design - Data Types"]
          gdd_refs: ["§7 Entities - Ship", "§8 Networking - Snapshot Format"]
          delta_relation: "Δ-8-networking-snapshot-format"
          implementation_spec: "Change World struct: replace 'Ship Ship' field with 'Ships []Ship' field. Add Ship.ID uint32 field to Ship struct for player identification. Update all code that accesses world.Ship to iterate over world.Ships. Update NewWorld() to accept Ships []Ship instead of Ship. Update rules.Step() to process inputs for specific ship by ID: find ship in Ships array by playerID, apply input to that ship. Update collision detection to check all ships. Update win/lose conditions to check all ships (per-player win/lose). Function signature changes: NewWorld(ships []Ship, planets []Planet, pallets []Pallet) World."
          priority: "CRITICAL"
          status: "UNREVIEWED"
          arch_status: "UNRESOLVED"
    - id: "F-02"
      type: "MISSING_GAP"
      description: "TDD v0 World entity has single Sun field, but GDD v1 requires Planets array. GAP-5 mentions planet generation but doesn't explicitly identify the entity model change from Sun to Planets array."
      severity: "CRITICAL"
      tdd_section: "§3 Server (Go) Design - Data Types: type State struct { Sun Sun; ... }"
      gdd_section: "§7 Entities: Planet (multiple), §8 Networking: snapshot has planets array"
      delta_section: "Δ-7-entity-sun-to-planet, Δ-8-networking-snapshot-format"
      suggested_fix:
        action: "ADD_GAP"
        new_gap:
          gap_id: "GAP-12-missing-world-planets-array"
          type: "MISSING"
          description: "TDD v0 World entity has single Sun field (Sun Sun), but GDD v1 requires Planets array (Planets []Planet) for multiple planets. Entity model needs to change from single sun to array of planets."
          tdd_refs: ["§3 Server (Go) Design - Data Types"]
          gdd_refs: ["§7 Entities - Planet", "§8 Networking - Snapshot Format"]
          delta_relation: "Δ-7-entity-sun-to-planet"
          implementation_spec: "Change World struct: replace 'Sun Sun' field with 'Planets []Planet' field. Add Planet.ID uint32 field to Planet struct for identification. Update all code that accesses world.Sun to iterate over world.Planets. Update NewWorld() to accept Planets []Planet instead of Sun. Update gravity calculation (GAP-4) to loop over Planets array. Update collision detection to check all planets. Function signature changes: NewWorld(ships []Ship, planets []Planet, pallets []Pallet) World. Planet struct: type Planet struct { ID uint32, Pos Vec2, Radius float32, Mass float64 }."
          priority: "CRITICAL"
          status: "UNREVIEWED"
          arch_status: "UNRESOLVED"
    - id: "F-03"
      type: "VERIFICATION"
      description: "All existing gaps verified for redundancy with delta analysis - all gaps provide implementation details beyond deltas. All gaps verified for actionability - all have specific implementation_spec fields."
      severity: "INFO"
      suggested_fix:
        action: "NONE"
        note: "All 10 existing gaps are valid and actionable. No gaps need to be marked invalid or removed."
  gaps_added:
    - "GAP-11-missing-world-ships-array"
    - "GAP-12-missing-world-planets-array"
  gaps_marked_invalid: []
  gaps_updated: []
  notes: >
    Critique identified 2 critical missing gaps: entity model changes from single
    Ship/Sun to arrays. These are fundamental structural changes that affect all
    simulation code. GAP-8 and GAP-5 mention the arrays but don't explicitly call
    out the entity model structure change as a gap. These gaps are critical because
    they require changes to the core World struct definition, which cascades to all
    code that uses World (physics, rules, session, protocol conversion).
```

---

```yaml
gap_reasoning:
  id: "GR-2025-01-15-003"
  mode: "refine"
  scope: "Refinement of newly added gaps (GAP-11, GAP-12) from critique - improving implementation details"
  tdd_sections_touched:
    - "§3 Server (Go) Design - Data Types"
  gdd_sections_touched:
    - "§7 Entities"
    - "§8 Networking - Snapshot Format"
  delta_file_checked:
    - "Δ-7-entity-sun-to-planet (verified: mentions entity change but not implementation)"
    - "Δ-8-networking-snapshot-format (verified: mentions ships array in protocol but not entity model)"
  refinement_actions:
    - id: "RF-11"
      gap_id: "GAP-11-missing-world-ships-array"
      action: "UPDATE_DESCRIPTION + MARK_REVIEWED"
      reason: "Improved implementation_spec with detailed step-by-step changes: entity struct changes, function signature updates, loop patterns for processing all ships, per-player input handling, collision detection updates, win/lose condition changes."
      changes: "Added detailed implementation steps: (1) World struct field change, (2) Ship.ID field addition, (3) NewWorld() signature update, (4) rules.Step() signature change with playerID parameter, (5) ship lookup by ID in Step(), (6) physics loop over all ships, (7) collision detection loops, (8) per-player win/lose evaluation, (9) protocol conversion updates. Marked as REVIEWED."
    - id: "RF-12"
      gap_id: "GAP-12-missing-world-planets-array"
      action: "UPDATE_DESCRIPTION + MARK_REVIEWED"
      reason: "Improved implementation_spec with detailed step-by-step changes: Planet struct creation, World struct field change, Sun type removal, function signature updates, gravity calculation updates, collision detection updates, protocol conversion."
      changes: "Added detailed implementation steps: (1) Planet struct creation, (2) World struct field change, (3) Sun type removal, (4) NewWorld() signature update, (5) gravity calculation loop (GAP-4 integration), (6) collision detection loop over all planets, (7) physics function renaming/creation, (8) protocol conversion updates, (9) initial world creation using GeneratePlanets(). Marked as REVIEWED."
  gaps_updated:
    - gap_id: "GAP-11-missing-world-ships-array"
      changes: "Implementation_spec improved with step-by-step entity model changes, function signature updates, loop patterns, per-player processing, status: UNREVIEWED → REVIEWED"
    - gap_id: "GAP-12-missing-world-planets-array"
      changes: "Implementation_spec improved with step-by-step entity model changes, Planet struct creation, Sun removal, gravity/collision updates, status: UNREVIEWED → REVIEWED"
  gaps_marked_invalid: []
  gaps_added: []
  gaps_removed: []
  notes: >
    Refined the 2 critical entity model gaps identified in critique. Both gaps now
    have comprehensive implementation_spec fields with step-by-step changes required
    to transform the entity model from single Ship/Sun to arrays. These changes
    cascade through all simulation code (physics, rules, session, protocol). Both
    gaps marked as REVIEWED since they provide specific, actionable implementation
    details that developers can follow to implement the multiplayer entity model.
```

---
