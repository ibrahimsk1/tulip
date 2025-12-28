# 🌷 Tulip

**An automated development workflow that guides you from idea to working software, step by step.**

Tulip is a systematic approach to building software that uses AI agents to automate workflow steps, ensuring quality through testing and clear documentation. Instead of wondering "what do I build next?", Tulip provides a clear, repeatable process that takes your project from concept to completion.

---

## How Tulip Works: The Complete Flow

Here's the step-by-step flow that takes your project from idea to working software:

```
┌─────────────────────────────────────────────────────────┐
│ STEP 1: Design Phase (You Do This)                      │
│                                                          │
│ • Write Product Design Document (GDD/PRD)               │
│   → Describes what you're building                      │
└─────────────────────────────────────────────────────────┘
                        ↓
┌─────────────────────────────────────────────────────────┐
│ STEP 2: Change Analysis (Automated)                     │
│                                                          │
│ • Delta Agent: Finds what changed in your design       │
│ • Gap Agent: Finds what's missing in your technical plan│
└─────────────────────────────────────────────────────────┘
                        ↓
┌─────────────────────────────────────────────────────────┐
│ STEP 3: Architecture Update (Automated)                 │
│                                                          │
│ • Architect Agent: Updates technical docs based on      │
│   changes and gaps                                      │
│ • Ensures TDD, specs, and APIs are up to date           │
└─────────────────────────────────────────────────────────┘
                        ↓
┌─────────────────────────────────────────────────────────┐
│ STEP 4: Master Plan (Automated)                         │
│                                                          │
│ • Creates development plan with all loops and gates     │
│ • Defines progression through D-loops                  │
└─────────────────────────────────────────────────────────┘
                        ↓
┌─────────────────────────────────────────────────────────┐
│ STEP 5: Loop Planning (Automated)                       │
│                                                          │
│ • Planning Agent: Chooses next loop to work on          │
│ • Breaks loop into Change Units (small tasks)           │
│ • Each CU has goals, file allowlists, test requirements │
└─────────────────────────────────────────────────────────┘
                        ↓
┌─────────────────────────────────────────────────────────┐
│ STEP 6: Implementation (Automated)                       │
│                                                          │
│ • Implementation Agent: Picks next Change Unit          │
│ • Creates implementation plan                            │
│ • Writes tests FIRST (test-first development)           │
│ • Implements minimal code to pass tests                 │
│ • Verifies all tests pass                               │
│ • Marks CU complete, selects next one                    │
└─────────────────────────────────────────────────────────┘
                        ↓
┌─────────────────────────────────────────────────────────┐
│ STEP 7: Loop Completion (Automated)                      │
│                                                          │
│ • When all CUs in loop are done and tests pass          │
│ • Workflow advances to next development loop            │
│ • Process repeats until project is complete              │
└─────────────────────────────────────────────────────────┘
```

---

## Step-by-Step Flow Explanation

### Step 1: Design Phase (You Do This)

**What happens:**
- You write the **Product Design Document** (PDD) that defines your project:
  - For games: Called Game Design Document (GDD) - describes gameplay, mechanics, features
  - For apps: Called Product Requirements Document (PRD) - describes features, user flows, requirements
  - This document describes **what** you're building

**Why it matters:**
- This document serves as the source of truth for what your product should do
- It uses templates (blueprints) to ensure consistency
- The Architect Agent (in Step 3) will create/update the Technical Design Document (TDD) based on this

**Templates available:**
- `blueprint/gdd-blueprint.md` for games
- `blueprint/prd-blueprint.md` for applications

---

### Step 2: Change Analysis (Automated via AI Agents)

**What happens:**
When you create a new version of your design (e.g., v0 → v1), two AI agents analyze the changes:

1. **Delta Agent** (`agents/deltas/`)
   - Compares your old and new design documents
   - Extracts what changed: new features, modified features, removed features
   - Creates a structured list (YAML file) tracking all changes

2. **Gap Agent** (`agents/gaps/`)
   - Compares your design document with your technical plan
   - Identifies missing implementation details in your TDD
   - Finds gaps like: missing algorithms, undefined constants, unclear patterns
   - Creates a structured list (YAML file) of what needs to be added

**Why it matters:**
- Ensures nothing is missed when upgrading versions
- Provides traceability: you know exactly what changed and why
- Creates structured data that other agents can use

