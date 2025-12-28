# Application Development Workflow

> **Note**: This document extends `workflow.md`. Read the base workflow first for common concepts.

This document provides application-specific details for the development workflow. For common concepts (agents, CUs, test-first, etc.), see `workflow.md`.

---

## Design Document: PRD (Product Requirements Document)

### Product Requirements Document (PRD)

The **Product Requirements Document** (`prd_<project>_v*.md`) defines **what** the application should be: goals, workflows, scope, interactions, domain entities, user experience, UI/UX, success/failure states, and feature mechanics.

**Blueprint**: All PRDs must follow the structure defined in `blueprint/prd-blueprint.md`.

**Example locations:**
- `v0/prd_myapp_v0.md` - Initial minimal viable product
- `v1/prd_myapp_v1.md` - Multi-tenant upgrade

### Delta Documents (Application-Specific)

For version upgrades, we create **delta documents** that track changes between PRD versions. These are maintained by the **Delta Agent** (`agents/deltas/delta_agent.yaml`):

- **Delta YAML files** (`agents/deltas/prd_v*_to_v*.yaml`) – Structured list of changes (ADD, MODIFY, REMOVE) with delta IDs, descriptions, PRD references, importance, and status

**Delta Agent Process:**
- **Extract mode**: Identifies changes between PRD versions (feature mechanics, systems, domain entities, API changes)

**Purpose**: Delta documents explicitly describe what changed from the previous PRD version, providing traceability for planning and implementation.

### Gap Documents (Application-Specific)

For version upgrades, we create **gap documents** that identify missing implementation details in the TDD compared to PRD requirements:

- **Gap YAML files** (`agents/gaps/tdd_v*_to_prd_v*.yaml`) – Structured list of gaps (MISSING, OUTDATED, INCOMPLETE) with gap IDs, implementation specifications, TDD/PRD references, priority, and status

**Purpose**: Gap documents identify actionable TDD improvements needed to support PRD requirements, focusing on implementation details that PRD doesn't specify but TDD should document.

---

## D-Loop Definitions (Application-Specific)

**IMPORTANT: D-loops are applied per service.** Each service (client, server) has its own independent progression through D-loops. Services share D0 (workspace) and D4 (protocol contracts) but have service-specific adapters.

### Server Service D-loops (Applications)

1. **D0 Workspace**: Repo, tools, CI/CD setup
2. **D1 Entities**: Core data types for domain logic (User, Order, Product, Account)
3. **D2 Domain Logic**: Business logic and calculations (pricing, transformations, calculations)
4. **D3 Validation**: Business rules and validation (validation rules, constraints, state machines)
5. **D4 Protocol & Contracts**: Communication/interface contracts (API schemas, validation)
6. **D5 Session**: Request handling, transaction management, workflow orchestration
7. **D6 Context Management**: Resource lifecycle, multi-tenant coordination, workspace management
8. **D7 Transport**: HTTP/gRPC handler and message routing
9. **D8 Observability & Ops**: Cross-cutting hardening (logs, metrics, traces, health)
10. **D9 Scenarios, Perf, Security**: End-to-end validation (integration tests, chaos tests, performance, security audits)

### Client Service D-loops (Applications)

1. **D0 Workspace**: Repo, tools, CI/CD setup
2. **D1 State**: Client-side application state management
3. **D2 App Orchestrator**: Coordinates client subsystems (net, render, ui, input, domain)
4. **D4 Protocol & Contracts**: Communication/interface contracts (shared with server)
5. **D5 Network**: API client, WebSocket client, connection pooling
6. **D6 Rendering**: React/Vue rendering, canvas rendering, or native UI
7. **D7 UI**: User interface components (dashboard, forms, navigation, widgets)
8. **D8 Input**: Keyboard, mouse, touch, form input handling
9. **D9 Observability & Ops**: Cross-cutting hardening (console logging, error handling)
10. **D10 Scenarios, Perf**: Multi-part confirmation (client integration tests, UI tests, E2E tests)

**Key principle**: You cannot advance to loop `D(n+1)` until all proofs for `Dn` are green for that service. Each service progresses through its D-loops independently.

---

## Subsystem Classifications (Application-Specific)

Each CU is classified by **subsystem**:

**subsystem:**
- `model` – Domain entities/data models (User, Order, Product, Account, Address)
- `domain` – Business logic and calculations (pricing, calculations, transformations)
- `validation` – Business rules and validation (validation rules, constraints, state machines)
- `orch` – Orchestration (session, context, workflow, command queue)
- `proto` – Protocol (API schemas, validation)
- `client` – Client-side rendering, input, state management
- `ops` – Observability (logs, metrics, traces, health)
- `scenario` – End-to-end tests, integration tests, chaos tests

### CU Examples (Application-Specific)

**kind:**
- **`core`** – Evolves the primitives of a package (e.g., new entity fields, new business logic primitive, new validation rules). Must update the corresponding package spec, and only operates inside that package's core code.
  - Examples: new User fields, new pricing formula, new validation constraint
- **`feature`** – Composes existing primitives to implement product features (e.g., user dashboard, reporting). Must not introduce new core entities, business logic formulas, or validation state machines. Cannot change package specs.
  - Examples: user dashboard UI, reporting feature, notification system
