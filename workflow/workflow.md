# Development Workflow

This document describes the common workflow structure for systematic, inside-out development. For project-specific details:
- **Games**: See `workflow-game.md`
- **Applications**: See `workflow-application.md`

---

## Overview

Our workflow follows a systematic, inside-out approach that ensures each layer is proven before moving to the next. We use AI agents to help plan and implement features, maintaining quality through explicit proofs and test-first development.

---

## Design Hierarchy

Our design documents form a clear hierarchy that establishes ownership and prevents architectural drift:

### Document Levels

1. **Product Design Document (PDD)** – Defines **what** the product is:
   - **Games**: Called GDD (Game Design Document) - see `workflow-game.md`
   - **Applications**: Called PRD (Product Requirements Document) - see `workflow-application.md`
   - User-facing behavior, goals, workflows, scope, interactions, domain entities
   - User experience: UI/UX, success/failure states, feature mechanics
   - **Must follow the structure defined in the appropriate blueprint** (`blueprint/gdd-blueprint.md` for games, `blueprint/prd-blueprint.md` for applications)

2. **TDD (Technical Design Document)** – Defines **how** the product is structured:
   - **Source of truth for architecture**: packages, core types, interfaces
   - Technical decisions: architecture patterns, protocol choices, data consistency models
   - Layer boundaries: domain logic, orchestration, protocol, transport
   - **Must follow the structure defined in `blueprint/tdd-blueprint.md`**
   - **All Planning and Implementation agents must respect the TDD as authoritative**

3. **Package Specs** – Describe **meaning and invariants** for packages:
   - Semantic descriptions of core types and their relationships
   - Invariants, constraints, and ownership rules
   - **Not duplicate code** – they explain what entities mean, not how they're implemented
   - Examples: See project-specific workflows for concrete examples

4. **API.md Files** – Provide **API reference documentation** for client-side packages:
   - Detailed interface documentation with methods, parameters, and return types
   - Semantics, lifecycle, and invariants for client packages
   - Code location and design goals for each package
   - Examples: See project-specific workflows for concrete examples

5. **Devplan (Development Plan)** – Defines **in what order** we evolve the structure:
   - D-loop progression (per service: server D0-D9, client D0-D10)
   - Capabilities and quality gates
   - Sequencing of work
   - **D-loop definitions are project-specific** - see `workflow-game.md` or `workflow-application.md`

6. **plan.md + CU Implementation Plans** – Define **how** we slice work:
   - Change Units (CUs) that implement specific capabilities
   - Task breakdowns and allowlists
   - Evidence requirements

### Critical Constraints

- **Planning & Implementation agents must not contradict the TDD**
- If a new core type, package, or subsystem change is needed:
  1. The TDD (and relevant package spec) must be updated first
  2. Planning is re-run to incorporate the updated design
  3. Only then can implementation proceed
- **No ad-hoc architectural decisions** – all structural changes flow through the TDD

---

## 1. Design Phase: Product Design Document

### Product Design Document (PDD)

The **Product Design Document** defines **what** the product should be. The specific document type and naming depends on the project type:

- **Games**: GDD (Game Design Document) - see `workflow-game.md` for details
- **Applications**: PRD (Product Requirements Document) - see `workflow-application.md` for details

**Blueprint**: All design documents must follow the structure defined in the appropriate blueprint file.



### Delta Documents

For version upgrades, we create **delta documents** that track changes between design document versions. These are maintained by the **Delta Agent** (`agents/deltas/delta_agent.yaml`):

- **Delta YAML files** (`agents/deltas/<pdd>_v*_to_v*.yaml`) – Structured list of changes (ADD, MODIFY, REMOVE) with delta IDs, descriptions, design document references, importance, and status
- **Delta Journal** (`agents/deltas/delta_journal.md`) – Append-only journal tracking delta extraction, refinement, and critique iterations

**Delta Agent Process:**
- **Extract mode**: Identifies changes between design document versions (features, systems, entities, protocol changes)
- **Refine mode**: Improves descriptions, splits/merges deltas, marks as REVIEWED
- **Critique mode**: Adversarial check for missing deltas, invalid entries, redundancies

**Purpose**: Delta documents explicitly describe what changed from the previous design document version, providing traceability for planning and implementation.

### Gap Documents

For version upgrades, we create **gap documents** that identify missing implementation details in the TDD compared to design document requirements. These are maintained by the **Gap Agent** (`agents/gaps/gap_agent.yaml`):