**Output:**
- `deltas/<pdd>_v*_to_v*.yaml` - List of design changes
- `gaps/tdd_v*_to_<pdd>_v*.yaml` - List of missing implementation details

---

### Step 3: Architecture Update (Automated via Architect Agent)

**What happens:**
The **Architect Agent** (`agents/architect/`) creates or updates your technical documentation:

1. **Creates/Updates Technical Design Document (TDD)**
   - For initial version: Creates TDD based on your Product Design Document
   - For version upgrades: Updates TDD based on deltas and gaps
   - Adds new packages, modules, or systems needed
   - Updates architecture diagrams and descriptions
   - Works top-down: system overview → services → modules → interfaces

2. **Updates Package Specifications (SPEC.md files)**
   - Documents what each package does
   - Defines invariants and constraints
   - Explains relationships between components

3. **Updates API Documentation (API.md files)**
   - Documents interfaces and contracts
   - Defines method signatures and behaviors
   - Specifies protocols for communication

**Why it matters:**
- Creates the technical foundation (TDD) from your product design
- Keeps technical documentation in sync with design changes
- Ensures all agents work from the same up-to-date information
- The TDD becomes the source of truth for planning and implementation

**Output:**
- Created/Updated `tdd_<project>_v*.md`
- Created/Updated `server/**/SPEC.md` files
- Created/Updated `client/**/API.md` files

---

### Step 4: Master Plan Creation (Automated)

**What happens:**
A master development plan is created that defines:

1. **Development Loops (D-loops)**: The layers you'll build in order
   - **Server loops**: D0 (workspace) → D1 (entities) → D2 (domain logic) → D3 (validation) → D4 (contracts) → D5 (session) → D6 (context) → D7 (transport) → D8 (observability) → D9 (end-to-end)
   - **Client loops**: D0 (workspace) → D1 (state) → D2 (orchestrator) → D4 (contracts) → D5 (network) → D6 (rendering) → D7 (UI) → D8 (input) → D9 (observability) → D10 (end-to-end)

2. **Quality Gates**: Proof requirements for each loop
   - What tests must pass before advancing
   - What evidence must be recorded

3. **Sequence**: The order in which loops will be completed

**Why it matters:**
- Provides the roadmap for the entire project
- Ensures you build from inside-out (core logic first, then user-facing features)
- Sets clear quality standards at each stage

**Key principle:** You can't advance to loop D(n+1) until all tests pass for loop Dn. This ensures each layer is solid before building on top.

**Output:**
- `devplan_<project>_v*.md` - Master development plan

---

### Step 5: Loop Planning (Automated via Planning Agent)

**What happens:**
The **Planning Agent** (`agents/planning/`) analyzes the next development loop to work on:

1. **Chooses the next loop** based on the master plan and current progress
2. **Breaks the loop into Change Units (CUs)** - small, testable tasks
   - Each CU has a clear, single goal
   - Each CU knows which files it can modify (allowlist)
   - Each CU has specific test requirements (proof labels)
   - Each CU has a status: `BACKLOG` → `NEXT` → `IN_PROGRESS` → `DONE`

3. **Creates a detailed plan** with:
   - Loop goals and prerequisites
   - Step-by-step outcomes
   - Complete CU list with all requirements
   - Acceptance criteria

**Example:** Instead of "build user authentication," you might have:
- CU1: Create User entity (D1 loop)
- CU2: Add password validation (D3 loop)
- CU3: Create login endpoint (D5 loop)
- CU4: Add session management (D5 loop)

**Why it matters:**
- Breaks large work into manageable pieces
- Ensures each piece is testable and has clear boundaries
- Provides clear status tracking

**Output:**
- `development_v*/<loop>/<date>_<slug>/plan.md` - Detailed plan for the loop

---

### Step 6: Implementation (Automated via Implementation Agent)

**What happens:**
The **Implementation Agent** (`agents/implementation/`) executes each Change Unit:

1. **Finds the next CU** marked `NEXT` in the plan
2. **Creates an implementation plan** with:
   - Task breakdown
   - Files that can be modified (allowlist)
   - Tests that must pass
   - Evidence requirements

3. **Waits for approval** (manual gate)

4. **Writes tests FIRST** (test-first development)
   - Tests define the expected behavior
   - Tests are labeled with proof requirements (e.g., `scope:unit loop:d1-domain`)

5. **Implements minimal code** to make tests pass
   - Only modifies files in the allowlist
   - Writes the simplest code that satisfies the tests

