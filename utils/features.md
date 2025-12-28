# Transformation Step Features

A vocabulary for describing the cognitive complexity of transformation steps in workflow pipelines. These features help identify when steps should be split or require intermediate artifacts.

## Overview

This document defines a family of transformation-level features that measure how much "cognitive shape-shifting" each step demands. Use these metrics to:

- Tag every step with feature values
- Identify steps that score "high" on too many axes → split them or add intermediate artifacts
- Ensure step outputs align with the next step's expected abstraction level, granularity, domain, and representation

## Feature Catalog

### 1. abstraction_jump

**Description:** How far the semantic level moves between input and output.

**Levels:**
- **0 = same-layer**  
  Behavior→behavior, architecture→architecture, plan→plan.  
  *Example:* Summarizing PDD v1 into a shorter "PDD overview"

- **1 = adjacent-layer**  
  Behavior→meta-behavior, architecture→plan, plan→tasks.  
  *Example:* PDD v0 & v1 → Delta list (still in behavior space, but structured)  
  *Example:* TDD v1 → Devplan (architecture → plan)

- **2 = cross-layer**  
  Behavior→architecture, architecture→code, behavior→tests.  
  *Example:* PDD + Deltas → TDD updates  
  *Example:* PDD → test cases directly

- **3 = stacked cross-layer**  
  Multiple jumps in one go (behavior+architecture → code+tests).  
  *Example:* PDD + TDD → "generate code & tests" in a single step (the usual over-ambitious LLM fantasy)

**Guidance:** Most steps should be 0 or 1. Any step with `abstraction_jump ≥ 2` is danger territory and usually wants to be split.

---

### 2. mapping_cardinality

**Description:** How many-to-how-many relations between input concepts and output units.

**Levels:**
- **0 = 1→1**  
  One input concept → one output unit.  
  *Example:* "This single PDD requirement becomes one Delta row"

- **1 = 1→N or N→1**  
  One-to-many or many-to-one.  
  *Example:* One PDD requirement yields multiple deltas (feature behavior broken into several steps)  
  *Example:* Many PDD sentences describing the same feature → one normalized delta

- **2 = N→M**  
  Many inputs cross-linked to many outputs.  
  *Example:* Deltas (N) + TDD sections (N) → Gaps (M) where each gap may reference multiple deltas and multiple TDD sections

**Guidance:** High `mapping_cardinality` + high `abstraction_jump` = heavy cognitive step.

---

### 3. locality_span

**Description:** How "wide" you have to look to make a correct decision.

**Levels:**
- **0 = local**  
  Only need one small region (one section / function).  
  *Example:* "What changed in this one PDD section?" → local Delta extraction

- **1 = sectional**  
  Need multiple related sections in one doc.  
  *Example:* "Entities" + "Core Workflow" combined to interpret a feature change

- **2 = global (intra-doc)**  
  Need the whole document structure in mind.  
  *Example:* Ensuring Delta coverage across all PDD sections

- **3 = cross-doc**  
  Need multiple documents and their relationships.  
  *Example:* Gaps: require PDD, TDD, and Deltas together

**Guidance:** If a step has `locality_span = 3`, that's a strong signal to either:
- Introduce intermediate artifacts (e.g., trace tables), or
- Break into two passes (local then global audit)

---

### 4. temporal_span

**Description:** How many time states or versions you must juggle.

**Levels:**
- **0 = single-state**  
  No versioning; just "what is".  
  *Example:* Generate TDD v1 skeleton from scratch

- **1 = two-state diff**  
  Old vs new.  
  *Example:* Delta extraction: PDD v0 vs PDD v1

- **2 = multi-state / history**  
  Need to consider a sequence of changes or multiple versions.  
  *Example:* Upgrading v0→v1 when there was already a v-1 or v0.5 and you care about cumulative compatibility

**Guidance:** 
- Delta step is naturally `temporal_span = 1`
- Gap step can feel like 2 if you're considering "TDD v0, TDD v1 draft, and future v2"

The more temporal states, the more you want:
- Explicit IDs
- Explicit "source_version" / "target_version" fields
- Maybe separate "migration" docs

---

### 5. representation_shift

**Description:** How different the representation form is between input and output.

**Levels:**
- **0 = same representation family**  
  Text → text, table → table, YAML → YAML.  
  *Example:* TDD (markdown) → devplan (markdown outline)

- **1 = structured vs semi-structured**  
  Sectioned text ↔ table/YAML.  
  *Example:* PDD text → Delta YAML  
  *Example:* TDD text → Gap YAML

- **2 = symbolic / executable**  
  Text↔code, text↔formal constraint language, text↔queries.  
  *Example:* PDD/TDD → auto-generated tests or code

**Guidance:** 
- Delta & Gap steps are mostly `representation_shift = 1`
- Once you ask: "use this to generate tests/code" → you're at 2 and should be very suspicious

---

### 6. constraint_mode

**Description:** What role constraints play in the output: descriptive vs prescriptive vs executable.

**Levels:**
- **0 = descriptive**  
  Output just describes reality.  
  *Example:* "Here are the changes in PDD" (deltas)

- **1 = prescriptive**  
  Output says "this must now be true".  
  *Example:* Gaps that say "TDD must define this field/algorithm"  
  *Example:* Devplan that defines gate rules for D-loops

- **2 = executable / enforceable**  
  Output can be directly checked or executed.  
  *Example:* A CI config that fails build if tests don't exist for a given CU  
  *Example:* A constraints file that downstream tools interpret