- **Gap YAML files** (`agents/gaps/tdd_v*_to_<pdd>_v*.yaml`) – Structured list of gaps (MISSING, OUTDATED, INCOMPLETE) with gap IDs, implementation specifications, TDD/design document references, priority, and status
- **Gap Journal** (`agents/gaps/gap_journal.md`) – Append-only journal tracking gap extraction, refinement, and critique iterations

**Gap Agent Process:**
- **Extract mode**: Identifies implementation details missing from TDD (algorithms, constants, patterns, technical debt)
- **Refine mode**: Improves descriptions with specific implementation details, removes redundant gaps
- **Critique mode**: Adversarial check for redundancy with delta analysis, actionability, missing gaps

**Key Principle**: Gap analysis must NOT duplicate delta analysis. Gaps should only exist if they provide specific implementation details (algorithms, values, patterns) beyond what deltas describe.

**Purpose**: Gap documents identify actionable TDD improvements needed to support design document requirements, focusing on implementation details that the design document doesn't specify but TDD should document.

---

## 2. Delta and Gap Analysis (with AI)

When upgrading between versions (e.g., v0 to v1), we use AI agents to analyze and document changes:

### Delta Agent

The **Delta Agent** (`agents/deltas/delta_agent.yaml`) maintains delta documents that track changes between design document versions:

- **Purpose**: Identify what changed in the design document (features added, modified, or removed)
- **Inputs**: Old design document, new design document, existing delta file, delta journal
- **Outputs**: Updated delta YAML file, delta journal entries
- **Modes**: 
  - `extract`: Initial delta generation from design document comparison
  - `refine`: Improve descriptions, split/merge deltas, mark as REVIEWED
  - `critique`: Adversarial check for missing deltas, invalid entries, redundancies
- **Key Principle**: Design document is always the source of truth; delta file is derived

### Gap Agent

The **Gap Agent** (`agents/gaps/gap_agent.yaml`) maintains gap documents that identify missing implementation details in the TDD:

- **Purpose**: Identify what's missing in the TDD compared to design document requirements (algorithms, constants, patterns, technical debt)
- **Inputs**: TDD v0, design document v1, delta file, existing gap file, gap journal
- **Outputs**: Updated gap YAML file, gap journal entries
- **Modes**:
  - `extract`: Initial gap identification focusing on implementation details
  - `refine`: Improve descriptions with specific details, remove redundant gaps
  - `critique`: Adversarial check for redundancy with delta analysis, actionability
- **Key Principle**: Gap analysis must NOT duplicate delta analysis. Gaps should only exist if they provide specific implementation details beyond what deltas describe.

**When to Run**:
- After creating a new design document version (e.g., v1)
- Before planning implementation work for version upgrades
- When TDD needs to be updated to support new design document requirements

---

## 3. Architect Agent

The **Architect Agent** (`agents/architect/architect_agent.yaml`) is the single architectural authority that creates and updates TDD and SPEC/API documentation.

### Purpose

- **For initial version (v0)**: Creates the Technical Design Document (TDD) based on your Product Design Document
- **For version upgrades**: Updates TDD and SPEC/API documentation based on deltas and gaps

The Architect Agent works top-down to ensure TDD, SPEC.md, and API.md files accurately reflect the architectural structure needed to support design document requirements.

### Inputs

- `v*/<pdd>_<project>_v*.md` - Current product design document (GDD for games, PRD for applications)
- `v*/tdd_<project>_v*.md` - Current technical design document (may not exist for initial version)
- `agents/deltas/<pdd>_v*_to_v*.yaml` - Delta documents (design document changes between versions, only for upgrades)
- `agents/gaps/tdd_v*_to_<pdd>_v*.yaml` - Gap documents (TDD implementation gaps, only for upgrades)
- `agents/architect/architect_objectives.md` - Architectural objectives for each level (L0–L3)
- `server/**/SPEC.md` - Existing service/module specs (may or may not exist yet)
- `client/**/API.md` - Existing client-facing contracts (may or may not exist yet)

### Outputs

- `v*/tdd_<project>_v*.md` - Created or updated technical design document
- `server/**/SPEC.md` - Created or updated service/module specs
- `client/**/API.md` - Created or updated client-facing API documentation
- `agents/architect/architect_journal.md` - Journal entries tracking refine and critique iterations

