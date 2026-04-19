---
name: nafv4-coordinator
description: Coordinate NAF v4 / ADMBw modeling across all viewpoint categories. Routes requests to specialized skills (Requirements, Architecture Metadata, Concepts, Logical Specification, Physical Resources, Service Specification) and handles cross-viewpoint relationships. Use when the user wants to work with multiple viewpoint categories, create relationships between different viewpoint types (e.g., Requirement → Capability, Capability → Service), or needs guidance on which viewpoint to use. Use when this capability is needed.
metadata:
  author: carstenlucke
---

# NAF v4 Coordinator for Sparx Enterprise Architect

## Overview

This coordinator skill manages NAF v4 / ADMBw architecture modeling across all viewpoint categories. It dispatches requests to specialized skills and handles cross-viewpoint relationships.

## Core Responsibilities

1. **Viewpoint Identification** - Recognize which NAF viewpoint category the user needs
2. **Skill Routing** - Activate the appropriate specialized skill
3. **Cross-Viewpoint Relationships** - Handle relationships spanning multiple viewpoint categories
4. **Guidance** - Help users understand and choose viewpoints

## General Modeling Rules

These rules apply to all NAF v4 modeling tasks:

### 1. Modeling Target Clarification

When the modeling target is unclear, always ask the user where to model:
- **Currently open diagram** - Add elements to the active diagram
- **Specific (non-displayed) diagram** - Add to a named diagram that may not be open
- **Package in workspace** - Create elements in a specific package location
- **New diagram** - Create a new diagram first

**Default behavior:** If not explicitly specified, use the currently open diagram as the modeling target.

### 2. Element Existence Verification

Before using element names in any operation, especially when creating connections:
- **Always check first** if the element already exists in the diagram or workspace
- Use MCP `find_elements_by_name` to search for elements
- If multiple elements with the same name exist, ask the user to clarify which one (by package path or GUID)
- If the element doesn't exist, offer to create it or ask for clarification

**This prevents:**
- Creating duplicate elements
- Invalid connections to non-existent elements
- Confusion about which element is being referenced

### 3. Automatic Diagram Layout

Never apply automatic diagram layout operations:
- **Do not use** `layout_diagram` or similar automatic layout functions
- **Manual layout only** - Let the user arrange elements manually in Sparx EA
- Elements should be placed on diagrams using `place_element_on_diagram`, but their visual arrangement is left to the user
- The user controls the visual organization of their diagrams

## Available Specialized Skills

| Skill | Viewpoints | Description |
|-------|-----------|-------------|
| **nafv4-requirements** | R2-R6 | Requirements Catalog, Dependencies, Conformance, Derivation, Realization |
| **nafv4-architecture-metadata** | A1-A8, Ar | Meta-Data, Products, Correspondence, Methodology, Status, Version, Compliance, Standards, Roadmap |
| **nafv4-concepts** | C1-C8, Cr | Capability Taxonomy, Enterprise Vision, Dependencies, Processes, Effects, Parameters, Assumptions, Roadmap |
| **nafv4-logical-specification** | L1-L8, Lr | Node Types, Scenario, Interactions, Activities, States, Sequence, Information Model, Constraints, Lines of Development |
| **nafv4-physical-resources** | P1-P8, Pr | Resource Types, Structure, Connectivity, Functions, States, Sequence, Data Model, Constraints, Configuration Management |
| **nafv4-service-specification** | S1-S8, Sr | Service Taxonomy, Structure, Interfaces, Functions, States, Interactions, Parameters, Policy, Roadmap |

## Workflow

### Step 1: Identify User Intent

Analyze the user's request to determine:
- Which viewpoint category is needed?
- Is this a single-viewpoint or cross-viewpoint task?
- Does the user know which viewpoint they need?

### Step 2: Route to Specialized Skill

**For single-viewpoint tasks:**
- Inform user which skill will be activated
- The specialized skill handles the entire interaction
- No further coordinator involvement needed

**For cross-viewpoint tasks:**
- Continue with coordinator to manage the interaction
- Call multiple specialized skills as needed
- Coordinate the cross-viewpoint relationships

### Step 3: Handle Cross-Viewpoint Relationships

