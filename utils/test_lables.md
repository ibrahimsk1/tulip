# Test Labels

## Tooling
- **test_runner:** Ginkgo v2 + Gomega
- **containers:** testcontainers-go
- **contracts:** Buf + Protobuf + Pact Go
- **telemetry:** OpenTelemetry + Tracetest
- **coverage:** GOCOVERDIR merge + go tool cover
- **replay:** deterministic state-hash verifier

## Test Doubles
- **fake** — deterministic replacement (fake clock, fake RNG, in-memory ECS)
- **mock** — verifies call order and arguments
- **spy** — records side-effects or timing

**When to use:**
- Remove IO/nondeterminism → `fake`
- Verify tick ordering/command sequence → `mock`
- Observe telemetry/state mutation → `spy`

## Label Dimensions

Format: `dimension:value` (lowercase, kebab-case)

| Dimension | Purpose | Examples |
|-----------|---------|----------|
| `scope:` | Test size/boundary | `scope:unit`, `scope:integration`, `scope:e2e`, `scope:contract`, `scope:perf` |
| `loop:` | D-loop being proven | `loop:d0-work`, `loop:d1-[domain]`, `loop:d2-[logic]`, `loop:d3-orch`, `loop:d4-proto`, `loop:d5-adapter`, `loop:d6-client`, `loop:d7-ops`, `loop:d8-scenario` |
| `layer:` | System layer | `layer:[domain]`, `layer:server`, `layer:client`, `layer:contract`, `layer:ops`, `layer:infra`, `layer:scenario` |
| `b:` | Behavior under test | `b:[feature-name]`, `b:[operation-name]`, `b:[workflow-name]`, `b:ws-broadcast` |
| `r:` | Risk/criticality | `r:high`, `r:medium`, `r:low` |
| `double:` | Doubles in use | `double:fake`, `double:mock`, `double:spy`, `double:bot` |
| `dep:` | Real dependency | `dep:none`, `dep:redis`, `dep:postgres`, `dep:ws`, `dep:pixi` |
| `brick:` | Fine-grain focus (optional) | `brick:[component-name]`, `brick:[subsystem-name]` |
| `route:` | Network path (optional) | `route:ws:/[endpoint]`, `route:http:/[endpoint]` |
| `scn:` | Scenario identifier (optional) | `scn:[scenario-name].v1`, `scn:replay.verify.001` |

## Rules
- Always include exactly one `scope:` and one `loop:` per test
- Pure logic layers (D1–D2) tests must be deterministic (`dep:none`)
- Orchestration (D3) tests must assert order + command idempotency
- Observability (D7) tests must assert logs/traces/metrics, not just state
- Scenario (D8) tests must attach replay or perf evidence files

## Loop Expectations

| Loop | Proof Goal | Typical Tests |
|------|------------|---------------|
| `loop:d0-work` | Workspace bootstrap | CI config, task runner, lint/test harness |
| `loop:d1-[domain]` | Deterministic core logic | domain calculations, business logic, data transformations |
| `loop:d2-[logic]` | Entity logic & business rules | state transitions, validation, business constraints |
| `loop:d3-orch` | Orchestration & coordination | workflow orchestration, command queue, state management |
| `loop:d4-proto` | Protocol and contract stability | schema diff, consumer/provider, message replay |
| `loop:d5-adapter` | Real IO & network adapters | WebSocket, HTTP, database, external APIs |
| `loop:d6-client` | Client rendering & input | UI rendering, input handling, state synchronization |
| `loop:d7-ops` | Observability and ops health | latency, resource usage, trace/metric checks |
| `loop:d8-scenario` | Cross-system validation | end-to-end scenarios, chaos tests, perf & security |

## Test-First Checklist
1. Select target `loop:d*` matching CU plan
2. Declare `scope:` and `layer:`
3. Add descriptive `b:` (behavior) and `r:` (risk)
4. Include `double:` and `dep:` indicators
5. Optionally add `brick:`, `route:`, or `scn:` for traceability
6. Write test skeleton with labels before implementing code
7. Ensure CI filters by labels to execute required suites

## Examples

**Example A — Deterministic Core Logic**
```go
It("calculates [operation] correctly", Label(
  "scope:unit",
  "loop:d1-[domain]",
  "layer:[domain]",
  "b:[operation-name]",
  "r:medium",
  "double:fake",
  "dep:none",
))
```

**Example B — Network Adapter**
```go
It("broadcasts state delta to all clients", Label(
  "scope:integration",
  "loop:d5-adapter",
  "layer:server",
  "b:ws-broadcast",
  "r:high",
  "dep:ws",
  "double:spy",
))
```

**Example C — End-to-End Scenario**
```go
It("validates [scenario] after [duration]", Label(
  "scope:e2e",
  "loop:d8-scenario",
  "layer:scenario",
  "b:[scenario-name]",
  "r:high",
  "double:bot",
  "dep:ws",
  "scn:[scenario-name].v1",
))
```