### Process

The Architect Agent follows a **refine → critique loop** at each level (L0→L3):

#### Step 1: Delta Processing (once)

Enrich raw deltas/gaps with metadata to enable efficient selection and prioritization. Analyze each delta to extract its architectural intent, categorize by level (L0/L1/L2/L3), domain, and affected components. Tag deltas with relevant architectural concerns (e.g., scalability, security, data-ownership), identify relationships and dependencies between deltas, and summarize the architectural change in clear, structured text.

#### Step 2: Refine (per level, L0→L3)

Transform design requirements (from PDD for initial version, or from deltas/gaps for upgrades) into precise architecture documentation. Work top-down (L0→L3), analyzing impact and evaluating against level-specific objectives. Create or update TDD and SPEC/API with explicit responsibilities, boundaries, and contracts. Keep changes minimal and consistent with existing architecture.

**Refine steps:**
1. **Select Deltas** - Apply delta management strategies to select, prioritize, and batch deltas for this level (L0→L3), respecting dependencies and grouping related changes
2. **Analyze Context** - Understand what changed, why, and which architectural elements are affected at this level
3. **Evaluate Objectives** - Check against level-specific objectives (from `agents/architect/architect_objectives.md`) and identify quality attribute impacts
4. **Locate and Update** - Find target sections in TDD/SPEC/API and make precise, minimal changes that preserve consistency
5. **Document Rationale** - Include brief reasoning for architectural choices and note any lower-level dependencies (add TODO if needed)
6. **Verify Coherence** - Ensure changes align with higher-level decisions and don't introduce contradictions
7. **Document in Journal** - Record this refine entry in `agents/architect/architect_journal.md` using the Refine Entry Format

#### Step 3: Critique (per level, L0→L3)

Self-review the new/changed TDD and SPEC/API text against the design document and the rest of the TDD.

**Critique checks:**
- Are there any obvious contradictions with existing sections?
- Did you accidentally introduce new behavior not requested by deltas/gaps?
- Does this change force an update at a lower level? If yes, add a note/TODO in the relevant section instead of silently assuming it.

**Critique actions:**
- If the change is noisy or unclear → simplify and tighten once
- If a delta/gap cannot truly be resolved at this level → note this clearly in the TDD section (e.g., "Requires L2/L3 detail") and stop pushing
- Document critique entry in `agents/architect/architect_journal.md` using the Critique Entry Format

### Level Semantics (L0–L3)

**L0 – System Landscape:**
- Focus: System landscape, services, external systems, main channels, high-level data and control flows
- Allowed edits: TDD sections describing system overview/architecture diagrams, high-level references in SPEC/API

**L1 – Service / Bounded Context Level:**
- Focus: Individual services/bounded contexts, responsibilities, owned data, interactions with other services
- Allowed edits: TDD sections describing each service, service-level SPEC files (`server/**/SPEC.md`)

**L2 – Service-Internal Module Level:**
- Focus: Internal structure of each service, modules, layers, and how they collaborate
- Allowed edits: TDD subsections for internal modules, module-level SPEC files (`server/**/SPEC.md` sub-sections)

**L3 – Interface & Contract Level:**
- Focus: Interface & contract level, APIs, messages, schemas, and error behavior
- Allowed edits: Client `**/API.md` files, protocol/contract parts of SPEC.md, TDD contract sections

### Constraints

- **Do NOT edit**: Design documents (`v*/<pdd>_v*.md`), `agents/gaps/tdd_v*_to_<pdd>_v*.yaml`, `agents/deltas/<pdd>_v*_to_v*.yaml`, or code
- **Only edit**: `v*/tdd_<project>_v*.md`, `server/**/SPEC.md`, `client/**/API.md`
- **Top-down approach**: Work L0→L3, ensuring higher-level decisions inform lower-level details
- **Minimal changes**: Keep changes minimal and consistent with existing architecture

### Done Condition

The Architect Agent is done when:
- Current batch of deltas/gaps has been processed (all deltas have clear, updated description in TDD and/or SPEC/API, OR explicit note saying why they are deferred)
- New text is consistent with existing higher-level decisions and previous batches
- No obvious contradictions are visible in the focused context
- Batch processing is complete (refine + critique loop finished for this batch)