- **`refactor`** – Structural changes (renames, moves, mechanical cleanup) with no external behavior change. Tests must remain green; no new primitives.

**Guardrail**: Feature CUs are not allowed to define new domain state structs, business logic formulas, or validation state machines. Any such change must be done as a Core CU for the relevant package and reflected in the TDD + package spec.

---

## Proof Labels (Application-Specific)

Tests are labeled to indicate their scope and purpose:
- `scope:unit|contract|integration|e2e|perf`
- `loop:d0-work|d1-domain|d2-validation|d3-orch|d4-proto|d5-adapter|d6-client|d7-ops|d8-scenario`
- `layer:domain|contract|server|client|ops|infra|scenario`
- `net:proto:v<major>` - Protocol version
- `dep:<dependency>` - Integration dependencies
- `double:<test-double>` - Test doubles used

**Example proof labels:**
- `scope:unit loop:d1-domain layer:domain` - Unit test for domain logic
- `scope:integration loop:d5-session layer:server` - Integration test for request handling

---

## Inside-Out Development (Application-Specific)

**Server Service:**
- Start with entities (D1)
- Add domain logic (D2)
- Add validation (D3)
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

---

## Paradigm Highlights (Application-Specific)

**Paradigm highlights**: core principles (domain-first, contracts over code, test-first)

- **domain-first**: Domain logic is the source of truth; all other layers adapt to it
- **contracts over code**: API and protocol contracts are defined before implementation
- **test-first**: Always write tests before implementation

---

## File Structure Examples (Application-Specific)

```
workflow/
└── workflow-application.md        # This file

blueprint/
└── prd-blueprint.md               # PRD structure template

v0/
├── prd_myapp_v0.md                # v0 Product Requirements Document
├── tdd_myapp_v0.md                # v0 Technical Design Document
└── devplan_myapp.md               # v0 Development Plan

v1/
├── prd_myapp_v1.md                # v1 Product Requirements Document
├── tdd_myapp_v1.md                # v1 Technical Design Document
└── devplan_myapp_v1.md            # v1 Development Plan

agents/
├── deltas/
│   └── prd_v0_to_v1.yaml         # PRD v0 to v1 changes (structured)
└── gaps/
    └── tdd_v0_to_prd_v1.yaml     # TDD v0 gaps for PRD v1 (structured)

server/internal/
├── domain/
│   ├── entities/SPEC.v1.md        # Domain entities spec
│   ├── logic/SPEC.v1.md          # Business logic spec
│   └── validation/SPEC.v1.md      # Validation rules spec
└── proto/SPEC.v1.md              # Protocol/API spec

client/src/
├── core/API.md                   # Core package API reference
├── ui/API.md                     # UI package API reference
├── input/API.md                  # Input package API reference
├── render/API.md                 # Rendering package API reference
├── net/API.md                    # Network package API reference
└── domain/API.md                 # Domain package API reference
```

---

## Best Practices (Application-Specific)

1. **Single source of truth for domain entities**: All domain entities (User, Order, Product, Account, etc.) live in `server/internal/domain/entities` and are defined in the model spec. No parallel entity structs elsewhere.

2. **Core logic only in package cores**: Business logic in `/domain/logic`, validation logic in `/domain/validation`, etc. Higher layers (session, context, proto, client) compose but don't re-implement these.

3. **API versioning**: Use versioned APIs for backward compatibility and gradual migration.

4. **Data consistency**: Ensure proper transaction boundaries and data consistency models.

---

## Architect Agent Inputs/Outputs (Application-Specific)

### Inputs
- `v*/prd_<project>_v*.md` - Current product requirements document
- `v*/tdd_<project>_v*.md` - Current technical design document
- `agents/deltas/prd_v*_to_v*.yaml` - Delta documents (PRD changes between versions)
- `agents/gaps/tdd_v*_to_prd_v*.yaml` - Gap documents (TDD implementation gaps)

### Outputs
- `v*/tdd_<project>_v*.md` - Updated technical design document
- `server/**/SPEC.md` - Updated or created service/module specs
- `client/**/API.md` - Updated or created client-facing API documentation

---

## Planning Agent Inputs (Application-Specific)

- `v*/devplan_<project>_v*.md` - Master development plan
- `v*/prd_<project>_v*.md` - Product requirements document (must follow `blueprint/prd-blueprint.md`)
- `v*/tdd_<project>_v*.md` - Technical design document (must follow `blueprint/tdd-blueprint.md`)
- `agents/deltas/prd_v*_to_v*.yaml` - Delta documents (PRD changes between versions)
- `agents/gaps/tdd_v*_to_prd_v*.yaml` - Gap documents (TDD implementation gaps)

---

## Implementation Agent Inputs (Application-Specific)

- `v*/devplan_<project>_v*.md` - Master development plan
- `v*/prd_<project>_v*.md` - Product requirements document (must follow `blueprint/prd-blueprint.md`)
- `v*/tdd_<project>_v*.md` - Technical design document (must follow `blueprint/tdd-blueprint.md`)
- `agents/deltas/prd_v*_to_v*.yaml` - Delta documents (PRD changes between versions)
- `agents/gaps/tdd_v*_to_prd_v*.yaml` - Gap documents (TDD implementation gaps)

---

This workflow ensures systematic, testable, and maintainable application development with clear separation of concerns and explicit quality gates at each layer.

