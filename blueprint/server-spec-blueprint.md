# Server Subsystem Specification Template

This document defines the normalized template for all server subsystem specifications (SPEC files). All SPEC files should follow this structure to ensure consistency and maintainability.

---

## Purpose

Subsystem specifications document:
- **What** the subsystem does (semantics, behavior)
- **Where** code lives (file locations, package boundaries)
- **How** components relate (dependencies, ownership)
- **Why** design decisions were made (invariants, constraints)

SPEC files are **not** code documentation—they explain meaning, ownership, and boundaries. Code comments explain implementation details.

---

## Standard Structure

### Header

```markdown
# [Subsystem Name] v[Version] – [Subsystem Type] Specification

[One-sentence description of what this subsystem does and what it defines.]

---
```

**Examples:**
- `# [Project Name] v1 – [Domain] Model Specification`
- `# [Project Name] v1 – [Subsystem] Subsystem Specification`
- `# [Project Name] v1 – [Orchestration] Subsystem Specification`

### Scope & Location

```markdown
## Scope & Location

**Scope**: [Brief description of what this subsystem covers]

**Code location**: `[package/path]`

**Design Goals**:
- [Goal 1: What the subsystem achieves]
- [Goal 2: Key principles or constraints]
- [Goal 3: Architectural decisions]
```

**Example:**
```markdown
## Scope & Location

**Scope**: [Subsystem description] for [Project Name] v1 ([key responsibilities]).

**Code location**: `server/internal/[subsystem]`

**Design Goals**:
- [Goal 1: What the subsystem achieves]
- [Goal 2: Key principles or constraints]
- [Goal 3: Architectural decisions]
- [Goal 4: Integration points]
```

---

## Core Section

The core section title varies by subsystem type:

| Subsystem Type | Section Title |
|----------------|---------------|
| Entities/Models | `## Core Types` |
| Physics/Calculations | `## Core Physics Operations` |
| Rules/Business Logic | `## Core Rules Operations` or `## Core Validation Operations` |
| Orchestration/Transport | `## Core Components` |
| Protocol/Messages | `## Message Schemas` |

### Component/Type/Operation Structure

Each component in the core section should follow this structure:

```markdown
### [Component Name]

**File**: `[path/to/file.go]`

**File Contract**:
- **Ownership**: [What this file owns/exports - one sentence describing the file's responsibility]
- **Exports**: [List of public types, functions, or interfaces exported by this file]
- **Related Files**: [Other files in the same package that this file interacts with]

**Concept**: [One-sentence description of what this component is]

**Key Fields**: [For structs/types]
- `fieldName Type` – Description

**Key Operations**: [For interfaces/functions]
- `OperationName(params)` – Description

**Semantics**: [How it behaves, when it's used, relationships with other components]
- Behavior description
- Usage patterns
- Relationships

**Invariants**: [Rules that must always hold]
- Invariant 1
- Invariant 2

**Ownership**: [Package-level ownership statement - if applicable]
```

**Example (Orchestration Component):**
```markdown
### [Component Name]

**File**: `server/internal/[subsystem]/[component].go`

**File Contract**:
- **Ownership**: This file defines the [Component] type and its [responsibility] methods. [Component] coordinates [dependencies] to [achieve goal].
- **Exports**: `[Component]`, `New[Component]()`, and [Component] methods (`[Method1]`, `[Method2]`, etc.)
- **Related Files**: `[file1].go` ([dependency] used by [Component]), `[file2].go` ([dependency] used by [Component])

**Concept**: [One-sentence description of what this component does].

**Key Fields**:
- `[field] [Type]` – [Description]
- `[field] [Type]` – [Description]

**Semantics**:
- [Behavior description]
- [Usage patterns]
- [Integration points]

**Invariants**:
- [Invariant 1: Rule that must always hold]
- [Invariant 2: Rule that must always hold]
```