**When to Run**:
- **For initial version (v0)**: After creating the Product Design Document (section 1), before Development Plan Creation (section 4)
- **For version upgrades**: After Delta and Gap Analysis (section 2), before Development Plan Creation (section 4) and Planning Agent (section 5)
- The created/updated TDD, SPEC.md, and API.md files become inputs for Planning and Implementation agents

---

## 4. Development Plan Creation (with AI)

The **Development Plan** (`devplan_<project>_v*.md`) is created with AI assistance and serves as the master plan for implementation. It defines:

- **Paradigm highlights**: core principles (domain-first, contracts over code, test-first)
- **D-loop progression**: Per-service development loops (server: D0-D9, client: D0-D10) from workspace setup to end-to-end scenarios
- **Layer progression**: how capabilities flow through each loop
- **Quality gates**: proof requirements, test expectations, evidence standards
- **Iteration cycle**: how capabilities are broken down and implemented

**D-Loops (Development Layers):**

**IMPORTANT: D-loops are applied per service.** Each service (client, server) has its own independent progression through D-loops. Services share D0 (workspace) and D4 (protocol contracts) but have service-specific adapters.

**D-loop definitions are project-specific** - see `workflow-game.md` for game-specific D-loops or `workflow-application.md` for application-specific D-loops.

**Key principle**: You cannot advance to loop `D(n+1)` until all proofs for `Dn` are green for that service. Each service progresses through its D-loops independently.

**Example locations:**
- `v0/devplan_<project>.md` - Initial development plan
- `v1/devplan_<project>_v1.md` - v1 upgrade plan

---

## 5. Planning Agent

The **Planning Agent** (`agents/planning/planning_agent.yaml`) creates detailed implementation plans for each D-loop segment.

### Inputs

- `v*/devplan_<project>_v*.md` - Master development plan
- `v*/<pdd>_<project>_v*.md` - Product design document (must follow appropriate blueprint)
- `v*/tdd_<project>_v*.md` - Technical design document (must follow `blueprint/tdd-blueprint.md`) - **Updated by Architect Agent**
- `agents/deltas/<pdd>_v*_to_v*.yaml` - Delta documents (design document changes between versions)
- `agents/gaps/tdd_v*_to_<pdd>_v*.yaml` - Gap documents (TDD implementation gaps)
- `utils/test_lables.md` - Test labeling conventions
- `server/**/SPEC.md` - Service/module specs (updated by Architect Agent)
- `client/src/*/API.md` - Client-side API documentation files (updated by Architect Agent)

### Process

1. **Choose next D-loop** to advance based on devplan sequencing (per service: server or client)
2. **Read all relevant documents** (devplan, design document, TDD, delta files, gap files, test labels, package specs, API.md files)
3. **Consider delta and gap documents**:
   - Deltas identify what changed in the design document (what features to implement)
   - Gaps identify what's missing in TDD (implementation details to add)
   - Use both to understand scope and technical requirements
4. **Respect TDD as source of truth**:
   - Do not invent new packages or core types not present in the TDD
   - Use the package and type locations defined by the TDD when planning CUs
   - If missing concepts are discovered (e.g., new entity type, new domain primitive), the change must be made in the TDD and package spec first, not designed ad-hoc in plan.md
5. **Create workspace** at `development_v*/<loop>/<yyyy-mm-dd>_<slug>/` (specify which service: server or client)
6. **Write plan.md** with:
   - Loop goals and prerequisites (traceable to devplan)
   - 3–7 outcome-oriented steps
   - **CU List** (Change Unit List) with:
     - CU IDs (e.g., `cu/g1-<feature-name>`)
     - Titles (short behavior descriptions)
     - Loop targets
     - Step references
     - **kind**: `core` | `feature` | `refactor` (see Key Concepts → Change Units)
     - **subsystem**: See project-specific workflows for subsystem types
     - **tdd_refs**: (optional) list of TDD sections being implemented
     - Files allowlists
     - Proof labels (see project-specific workflows for examples)
     - Status (BACKLOG | NEXT | IN_PROGRESS | DONE)
   - Acceptance criteria with required evidence

### Outputs

- `development_v*/<loop>/<date>_<slug>/plan.md`

### Key Rules

- **No CU marked NEXT initially** - Planning agent only creates the backlog
- Steps must map to loop goals from devplan
- CU rows must include proof labels for test requirements
- Prerequisites must reference existing v0/v1 tests that must stay green

---

## 6. Implementation Agent

