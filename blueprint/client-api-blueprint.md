# [Project Name] v[Version] – [Package Name] Package API

This document provides detailed API reference for the [package name] package.

---

## Scope & Location

**Scope**: [Brief description of what this package covers]

**Code location**: `[path/to/package]`

**Design Goals**:
- [Goal 1: What the package achieves]
- [Goal 2: Key principles or constraints]
- [Goal 3: Architectural decisions]

---

## [Component Name]

**File**: `[filename].ts` (or `.js`, `.tsx`, etc.)

**Concept**: [One-sentence description of what this component is]

### Interface

```typescript
export interface [InterfaceName] {
  // Interface definition
}

export class [ClassName] {
  constructor([params])
  [method1](): [return type]
  [method2]([params]): [return type]
}
```

### Methods

#### `constructor([params])`

[Description of what the constructor does]

**Parameters:**
- `param: Type` - [Description]

**Returns:** [ClassName] instance

---

#### `[methodName]([params]): [return type]`

[Description of what the method does]

**Parameters:**
- `param: Type` - [Description]

**Returns:** [Return type description]

**Throws:** [Error conditions if any]

**Example:**
```typescript
const instance = new [ClassName]()
instance.[methodName]([example params])
```

---

### Semantics

- [Behavior description]
- [Usage patterns]
- [Relationships with other components]

### Lifecycle

1. **Creation**: `new [ClassName]([params])` – [what happens]
2. **Usage**: `[method]()` – [what happens]
3. **Cleanup**: `[cleanup method]()` – [what happens]

### Invariants

- [Invariant 1: Rule that must always hold]
- [Invariant 2: Rule that must always hold]

---

## [Component Name 2]

**File**: `[filename].ts`

**Concept**: [One-sentence description]

### Interface

```typescript
export class [ClassName] {
  // Interface definition
}
```

### Methods

[Follow same structure as above]

### Semantics

[Behavior description]

### Lifecycle

[Lifecycle steps]

### Invariants

[Invariants]

---

## Types

### [Type Name]

[Description of the type]

```typescript
interface [TypeName] {
  field: Type  // Description
  optionalField?: Type  // Description
}
```

---

## Constants

### [Package Name] Constants

- `CONSTANT_NAME = value` – Description
- `ANOTHER_CONSTANT = value` – Description

---

## Ownership & Dependencies

### [Package Name] Package Ownership

- **Only `[package/path]` may define [what this package owns]**
- [Additional ownership statements]
- [What other packages can/cannot do]

### Dependencies

- **Imports**:
  - `[external-library]` – [Purpose]
  - `@[internal-package]` – [Purpose]
- **No dependencies on**: [List of packages this package must not import]

### No Duplication Rules

- **No [thing] elsewhere**: [Rule about where code must live]
- **No [types] elsewhere**: [Rule about type definitions]
- **[Package] does not implement**: [What this package does not do]

---

## Error Handling

### [Component Name] Errors

- **[Error type]**: [When it's thrown and how to handle it]
- **[Error type]**: [When it's thrown and how to handle it]

---

## Dependencies

- `[external-library]` - [Purpose]
- `@[internal-package]` - [Purpose]

---

## Version Notes

This API describes v[version] [package name]. Key features:
- [Feature 1]
- [Feature 2]
- [Feature 3]

**Changes from v[previous]**:
- [Change 1]
- [Change 2]

Future extensions may include:
- [Future feature 1]
- [Future feature 2]

---