**Example (Calculation/Operation):**
```markdown
### [Operation Name]

**File**: `server/internal/[subsystem]/[operation].go`

**File Contract**:
- **Ownership**: This file defines [operation] calculation functions. [Operation] uses [algorithm/formula description].
- **Exports**: `[OperationName]()` function and related calculation functions
- **Related Files**: `[file1].go` (uses [operation]), `[file2].go` (separate concern)

**Concept**: [One-sentence description of the operation].

**Formula/Algorithm** (if applicable):
- [Step 1]: [Description]
- [Step 2]: [Description]
- [Step 3]: [Description]

**Semantics**:
- [How the operation works]
- [When it's used]
- [What it produces]

**Invariants**:
- [Invariant 1: Rule that must always hold]
- [Invariant 2: Rule that must always hold]
```

---

## Constants

```markdown
## Constants

**[Subsystem] Constants**:
- `CONSTANT_NAME = value` – Description
- `ANOTHER_CONSTANT = value` – Description
```

**Example:**
```markdown
## Constants

**[Subsystem] Constants**:
- `CONSTANT_NAME = value` – Description
- `ANOTHER_CONSTANT = value` – Description
```

---

## Ownership & Dependencies

This section is **required** for all SPEC files.

```markdown
## Ownership & Dependencies

### [Package Name] Package Ownership

- **Only `[package/path]` may define [what this package owns]**
- [Additional ownership statements]
- [What other packages can/cannot do]

### Dependencies

- **Imports**: [List of packages this subsystem imports]
- **No dependencies on**: [List of packages this subsystem must not import]

### No Duplication Rules

- **No [thing] elsewhere**: [Rule about where code must live]
- **No parallel [types]**: [Rule about type definitions]
- [Additional rules]
```

**Example:**
```markdown
## Ownership & Dependencies

### [Package Name] Package Ownership

- **Only `[package/path]` may define [what this package owns]**
- [Additional ownership statements]
- [What other packages can/cannot do]

### Dependencies

- **Imports**:
  - `[package]` (for [purpose])
  - `[package]` (for [purpose])
- **No dependencies on**: [List of packages this subsystem must not import]

### No Duplication Rules

- **No [thing] elsewhere**: [Rule about where code must live]
- **No [types] elsewhere**: [Rule about type definitions]
- **[Package] does not implement**: [What this package does not do]
```

---

## Version Notes

This section is **required** for all SPEC files.

```markdown
## Version Notes

This spec describes v[version] [subsystem]. Key features:
- [Feature 1]
- [Feature 2]

**v[previous] Baseline**: See `SPEC.v[previous].md` for the [previous] specification.  
**Migration Guide**: See `agents/deltas/<pdd>_v[previous]_to_v[version].yaml` for detailed migration notes (delta file tracks design document changes).

Future extensions may include:
- [Future feature 1]
- [Future feature 2]
```

**Example:**
```markdown
## Version Notes

This spec describes v[version] [subsystem]. Key features:
- [Feature 1]
- [Feature 2]
- [Feature 3]

**v[previous] Baseline**: See `SPEC.v[previous].md` for the [previous] specification.  
**Migration Guide**: See `agents/deltas/<pdd>_v[previous]_to_v[version].yaml` for detailed migration notes (delta file tracks design document changes).

Future extensions may include:
- [Future feature 1]
- [Future feature 2]
```

---

## Subsystem-Specific Sections

Some subsystems may have additional sections that are specific to their domain. These should be placed after the core section and before "Constants".

### Common Additional Sections

#### Algorithms (for orchestration/physics/rules)

```markdown
## [Algorithm Name] Algorithm

**File**: `[path/to/file.go]` – `[FunctionName]`

**Algorithm**:
1. Step 1 description
2. Step 2 description
3. Step 3 description

**Semantics**:
- [How the algorithm works]
- [When it's called]
- [What it produces]

**Invariants**:
- [Rules that must hold]
```

#### Integration Patterns (for orchestration/transport)

```markdown
## Integration with [Other Layer]

**[Pattern Name]**:
- [How this subsystem integrates with another]
- [Responsibilities of each layer]
- [Data flow]

**Semantics**:
- [Integration behavior]
```

#### Validation (for protocol)

```markdown
## Validation

### Validation Principles

- [Principle 1]
- [Principle 2]

### Validation Functions

- `Validate[Type](msg *[Type]) error`
- [Additional functions]

### Common Validation Rules

- [Rule 1]
- [Rule 2]
```

#### Versioning Policy (for protocol)