The **Implementation Agent** (`agents/implementation/implementation_agent.yaml`) executes CUs (Change Units) from the plan, implementing features test-first.

### Inputs

- `development_v*/<loop>/<slug>/plan.md` - The plan created by Planning Agent
- `v*/devplan_<project>_v*.md` - Master development plan
- `v*/<pdd>_<project>_v*.md` - Product design document (must follow appropriate blueprint)
- `v*/tdd_<project>_v*.md` - Technical design document (must follow `blueprint/tdd-blueprint.md`) - **Updated by Architect Agent**
- `agents/deltas/<pdd>_v*_to_v*.yaml` - Delta documents (design document changes between versions)
- `agents/gaps/tdd_v*_to_<pdd>_v*.yaml` - Gap documents (TDD implementation gaps)
- `utils/test_lables.md` - Test labeling conventions
- `cuplan.md` - CU planning guidelines (if exists, at root level)
- `server/**/SPEC.md` - Service/module specs (updated by Architect Agent)
- `client/src/*/API.md` - Client-side API documentation files (updated by Architect Agent)

### Lifecycle

#### Step 1: Find NEXT CU
- Read `plan.md` and find the CU marked `NEXT` in the CU List
- Record its `loop:` target and version context

#### Step 2: Create Branch
- Create branch: `cu/<loop>-<cu-slug>-<unique>`

#### Step 3: Create Implementation Plan
- Read all relevant documents
- Create `development_v*/<loop>/<slug>/cu_<cu-slug>/<cu-slug>-implementation_plan.md` with:
  - Tasks breakdown
  - Files allowlist (which files can be modified)
  - Target tests (which tests must pass)
  - Evidence fields (where to record test results)

#### Step 4: Wait for Approval
- **Manual gate**: Implementation plan must be approved before coding

#### Step 5: Test-First Development
- Add or update tests labeled with the CU's loop proof requirements
- Tests must be written before implementation

#### Step 6: Implement
- Write minimal code within allowlists to satisfy new/updated tests
- Follow test-first principles

#### Step 7: Verify
- Run labeled test suites
- Record command + result in evidence log
- Ensure all required proofs pass

#### Step 8: Update Plan
- Set CU status to `DONE` in `plan.md`
- Include evidence links
- Select a new single `NEXT` CU (only one at a time)

#### Step 9: Commit and Merge
- Commit changes
- Open PR with template (includes goal, loop, acceptance checks, tests plan, files touched, evidence)
- Secure approvals
- Merge to main

### Outputs

- `development_v*/<loop>/<slug>/cu_<cu-slug>/<cu-slug>-implementation_plan.md`
- Code changes in allowed files
- Updated `plan.md` with CU status changes

### Key Principles

- **Test-first**: Always write tests before implementation
- **One CU at a time**: Only one CU marked `NEXT` in the plan
- **Evidence-driven**: All test results must be recorded
- **Allowlist enforcement**: Only modify files in the CU's allowlist
- **Proof labels**: Tests must be labeled according to loop requirements
- **No spec modifications**: Implementation Agent must not modify the TDD or package spec docs
  - If the spec is missing or ambiguous for a CU, treat that as a planning/design problem (i.e., "spec missing; return to Planning Agent")
  - Do not improvise new core types or behavior – structural changes must flow through the TDD first

---

## 7. Workflow Summary

