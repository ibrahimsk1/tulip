# [Project Name] – Product Design Document (v[Version])

> **Note**: For games, this is called a Game Design Document (GDD). For applications, this is called a Product Requirements Document (PRD). The structure is the same, but terminology may vary.

**Version**: [X.Y]  
**Date**: [YYYY-MM-DD]  
**Status**: [Draft/Active/Deprecated]

---

## 1. Goal of v[Version]

[One paragraph describing what this version adds or achieves. For v0, this is the minimal playable slice. For later versions, describe the key additions or changes.]

---

## 2. Core Loop (v[Version])

The core loop is the primary cycle players engage with. Number each step clearly.

1. [Step 1: What player does first]
2. [Step 2: What happens next]
3. [Step 3: Outcome or progression]
4. [Step 4: How it loops back or transitions]

---

## 3. Scope Included

List all features, systems, and capabilities included in this version. Use bullet points for clarity.

- **[Feature Category]**: [Specific features]
- **[Feature Category]**: [Specific features]

**Common categories** (adapt based on project type):
- **Games**: Player count and matchmaking, world/environment features, gameplay mechanics, UI/visual features, multiplayer features, technical features
- **Applications**: User management, feature sets, workflows, integrations, UI/UX features, technical features

---

## 4. Scope Excluded

List features explicitly excluded from this version. This helps prevent scope creep and sets clear boundaries.

- [Feature that was considered but deferred]
- [Feature that will come in later versions]
- [Known limitations]

---

## 5. Controls

**In Menu/Lobby**: [Input methods for menu navigation]

**In Game**:
- **[Action]**: [Input method/key]
- **[Action]**: [Input method/key]

**Special Actions**:
- **[Action]**: [When/where it's available]

---

## 6. Simulation Model

### Physics & World

**Server Tick Rate**: [Hz] fixed-step simulation

**World Properties**:
- **Size**: [Dimensions, units]
- **Bounds**: [How boundaries work - wraparound, clamp, death, etc.]
- **Gravity**: [Gravity system description]
- **Drag**: [Drag/friction system]

**Collisions**:
- **[Entity A] vs [Entity B]**: [What happens]
- **[Entity A] vs [Entity B]**: [What happens]

### Standardized Game Metrics

Define the units used throughout the game for consistency:
- **Distance**: [meters/units]
- **Angle**: [radians/degrees]
- **Time**: [seconds]
- **Mass**: [units]

### Target Constants

List key simulation constants with units:
- `dt = [value] [unit]` ([description])
- `G = [value]` ([description])
- `[constant] = [value] [unit]` ([description])

### Entity Specifications

**Entity Name: [Name]**
- **Radius/Size**: [value] [unit]
- **Mass**: [value] [unit] (if applicable)
- **Other Properties**: [key properties]

---

## 7. Entities

List all game entities with their key properties:

- **[Entity Name]**: [key properties, e.g., id, pos, vel, rot, energy, etc.]
- **[Entity Name]**: [key properties]
- **[Entity Name]**: [key properties]

**Room/State Entities** (if applicable):
- **[Entity Name]**: [properties for room management, match state, etc.]

---

## 8. Networking

### Transport: [WebSocket/HTTP/etc.] [JSON/Protobuf/etc.]

### Client → Server

**Menu/Pre-Game**:
- `{"t":"[messageType]","[field]":[value]}` → [what happens]

**In-Game**:
- `{"t":"[messageType]","[field]":[value]}` → [what happens, cadence if applicable]

### Server → Client

**Room Updates** (if applicable):
- `{"t":"[messageType]","[field]":[value]}` → [what it means]

**Game Snapshot**:
```json
{
  "t": "[messageType]",
  "[field]": [type],
  "[entities]": [
    {
      "[property]": [type]
    }
  ]
}
```

**Event Messages**:
- `{"t":"[messageType]","[field]":[value]}` → [when it's sent]

---

## 9. UI & Visuals

### Main Menu
- [Layout description]
- [Key UI elements]
- [Navigation flow]

### Room Lobby (if applicable)
- [Layout description]
- [Player list format]
- [Action buttons]
- [Room code display]

### In-Game
- **Main View**: [Camera behavior, world rendering]
- **HUD**: [Heads-up display elements]
  - [Element 1]: [What it shows]
  - [Element 2]: [What it shows]
- **Radar/Mini-map** (if applicable): [Position, size, what it shows]
- **Banners/Notifications**: [Win/lose messages, alerts, etc.]

---

## 10. Camera System (if applicable)

- **Follow Mode**: [How camera follows player]
- **Smoothing**: [Smoothing factor/behavior]
- **Bounds**: [Camera boundary behavior]
- **Viewport**: [Viewport size, zoom, scaling]

---

## 11. Radar System (if applicable)

- **Position**: [Where it appears on screen]
- **Size**: [Dimensions]
- **Coordinate Mapping**: [How world coordinates map to radar]
- **Elements**: [What entities are shown and how]
- **Range**: [What area of the world is visible]
- **Update Rate**: [How often it updates]

---

## 12. Tech Stack

- **Client**: [Language, framework, rendering library, build tool]
- **Server**: [Language, version, libraries]
- **Networking**: [Protocol, libraries]
- **Deployment**: [Containerization, orchestration, hosting]

---

## 13. Acceptance

Acceptance criteria are testable conditions that must be met for this version to be considered complete. Each should be specific and verifiable.

- [Specific, testable requirement]
- [Specific, testable requirement]
- [Specific, testable requirement]

**Common acceptance criteria** (adapt based on project type):
- **Games**: Room/match creation and joining, multiplayer synchronization, physics behavior, win/lose conditions, UI functionality, deployment/running
- **Applications**: Feature functionality, user workflows, data persistence, API endpoints, UI functionality, deployment/running

---

## 14. Increment Path

List planned incremental improvements for future versions:

1. **v[X.Y]**: [Next small increment]
2. **v[X.Y]**: [Following increment]
3. **v[X+1] step**: [Major version upgrade]

---

## Notes

- Keep this document focused on **what** the product is, not **how** it's built (that's the TDD)
- Update scope sections as features are added or removed
- Use clear, specific language - avoid vague descriptions
- Constants should include units for clarity
- Acceptance criteria should be testable and specific
- For games: Focus on gameplay mechanics, simulation, and player experience
- For applications: Focus on user workflows, business logic, and feature requirements
