# Development Plan Blueprint
**Purpose**: Template for creating development plans for any project based on TDD and Product Design Document (PDD)

---

## How to Use This Blueprint

1. **Read the TDD** to understand the system architecture and identify layers
2. **Read the Product Design Document (PDD)** to understand product requirements and scope
   - For games: Read the Game Design Document (GDD)
   - For applications: Read the Product Requirements Document (PRD)
3. **Derive D-loops** from the TDD's architecture layers (Section 4 & 5)
4. **Fill in all placeholders** marked with `[PLACEHOLDER]` or `[INSTRUCTIONS]`
5. **Customize sections** based on your project's specific needs

---


## 0) Purpose

[INSTRUCTIONS: Write 1-2 paragraphs describing the development approach. Extract key principles from TDD Section 1 (Executive Summary) and Section 8 (Design Patterns). Focus on: inside-out development, layer isolation, determinism guarantees, and testing strategy.]

---

## 2) Layer Progression & Proofs (D‑loops)

**IMPORTANT: D-loops are applied per service.** Each service (client, server, admin, worker, etc.) has its own independent progression through D-loops. Create a separate D-loop table for each service identified in TDD Section 4 (System Architecture). Services may share some D-loops (e.g., D0 workspace, D4 protocol contracts) but will have service-specific adapters (e.g., D5 client rendering, D5 server match orchestration).

[INSTRUCTIONS: **This is the most important section to derive from TDD.** 
1. First, identify all services from TDD Section 4 (System Architecture)
2. For each service, analyze TDD Section 5 (Detailed Design) to identify the architectural layers within that service
3. Create one D-loop table per service, with one row per architectural layer, ordered from innermost (pure logic) to outermost (IO/adapters)
4. Add rows dynamically based on what you find in the TDD for each service]

| Loop | Focus | Outputs | Proof (expected labels) |
| --- | --- | --- | --- |
| D0 Workspace | Repo + tool bootstrap | mono repo, Docker setup, basic lint/test, local dev tools | `scope:contract loop:d0-work layer:infra` |
| D1 [Pure Layer Name] | [Description from TDD Section 5.X] | [Key outputs from TDD] | `scope:unit loop:d1-[name] layer:[layer]` |
| D3 [Orchestration Layer Name] | [Description from TDD] | [Key outputs from TDD] | `scope:unit loop:d3-[name] layer:[layer] double:fake-io` |
| D4 Protocol & Contracts | [Communication/Interface contracts] | [Protocol format from TDD Section 5.X], [event topics if applicable], [replay format if applicable] | `scope:contract loop:d4-proto layer:contract` |
| D5 [Service Adapter Name] | Real IO honoring ports | [Service description from TDD], [dependencies from TDD] | `scope:integration loop:d5-[name] layer:[layer] dep:[deps]` |
| D6 Observability & Ops | Cross‑cutting hardening | logs, metrics, traces, health, [replay tooling if applicable] | `scope:integration loop:d7-ops layer:ops` |
| D7 Scenarios, Perf, Anti‑Cheat | Multi‑part confirmation | [golden replays if applicable], soak tests, chaos net, sanity guards | `scope:e2e loop:d8-scenario layer:scenario`, `scope:perf loop:d8-scenario layer:ops` |

**How to derive D-loops from TDD architecture (step-by-step algorithm):**

1. **Read TDD Section 4 (System Architecture)** - Identify all major services (e.g., client, server, admin, worker)
2. **For each service, repeat steps 3-7:**
3. **Read TDD Section 5 (Detailed Design)** - For each subsystem in Section 5 that belongs to this service, classify it:
   - **Pure logic (no IO, no external deps)**: Mark as candidate for D1, D2, etc.
   - **Orchestration/coordination**: Mark as candidate for D3
   - **Interface/contract definitions**: Mark as candidate for D4
   - **Service adapters (IO, network, storage, UI)**: Mark as candidate for D5, D6, etc.
4. **Build dependency graph**: For each subsystem within this service, identify what it depends on
5. **Order by dependency direction (inner-to-outer)**:
   - Start with subsystems that have no dependencies → these are innermost (D1, D2, ...)
   - Then subsystems that depend only on innermost → next layer
   - Continue until all subsystems are ordered
6. **Assign D-numbers** (per service):
   - **D0**: Always workspace (shared across all services, typically done once)
   - **D1, D2, ...**: Pure logic layers in dependency order (add D2, D3, etc. if you find multiple pure layers within this service)
   - **D3**: First orchestration layer found (or next available number if no pure layers)
   - **D4**: Protocol/contracts layer (may be shared across services that communicate)
   - **D5, D6, ...**: Service-specific adapters (e.g., D5 client rendering, D5 server match orchestration) - one D-loop per major adapter within this service
   - **D7**: Observability (always second-to-last, applies to this service)
   - **D8**: Scenarios (always last, applies to this service)
7. **Create table rows for this service**: Add one row per D-loop you identified for this service, using the template above
8. **Repeat for next service**: Go back to step 2 for each remaining service

**Determining number of pure layers (D1, D2, ...) within a service:**
- Within each service, count distinct pure logic subsystems in TDD Section 5 that have no dependencies
- If this service has 1 pure layer → use D1 only
- If this service has 2 pure layers → use D1 and D2
- If this service has 3+ pure layers → use D1, D2, D3, etc. (skip D3 if it's reserved for orchestration)
- **Rule**: Only create D-loops for layers that actually exist in your TDD for this service
- **Note**: Different services may have different numbers of pure layers

**Determining number of service adapters (D5, D6, ...) within a service:**
- Within each service, count distinct adapter/IO subsystems (e.g., rendering, networking, storage, UI)
- Each major adapter within a service gets its own D-loop (D5, D6, etc.)
- If a service has 1 adapter → use D5 only
- If a service has 2 adapters → use D5 and D6
- If a service has 3+ adapters → use D5, D6, D7, etc. (but reserve the last D-number for observability, and D8 for scenarios)
- **Rule**: Only create D-loops for adapters that actually exist in your TDD for this service
- **Note**: Different services will have different adapters (e.g., client has D5 rendering, server has D5 match orchestration)

**Key principles (applied per service):**
- **D0**: Always workspace/bootstrap (infrastructure setup, typically shared across services)
- **D1, D2, ...**: Pure logic layers within this service (no IO, no external dependencies) - look for "pure functions", "no dependencies", "testable in isolation" in TDD
- **D3 (or next available)**: Orchestration/coordination layers within this service - look for components that coordinate other layers, manage state transitions, or handle event loops
- **D4**: Interface/contract definitions - look for "protocol", "messages", "schema", "API", "interface" in TDD (may be shared across communicating services)
- **D5, D6, ...**: Service-specific adapters (one D-loop per major adapter within this service) - look for components that interact with external systems (e.g., rendering, networking, storage, UI)
- **D7**: Cross-cutting observability for this service (logs, metrics, traces, health)
- **D8**: End-to-end validation for this service (scenarios, performance, anti-cheat)

**Advancing to loop `D(n+1)` requires all proofs for `Dn` to be green and recorded for that service. Each service progresses through its D-loops independently.**

---