```
┌─────────────────────────────────────────────────────────────┐
│ 1. Design Phase                                              │
│    └─ Create Product Design Document (GDD/PRD)              │
│       (TDD is created/updated by Architect Agent)             │
└─────────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────────┐
│ 2. Delta & Gap Analysis (Version Upgrades Only)             │
│    ├─ Delta Agent: Extract design document changes (v0→v1)  │
│    └─ Gap Agent: Identify TDD implementation gaps            │
│    (Skip for initial version - go directly to step 3)        │
└─────────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────────┐
│ 3. Architect Agent                                           │
│    ├─ Initial Version:                                       │
│    │  ├─ Reads: Design doc, objectives                       │
│    │  └─ Creates: TDD, SPEC.md, API.md (top-down L0→L3)     │
│    ├─ Version Upgrades:                                      │
│    │  ├─ Reads: Design doc, TDD, deltas, gaps, objectives   │
│    │  └─ Updates: TDD, SPEC.md, API.md (top-down L0→L3)      │
│    └─ Process: Refine → Critique loop per level               │
│       (TDD/SPEC/API become source of truth for planning)     │
└─────────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────────┐
│ 4. Development Plan (with AI)                                 │
│    └─ Create devplan_<project>_v*.md                        │
│       (Defines D-loops, capabilities, quality gates)         │
└─────────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────────┐
│ 5. Planning Agent                                            │
│    ├─ Reads: devplan, design doc, TDD (updated), SPEC/API   │
│    ├─ Chooses next D-loop to advance                         │
│    └─ Creates: plan.md with CU List                          │
│       (No CU marked NEXT initially)                           │
└─────────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────────┐
│ 6. Implementation Agent                                       │
│    ├─ Finds NEXT CU from plan.md                             │
│    ├─ Creates implementation_plan.md                        │
│    ├─ WAIT: Manual approval gate                             │
│    ├─ TEST-FIRST: Write tests with proof labels              │
│    ├─ IMPLEMENT: Minimal code to pass tests                   │
│    ├─ VERIFY: Run tests, record evidence                     │
│    ├─ UPDATE: Mark CU DONE, select next NEXT                 │
│    └─ COMMIT: PR with evidence, merge                        │
└─────────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────────┐
│ 7. Iterate                                                    │
│    └─ Repeat until all CUs in loop are DONE                  │
│    └─ Advance to next D-loop when all proofs green           │
└─────────────────────────────────────────────────────────────┘
```

Workflow summary as text:

1. Design Phase:
   - Create Product Design Document (GDD for games, PRD for applications)
   - TDD is created/updated by Architect Agent (not written manually)

2. Delta & Gap Analysis (Version Upgrades Only):
   - Delta Agent: Extract design document changes between versions
   - Gap Agent: Identify TDD implementation gaps
   - Skip for initial version - proceed directly to Architect Agent

3. Architect Agent:
   - **Initial Version**: Reads design document and objectives; Creates TDD, SPEC.md, API.md
   - **Version Upgrades**: Reads design document, TDD, deltas, gaps, objectives; Updates TDD, SPEC.md, API.md
   - Works top-down (L0→L3) with Refine → Critique loop per level
   - TDD/SPEC/API become source of truth for planning

4. Development Plan Creation (with AI):
   - Create devplan_<project>_v*.md
   - Defines D-loops, capabilities, quality gates
5. Planning Agent:
   - Reads: devplan, design document, TDD (updated), SPEC/API (updated), deltas, gaps, test labels
   - Chooses next D-loop to advance
   - Creates: plan.md with CU List
   - No CU marked NEXT initially
6. Implementation Agent:
   - Finds NEXT CU from plan.md
   - Creates implementation_plan.md
   - WAIT: Manual approval gate
   - TEST-FIRST: Write tests with proof labels
   - IMPLEMENT: Minimal code to pass tests
   - VERIFY: Run tests, record evidence
   - UPDATE: Mark CU DONE, select next NEXT
   - COMMIT: PR with evidence, merge
7. Iterate:
   - Repeat until all CUs in loop are DONE
   - Advance to next D-loop when all proofs green

---

## 8. Key Concepts

### Change Units (CUs)
- Small, testable units of work
- Each CU has a clear goal, allowlist, and proof requirements
- Only one CU marked `NEXT` at a time
- Status flow: BACKLOG → NEXT → IN_PROGRESS → DONE

#### CU Classification

Each CU is classified by **kind** and **subsystem**:

**kind:**
- **`core`** – Evolves the primitives of a package (e.g., new entity fields, new domain primitive, new validation rules). Must update the corresponding package spec, and only operates inside that package's core code.
- **`feature`** – Composes existing primitives to implement product features. Must not introduce new core entities, domain formulas, or validation state machines. Cannot change package specs.
- **`refactor`** – Structural changes (renames, moves, mechanical cleanup) with no external behavior change. Tests must remain green; no new primitives.

**subsystem:**
- See project-specific workflows (`workflow-game.md` or `workflow-application.md`) for subsystem types and examples

**Guardrail**: Feature CUs are not allowed to define new domain state structs, domain formulas, or validation state machines. Any such change must be done as a Core CU for the relevant package and reflected in the TDD + package spec.