Use MCP tools to create connectors between elements from different viewpoint categories.

**Common cross-viewpoint relationships:**
- **Requirement → Capability** (R2 to C1): DerivedFrom, MapsToCapability
- **Capability → Service** (C1 to S1): Consumes, Provides
- **Capability → Resource** (C1 to P1): Exhibits, RequiresResource
- **Service → Logical** (S2 to L2): Implements, ActivitySupportsService
- **Logical → Physical** (L2 to P2): Implements
- **Requirement → Architecture** (R2 to A2): TracedTo, RealizedBy

## Viewpoint Selection Guidance

### By User Need

**"I need to..."**

| User Need | Recommended Viewpoint | Skill |
|-----------|----------------------|-------|
| "Document requirements" | R2 - Requirement Catalogue | nafv4-requirements |
| "Define capabilities" | C1 - Capability Taxonomy | nafv4-concepts |
| "Show enterprise vision and goals" | C2 - Enterprise Vision | nafv4-concepts |
| "Model logical architecture" | L1-L2 - Node Types, Scenario | nafv4-logical-specification |
| "Define operational activities" | L4 - Logical Activities | nafv4-logical-specification |
| "Catalog physical resources" | P1 - Resource Types | nafv4-physical-resources |
| "Show system structure" | P2 - Resource Structure | nafv4-physical-resources |
| "Define services" | S1-S2 - Service Taxonomy, Structure | nafv4-service-specification |
| "Document architecture metadata" | A1-A2 - Meta-Data, Products | nafv4-architecture-metadata |

### By Abstraction Level

**Strategic/Conceptual Layer:**
- **Concepts (C1-C8)** - Capabilities, vision, goals, effects
- **Requirements (R2-R6)** - What the system must do
- Coordinator → `nafv4-concepts` or `nafv4-requirements`

**Logical Layer:**
- **Logical Specification (L1-L8)** - Solution-neutral design, nodes, activities
- **Service Specification (S1-S8)** - Service-oriented architecture
- Coordinator → `nafv4-logical-specification` or `nafv4-service-specification`

**Physical/Implementation Layer:**
- **Physical Resources (P1-P8)** - Actual systems, software, hardware
- Coordinator → `nafv4-physical-resources`

**Meta/Governance Layer:**
- **Architecture Metadata (A1-A8)** - Architecture documentation, standards
- Coordinator → `nafv4-architecture-metadata`

## Cross-Viewpoint Patterns

### Pattern: Requirement to Capability Traceability

```
User: "Link requirement 'FR-001' to capability 'Surveillance'"

Coordinator Actions:
1. Verify FR-001 exists (using nafv4-requirements skill knowledge)
2. Verify Surveillance capability exists (using nafv4-concepts skill knowledge)
3. Determine appropriate relationship stereotype (likely DerivedFrom or MapsToCapability)
4. Create connector using MCP create_or_update_connector:
   {
     "sourceElementName": "FR-001",
     "targetElementName": "Surveillance",
     "connectorType": "Dependency",
     "stereotype": "MapsToCapability",
     "profile": "NAFv4-ADMBw"
   }
5. Confirm to user
```

### Pattern: Capability to Service Mapping

```
User: "Show that 'C2 Capability' is provided by 'Command Service'"

Coordinator Actions:
1. Verify capability exists (C1 viewpoint)
2. Verify service exists (S1/S2 viewpoint)
3. Create relationship:
   - If service provides capability: Use Provides or ServiceProvision
   - If capability needs service: Use Consumes
4. Execute MCP call with correct stereotype
```

### Pattern: Logical to Physical Implementation

```
User: "OperationalPerformer 'ISR Node' is implemented by System 'RadarSystem'"

Coordinator Actions:
1. Verify OperationalPerformer exists (L1/L2)
2. Verify System exists (P1/P2)
3. Create Implements relationship (type: Abstraction)
4. Execute MCP call
```

### Pattern: Service to Activity Support

```
User: "Service 'AuthService' supports activity 'AuthenticateUser'"

Coordinator Actions:
1. Verify Service exists (S2/S4)
2. Verify OperationalActivity exists (L4)
3. Create ActivitySupportsService relationship (type: Realization)
4. Execute MCP call
```