6. **Verifies all tests pass** and records evidence
   - Runs the test suite
   - Documents results

7. **Marks CU as `DONE`** and selects the next `NEXT` CU
   - Only one CU marked `NEXT` at a time
   - Ensures focus on one task

8. **Commits and merges** with evidence

**Why it matters:**
- Test-first ensures you know when you're done
- Small, focused changes reduce risk
- Evidence provides proof that everything works

**Output:**
- `development_v*/<loop>/<slug>/cu_<cu-slug>/<cu-slug>-implementation_plan.md`
- Code changes in allowed files
- Updated plan.md with CU status changes

---

### Step 7: Loop Completion & Progression (Automated)

**What happens:**
When all Change Units in a loop are complete and all tests pass:

1. **Loop is marked complete**
2. **Workflow automatically advances** to the next development loop
3. **Process repeats** from Step 5 (Loop Planning) for the new loop
4. **Continues** until the project is complete

**Why it matters:**
- Ensures systematic progression through layers
- Quality gates prevent advancing with broken code
- Clear progression from core to surface

**The flow repeats:**
- Steps 5-7 repeat for each development loop
- Each loop builds on the previous one
- Eventually, all loops are complete and the project is done

---

## Understanding the Building Blocks

As you follow the flow, here are the key concepts you'll encounter:


### Change Units (CUs)

Work is broken into small, testable pieces called Change Units. Each CU:
- Has a clear, single goal
- Knows which files it can modify (allowlist)
- Has tests that prove it works
- Moves through stages: `BACKLOG` → `NEXT` → `IN_PROGRESS` → `DONE`

### AI Agents

Specialized AI agents execute specific workflow steps:

| Agent | What It Does | When It Runs |
|-------|-------------|--------------|
| **Delta Agent** | Analyzes what changed between design document versions | After creating a new version of your design document |
| **Gap Agent** | Identifies missing implementation details in your technical plan | After delta analysis, before architecture updates |
| **Architect Agent** | Updates technical documentation (TDD, specs, APIs) based on changes | After gap analysis, before planning |
| **Planning Agent** | Creates detailed implementation plans for each development loop | When ready to start a new development loop |
| **Implementation Agent** | Implements code following test-first principles | When a Change Unit is ready to be built |

---

## Repository Structure

```
vortex/
├── workflow/                    # 📖 Main guides - start here!
│   ├── workflow.md              # Complete workflow documentation
│   ├── workflow-game.md         # Game-specific guidance
│   └── workflow-application.md  # Application-specific guidance
│
├── agents/                      # 🤖 AI agent configurations
│   ├── architect/               # Updates technical documentation
│   ├── deltas/                  # Analyzes design document changes
│   ├── gaps/                    # Finds missing implementation details
│   ├── planning/                # Creates implementation plans
│   └── implementation/          # Implements code (test-first)
│
├── blueprint/                   # 📋 Document templates
│   ├── gdd-blueprint.md         # Game Design Document template
│   ├── tdd-blueprint.md         # Technical Design Document template
│   └── ...
│
├── utils/                       # 🛠 Utility files and helpers
│   ├── features.md              # Feature definitions and conventions
│   ├── objectives.md            # Project objectives and goals
│   └── test_lables.md          # Test labeling conventions
│
├── v0/                          # 📦 Version 0 designs
│   └── (your initial design documents)
│
└── v1/                          # 📦 Version 1 designs
    └── (your next version's designs)
```

---

## Getting Started

### For Developers

1. **Read the Main Guide**: Start with [`workflow/workflow.md`](workflow/workflow.md) - it explains everything in detail
2. **Choose Your Project Type**:
   - Building a game? See [`workflow/workflow-game.md`](workflow/workflow-game.md)
   - Building an app? See [`workflow/workflow-application.md`](workflow/workflow-application.md)
3. **Use the Templates**: The blueprint files in `blueprint/` help you structure your design documents
4. **Configure Agents**: The agent files in `agents/` are configurations for AI agents that execute workflow steps

### For Non-Technical Stakeholders

Tulip ensures that:
- **Everything is documented**: You can see what's being built and how
- **Changes are tracked**: You know what changed between versions and why
- **Quality is guaranteed**: Tests prove everything works before moving forward
- **Progress is visible**: Clear status tracking shows where things are

The workflow documents in `workflow/` explain the process in detail, and the design documents (GDD/PRD and TDD) serve as the single source of truth for what's being built.