### Proof Labels
Tests are labeled to indicate their scope and purpose:
- `scope:unit|contract|integration|e2e|perf`
- `loop:g0-work|g1-<domain>|g2-<validation>|g3-orch|g4-proto|g5-adapter|g6-client|g7-ops|g8-scenario` (see project-specific workflows for exact loop names)
- `layer:<domain>|contract|server|client|ops|infra|scenario` (see project-specific workflows for exact layer names)
- `net:proto:v<major>` - Protocol version
- `dep:<dependency>` - Integration dependencies
- `double:<test-double>` - Test doubles used

### Inside-Out Development

**Server Service:**
- Start with entities (D1)
- Add domain logic (D2)
- Add validation/rules (D3)
- Define contracts (D4)
- Add session orchestration (D5)
- Add context management (D6)
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

### Test-First
- Always write tests before implementation
- Tests define the behavior
- Implementation satisfies the tests
- Evidence must be recorded

### Versioning
- Each version has its own design document, TDD, and devplan
- **Delta documents** track design document changes between versions (what changed)
- **Gap documents** track TDD implementation gaps compared to design document requirements (what's missing)
- v0 is the baseline; v1 extends it
- v1 work is isolated in `development_v1/`

---

## 9. File Structure

```
workflow/
├── workflow.md                    # This file (base workflow)
├── workflow-game.md               # Game-specific extensions
└── workflow-application.md         # Application-specific extensions

blueprint/
├── gdd-blueprint.md               # GDD structure template (games)
├── prd-blueprint.md               # PRD structure template (applications)
└── tdd-blueprint.md               # TDD structure template

agents/
├── architect/
│   ├── architect_agent.yaml       # Architect agent configuration
│   ├── architect_objectives.md    # Architectural objectives (L0–L3)
│   ├── architect_journal.md       # Architect refine/critique journal
│   └── client_architect_journal.md # Client-specific architect journal
├── deltas/
│   ├── delta_agent.yaml           # Delta Agent configuration
│   ├── delta_journal.md           # Delta extraction/refinement journal
│   └── <pdd>_v*_to_v*.yaml        # Design document changes (structured)
├── gaps/
│   ├── gap_agent.yaml             # Gap Agent configuration
│   ├── gap_journal.md             # Gap extraction/refinement journal
│   └── tdd_v*_to_<pdd>_v*.yaml    # TDD gaps for design document (structured)
├── planning/
│   └── planning_agent.yaml       # Planning agent configuration
└── implementation/
    └── implementation_agent.yaml   # Implementation agent configuration

utils/
└── test_lables.md                 # Test labeling conventions

v0/
├── <pdd>_<project>_v0.md          # v0 Product Design Document
├── tdd_<project>_v0.md            # v0 Technical Design Document
└── devplan_<project>.md           # v0 Development Plan

v1/
├── <pdd>_<project>_v1.md          # v1 Product Design Document
├── tdd_<project>_v1.md            # v1 Technical Design Document
└── devplan_<project>_v1.md        # v1 Development Plan

development_v1/
└── <loop>/
    └── <date>_<slug>/
        ├── plan.md                # Plan created by Planning Agent
        └── cu_<cu-slug>/
            └── <cu-slug>-implementation_plan.md  # Created by Implementation Agent

client/src/
├── core/API.md                    # Core package API reference
├── ui/API.md                      # UI package API reference
└── ... (other API.md files as needed)

server/
└── ... (SPEC.md files as needed)
```

---

## 10. Best Practices

1. **Never skip proofs**: Each loop must have green tests before advancing
2. **One CU at a time**: Only mark one CU as `NEXT` in the plan
3. **Test-first always**: Write tests before implementation code
4. **Record evidence**: Document all test results and commands
5. **Respect allowlists**: Only modify files in the CU's allowlist
6. **Version isolation**: Keep v1 work separate from v0
7. **Manual gates**: Wait for approval before implementing
8. **Clear PRs**: Include goal, loop, tests, files, and evidence in PRs
9. **Follow blueprints**: Design documents must follow appropriate blueprint structure
10. **Architect updates TDD/SPEC/API**: The Architect Agent is the single authority for updating TDD, SPEC.md, and API.md files. Planning and Implementation agents must respect these as source of truth.
11. **Single source of truth for domain entities**: All domain entities live in the appropriate domain package and are defined in the model spec. No parallel entity structs elsewhere.
12. **Core logic only in package cores**: Domain logic in the appropriate domain package. Higher layers (session, context, proto, client) compose but don't re-implement these.

---

This workflow ensures systematic, testable, and maintainable development with clear separation of concerns and explicit quality gates at each layer.