## Ambiguity Resolution

### When User Request is Unclear

**Example:**
```
User: "Create a capability diagram"

Coordinator: "I can help you create capability-related diagrams. Which would you like?

1. **C1 - Capability Taxonomy** - Organize capabilities in hierarchies with MoEs
2. **C2 - Enterprise Vision** - Show capabilities in enterprise context with goals
3. **C3 - Capability Dependencies** - Analyze dependencies between capabilities
4. **Cr - Capability Roadmap** - Track capability delivery over time

Which best fits your needs?"
```

### When Multiple Skills Could Apply

**Example:**
```
User: "Add a constraint"

Coordinator: "I can create several types of constraints. What level?

1. **StrategicConstraint** (Concepts C1) - Capability-level rules
2. **OperationalConstraint** (Logical L8) - Logical architecture rules
3. **ResourceConstraint** (Physical P8) - Implementation rules
4. **ServicePolicy** (Services S8) - Service-level policies
5. **Requirements** (R2-R6) - Requirement-level constraints

Which type do you need?"
```

## Error Prevention

### Validate Cross-Viewpoint Compatibility

Before creating cross-viewpoint relationships:

1. **Load JSON metadata** for both viewpoints if needed
2. **Check stereotypes** of both source and target elements
3. **Verify compatibility** - Can these element types be connected?
4. **Suggest alternatives** if incompatible

**Example:**
```
User: "Link FunctionalRequirement to System"

Coordinator checks:
- FunctionalRequirement (R2, type: Requirement)
- System (P1, type: Class)
- Valid relationships between these types

If valid: Create connector
If invalid: "FunctionalRequirement and System cannot be directly linked.
             Consider:
             - Link Requirement to Capability (MapsToCapability)
             - Then Capability to System (Exhibits)"
```

## Delegation Strategy

### When to Stay Active (Coordinator)

- User asks about multiple viewpoint categories
- User needs cross-viewpoint relationships
- User is unsure which viewpoint to use
- User asks for overall NAF v4 guidance

### When to Delegate (Specialized Skills)

- User clearly indicates a specific viewpoint (e.g., "Create R2 diagram")
- User uses terminology specific to one category (e.g., "operational performer" → Logical)
- Task is entirely within one viewpoint category

**Delegation pattern:**
```
Coordinator: "I'm activating the nafv4-logical-specification skill to handle
             your request for operational activities..."

[Hand off to specialized skill - no further coordinator involvement unless
cross-viewpoint work is needed]
```

## Tips for Users

### Choosing the Right Viewpoint

1. **Start with WHY** - Requirements (R) and Capabilities (C)
2. **Move to HOW (logical)** - Logical Specification (L) and Services (S)
3. **Then HOW (physical)** - Physical Resources (P)
4. **Document throughout** - Architecture Metadata (A)

### Common Workflows

**Requirements to Implementation:**
1. R2 - Define requirements
2. C1 - Map to capabilities
3. L2/L4 - Design logical solution
4. P1/P2 - Specify physical resources
5. R6 - Trace realization

**Service-Oriented Development:**
1. C1 - Define capabilities
2. S1/S2 - Design services
3. L4 - Model service functions
4. P2 - Implement with resources
5. Sr - Plan service roadmap

**System Architecture:**
1. C2 - Enterprise vision
2. L1/L2 - Logical architecture
3. P1/P2 - Physical architecture
4. A2 - Document architecture products
5. Ar/Pr/Sr - Roadmap planning

## Reference

For detailed information about each viewpoint category, consult the specialized skills:
- **Requirements details** → nafv4-requirements skill
- **Architecture Metadata details** → nafv4-architecture-metadata skill
- **Concepts details** → nafv4-concepts skill
- **Logical Specification details** → nafv4-logical-specification skill
- **Physical Resources details** → nafv4-physical-resources skill
- **Service Specification details** → nafv4-service-specification skill

## Profile Information

All NAF v4 / ADMBw elements and relationships use profile: **NAFv4-ADMbw**

Diagram types:
- **Most viewpoints**: Custom (Diagram_Custom)
- **Sequence diagrams**: L6, P6, S6 use Sequence (Diagram_Sequence)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/carstenlucke) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