**Guidance:** Higher `constraint_mode` → more risk if you're still at high `abstraction_jump` / `mapping_cardinality`. Often you want:
- Extraction steps at 0
- Decision/spec steps at 1
- Code/tests/config steps at 2

---

### 7. cross_domain_bridge

**Description:** How different the conceptual domains of input and output are.

**Levels:**
- **0 = same domain**  
  Product design → product design; architecture → architecture.  
  *Example:* PDD v0 → PDD summary

- **1 = adjacent domains**  
  Product behavior ↔ product architecture, architecture ↔ dev plan.  
  *Example:* PDD/Deltas → TDD changes  
  *Example:* TDD → Devplan

- **2 = orthogonal domains**  
  Product spec ↔ org process, or product spec ↔ infra operations.  
  *Example:* Using PDD/TDD to auto-design infra runbooks or HR onboarding docs

**Guidance:** 
- Delta step: domain stays "product behavior" → `cross_domain_bridge = 0`
- Gap step: behavior+architecture interplay → `cross_domain_bridge = 1`
- The further you go, the more likely you get hallucinations or irrelevant structures

---

### 8. granularity_shift

**Description:** How much the "size of the units" changes from input to output.

**Levels:**
- **0 = same granularity**  
  Sections → sections, bullets → bullets.  
  *Example:* PDD sections → PDD v1 with slightly reorganized sections

- **1 = coarse → fine (decomposition)**  
  Big blobs → smaller atomic units.  
  *Example:* PDD paragraph describing "[feature]" → many small Deltas about [sub-features]  
  *Example:* Devplan → CU list

- **2 = fine → coarse (aggregation)**  
  Many small details → higher-level summarized entities.  
  *Example:* Many Gap rows → a single "TDD must define SessionConfig struct with these fields"

**Guidance:** 
- Decomposition (1) + high `mapping_cardinality` (2) is very brain-heavy
- If a single step has:
  - `granularity_shift = 1`
  - `mapping_cardinality = 2`
  - `abstraction_jump >= 2`
  → That's a candidate to split into two steps

---

### 9. polarity_complexity

**Description:** How many "directions of change" a step must juggle: adding, removing, modifying, reconciling.

**Levels:**
- **0 = mono-polar**  
  Only add or only remove or only restate.  
  *Example:* "List all new v1 features not present in v0" (ADD only)

- **1 = bi-polar**  
  Adds and removes, but mostly independent.  
  *Example:* Deltas that handle ADD and REMOVE, but few deep modifications

- **2 = multi-polar / entangled**  
  Adds, removes, modifies, and re-wires relationships.  
  *Example:* PDD where entities are merged/split and logic moves across systems; your Delta must express that as multiple interdependent entries  
  *Example:* Gap step where you both add new TDD sections and deprecate old ones while preserving compatibility

**Guidance:** High `polarity_complexity` particularly hurts models, because they're bad at keeping "A removed, B added, C rewired" consistent across a long structured list.

---

## Feature Summary

The complete feature family:

- `abstraction_jump`
- `mapping_cardinality`
- `locality_span`
- `temporal_span`
- `representation_shift`
- `constraint_mode`
- `cross_domain_bridge`
- `granularity_shift`
- `polarity_complexity`

---

## Example Step Signatures

### Delta Step (PDD v0 → PDD v1)

**Transformation:** PDD v0 → PDD v1

**Feature signature:**
- `abstraction_jump` = 1 (behavior→behavior-meta)
- `mapping_cardinality` = 1 or 2 (can drift to N→M)
- `locality_span` = 1→2 (per section, then global coverage)
- `temporal_span` = 1 (two versions)
- `representation_shift` = 1 (text → YAML)
- `constraint_mode` = 0→1 (descriptive, slightly prescriptive)
- `cross_domain_bridge` = 0 (same domain)
- `granularity_shift` = 1 (coarse behavior → finer deltas)
- `polarity_complexity` = 1 (ADD/MODIFY/REMOVE)

**Assessment:** Medium abstraction, but high granularity + moderate mapping. Hard, but manageable with the section-wise split.

---

### Gap Step (PDD v1 + TDD v0 + Deltas → Gaps)

**Transformation:** PDD v1 + TDD v0 + Deltas → Gaps

**Feature signature:**
- `abstraction_jump` = 2 (behavior→architecture)
- `mapping_cardinality` = 2 (N PDD concepts ↔ N TDD sections → M gaps)
- `locality_span` = 3 (cross-doc: PDD + TDD + Deltas)
- `temporal_span` = 1 (or 2) (if multiple TDD versions)
- `representation_shift` = 1 (text → YAML)
- `constraint_mode` = 1 (prescriptive: "TDD must do X")
- `cross_domain_bridge` = 1 (behavior ↔ architecture)
- `granularity_shift` = 1 (big behaviors → specific missing implementation details)
- `polarity_complexity` = 2 (MISSING, OUTDATED, INCOMPLETE intertwined)

**Assessment:** That's a much nastier step by design. When you see this, it justifies:
- Adding the intermediate trace table
- Having multiple passes (extract → normalize → critique)
- Stricter schemas

---

## Usage Guidelines

### Step Design Principles

1. **Tag every step** with these feature values (manually at first)

2. **Identify problematic steps:** If a step scores "high" on too many axes → split it or add intermediate artifacts

3. **Ensure alignment:** Step outputs should align with the next step's expected:
   - Abstraction level
   - Granularity
   - Domain
   - Representation

### Goal

This vocabulary helps describe how much cognitive shape-shifting each step demands, independent of page count, and to design your pipeline as a sequence of "low-to-medium hardness" transforms instead of occasional boss fights.