```markdown
## Versioning Policy

### Version Format

[Description of version format]

### Compatibility Rules

- [Rule 1]
- [Rule 2]

### Breaking vs Non-Breaking Changes

#### Breaking Changes (require major version increment)

- [Type of change 1]
- [Type of change 2]

#### Non-Breaking Changes (same major version)

- [Type of change 1]
- [Type of change 2]
```

---

## File Contract Guidelines

The **File Contract** section is a new addition to normalize file boundaries. It should:

1. **Be concise**: One sentence for ownership, bullet list for exports
2. **Be explicit**: Clearly state what belongs in the file
3. **Reference related files**: Help developers understand relationships
4. **Focus on boundaries**: What's in vs. out, not implementation details

### When to Include File Contracts

- **Always include** for files that define types/structs
- **Always include** for files that export public APIs
- **Optional** for simple utility files with single functions
- **Optional** for test files (they're self-explanatory)

### File Contract Format

```markdown
**File Contract**:
- **Ownership**: [One sentence describing the file's responsibility and what it owns]
- **Exports**: [Comma-separated list of public types, functions, interfaces]
- **Related Files**: [List of related files in the same package, with brief relationship description]
```

**Minimal format** (for simple files):
```markdown
**File Contract**:
- **Ownership**: [One sentence]
- **Exports**: [List]
```

---

## Checklist for New SPEC Files

When creating a new SPEC file, ensure:

- [ ] Header follows template format
- [ ] Scope & Location section is complete
- [ ] Design Goals are clear and specific
- [ ] Core section uses appropriate title for subsystem type
- [ ] All components include File Contract section
- [ ] Component structure follows template (File, File Contract, Concept, Key Fields/Operations, Semantics, Invariants)
- [ ] Constants section is present (even if empty, note "No constants")
- [ ] Ownership & Dependencies section is complete
- [ ] Version Notes section is present
- [ ] All file paths are correct and relative to repo root
- [ ] All cross-references to other SPEC files use correct paths

---

## Examples by Subsystem Type

### Entities/Models SPEC

- Focus: Data structures, type definitions
- Core section: "Core Types"
- Emphasize: Ownership, invariants, relationships between types
- Example: `server/internal/[domain]/entities/SPEC.v[version].md`

### Calculation/Logic SPEC

- Focus: Calculations, formulas, algorithms, business logic
- Core section: "Core [Domain] Operations" or "Core Logic Operations"
- Emphasize: Formulas, parameters, determinism (if applicable)
- Example: `server/internal/[domain]/[logic]/SPEC.v[version].md`

### Rules/Validation SPEC

- Focus: Business rules, validation logic, state transitions
- Core section: "Core Rules Operations" or "Core Validation Operations"
- Emphasize: State machine, validation rules, business constraints
- Example: `server/internal/[domain]/rules/SPEC.v[version].md` or `server/internal/[domain]/validation/SPEC.v[version].md`

### Orchestration SPEC

- Focus: Coordination, lifecycle, algorithms
- Core section: "Core Components"
- Emphasize: Lifecycle, integration patterns, algorithms
- Example: `server/internal/[orchestration]/SPEC.v[version].md`

### Protocol SPEC

- Focus: Message schemas, validation, versioning
- Core section: "Message Schemas"
- Emphasize: JSON schemas, validation rules, versioning policy
- Example: `server/internal/proto/SPEC.v[version].md`

### Transport SPEC

- Focus: Network IO, connection management
- Core section: "Core Components"
- Emphasize: Connection lifecycle, error handling, conversion
- Example: `server/internal/transport/SPEC.v[version].md`

---

## Maintenance Guidelines

1. **Keep SPEC files in sync with code**: When code changes, update the SPEC
2. **File contracts are living documents**: Update them when file boundaries change
3. **Version notes track evolution**: Always document changes between versions
4. **Cross-reference related SPECs**: Link to related subsystems
5. **Review during PRs**: Ensure SPEC updates accompany code changes

---

## Questions?

If you're unsure about:
- **What to include**: Ask "Would a new team member understand this subsystem from this SPEC?"
- **How to structure**: Follow this template, but adapt for subsystem-specific needs
- **File contracts**: Include them for any file that defines types or exports public APIs
- **Versioning**: Always include version notes, even for v0 (reference future versions)


