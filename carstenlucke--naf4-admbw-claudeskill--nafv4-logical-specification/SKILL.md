---
name: nafv4-logical-specification
description: Create and manage NAF v4 (NATO Architecture Framework) / ADMBw Logical Specification Viewpoints (L1-L8, Lr) in Sparx Enterprise Architect. Use when the user wants to create L1 (Node Types), L2 (Logical Scenario), L3 (Node Interactions), L4 (Logical Activities), L5 (Logical States), L6 (Logical Sequence), L7 (Information Model), L8 (Logical Constraints), or Lr (Lines of Development) diagrams, add logical elements, create flows and interactions, or work with NAF logical architecture modeling. Also triggers on natural language like "operational performer", "logical node", "operational activity", "information exchange", "logical flow", etc. Use when this capability is needed.
metadata:
  author: carstenlucke
---

# NAF v4 Logical Specification Modeling for Sparx Enterprise Architect

## Overview

This skill enables natural language interaction with Sparx Enterprise Architect's MCP server to create NAF v4 / ADMBw compliant Logical Specification Viewpoints. It translates informal user requests into precise MCP tool calls with correct stereotypes, UML types, and profiles.

## Core Workflow

When the user requests NAF logical specification modeling:

1. **Parse the request** - Identify what the user wants (diagram, element, or association)
2. **Map to NAF metamodel** - Translate natural language to formal stereotypes using references
3. **Execute MCP calls** - Create or update models in Sparx EA
4. **Confirm and offer next steps** - Show what was created and suggest related actions

## Key Principles

- **Interpret flexibly** - Accept natural language like "add a logical node" or "create information flow"
- **Map precisely** - Always use exact stereotypes and UML types from the metamodel
- **Auto-name when needed** - If user provides description but no name, generate concise technical name
- **Validate connections** - Check metaconstraints before creating associations (see JSON data)
- **Ask when ambiguous** - Offer options if request could map to multiple stereotypes

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

## Supported Viewpoints

| Viewpoint | ID | Purpose | Common Requests |
|-----------|----|---------|-----------------|
| Node Types | L1 | Define nodes with measures of performance | "Create L1 diagram", "Add operational performer", "Define node capabilities" |
| Logical Scenario | L2 | Specify nodes in context with flows | "Create L2 diagram", "Show information flows", "Add logical scenario" |
| Node Interactions | L3 | Detail interoperability requirements | "Create L3 diagram", "Add operational exchange", "Define node interactions" |
| Logical Activities | L4 | Describe logical operational activities | "Create L4 diagram", "Add operational activity", "Show activity flows" |
| Logical States | L5 | Specify node states and transitions | "Create L5 diagram", "Add state machine", "Define state transitions" |
| Logical Sequence | L6 | Trace event sequences between nodes | "Create L6 diagram", "Add sequence diagram", "Show operational messages" |
| Information Model | L7 | Document business information | "Create L7 diagram", "Add information element", "Define data model" |
| Logical Constraints | L8 | Constrain logical architecture | "Create L8 diagram", "Add operational constraint", "Define logical rules" |
| Lines of Development | Lr | Support acquisition and dependencies | "Create Lr diagram", "Add project milestone", "Show capability roadmap" |

## Creating Diagrams

To create a NAF logical specification diagram, use the MCP `create_or_update_diagram` tool:

```javascript
{
  "name": "<diagram-name>",
  "type": "<diagram-type>",  // "Custom" for L1-L5, L7, L8, Lr; "Sequence" for L6
  "stereotype": "<viewpoint-identifier>",  // e.g. "L1", "L2", "L3", "L4", "L5", "L6", "L7", "L8", "Lr"
  "packagePath": "<package-path>",  // e.g. "Model/Logical Specification"
  "extendedProperties": {
    "alias": "<full-viewpoint-name>",  // e.g. "L1 - Node Types"
    "diagramID": "<viewpoint-id>",     // e.g. "L1"
    "toolbox": "<toolbox-name>"        // e.g. "NAFv4-ADMBw-L1-Toolbox"
  }
}
```

**Important:** L6 (Logical Sequence) uses diagram type "Sequence", all other viewpoints use "Custom".

**Example user requests:**
- "Create an L1 diagram called 'Surveillance System Nodes'"
- "Make a new Logical Scenario view"
- "I need an L4 diagram for operational activities"
- "Create a sequence diagram for L6"

## Creating Elements

To create a NAF logical specification element, use the MCP `create_or_update_element` tool:

```javascript
{
  "name": "<element-name>",
  "type": "<uml-type>",           // e.g. "Class", "Activity", "Part", "Port", "Object", "StateMachine"
  "stereotype": "<NAF-stereotype>", // e.g. "OperationalPerformer", "OperationalActivity"
  "packagePath": "<package-path>",
  "notes": "<description>",        // User's full description text
  "profile": "NAFv4-ADMBw"        // Always use this profile
}
```

**Auto-naming logic:**
When user provides description but no name, generate a concise technical identifier:
- Extract key concepts from description
- Use PascalCase or hyphenated format
- Keep to 3-5 words maximum
- Example: "Command and control node" → "CommandControlNode" or "C2-Node"

**Example user requests:**
- "Add operational performer called 'ISR Node'"
- "Create OperationalActivity named 'DetectTargets'"
- "Add an information element for 'Target Location Data'"
- "Create a state machine for the radar node"
- "Add operational performer: A logical node responsible for command and control functions"

## Creating Associations

To create connections between elements, use the MCP `create_or_update_connector` tool:

```javascript
{
  "sourceElementName": "<source-element>",
  "targetElementName": "<target-element>",
  "connectorType": "<uml-connector-type>",  // e.g. "Connector", "InformationFlow", "Dependency", "Abstraction"
  "stereotype": "<NAF-stereotype>",          // e.g. "OperationalExchange", "IsCapableToPerform"
  "packagePath": "<package-path>",
  "profile": "NAFv4-ADMBw"
}
```

**Before creating associations, validate:**
1. Load `references/logical_specification_viewpoints.json` if not already in context
2. Check metaconstraints for the desired stereotype
3. Ensure source element stereotype matches valid `client` constraint
4. Ensure target element stereotype matches valid `supplier` constraint
5. If invalid, explain to user and suggest valid alternatives

**Example user requests:**
- "Create information flow from ISR Node to C2 Node"
- "Link DetectTargets activity to ISR Node with 'capable to perform'"
- "Add operational exchange carrying 'Target Data' between nodes"
- "Show that RadarNode exhibits 'Surveillance Capability'"

## Handling Ambiguity

When user request could map to multiple stereotypes:

1. **Identify possibilities** - Check `stereotype_mappings.md` and JSON data
2. **Present 2-4 options** - Show most likely matches with brief explanation
3. **Let user choose** - Wait for clarification before proceeding
4. **Execute** - Proceed with selected option

**Example interaction:**
```
User: "Add a node to the diagram"

Claude: "I can create several types of nodes. Which would you like?

1. **OperationalPerformer** - A logical entity that performs operational activities (most common for L1/L2)
2. **OperationalRole** - A usage of a performer in a specific context
3. **OperationalArchitecture** - An entire architecture from operational perspective

Which type fits your needs?"
```

## Natural Language Mapping

Load `references/stereotype_mappings.md` for quick lookup when user uses casual terminology:
- "logical node" → OperationalPerformer
- "information flow" → OperationalExchange
- "capable to perform" → IsCapableToPerform
- "data model" → DataModel

For detailed metamodel constraints, properties, and valid connections, reference `references/logical_specification_viewpoints.json`.

## Progressive Data Loading

**Always in context:** Core workflow and mapping principles (this SKILL.md file)

**Load on demand:**
- `references/stereotype_mappings.md` - When mapping user's natural language to formal stereotypes
- `references/logical_specification_viewpoints.json` - When validating metaconstraints, checking detailed properties, or resolving complex associations

This keeps responses efficient while ensuring access to complete metamodel data when needed.

## Common Patterns

### Pattern: Create L1 Diagram with Nodes and Capabilities
```
User: "Create L1 diagram with a radar node and surveillance capability"

Actions:
1. Create L1 diagram using create_or_update_diagram
2. Create OperationalPerformer element (type: Class) for "RadarNode"
3. Create Capability element for "SurveillanceCapability"
4. Create Exhibits relationship (type: Abstraction) from RadarNode to SurveillanceCapability
5. Use place_element_on_diagram to add all elements to the diagram
6. Optional: Use layout_diagram for automatic arrangement
```

### Pattern: Create L2 Diagram with Information Flows
```
User: "Create L2 diagram showing information flows between ISR and C2 nodes"

Actions:
1. Create L2 diagram using create_or_update_diagram
2. Create OperationalPerformer "ISRNode" (type: Class)
3. Create OperationalPerformer "C2Node" (type: Class)
4. Create OperationalRole for ISRNode (type: Part)
5. Create OperationalRole for C2Node (type: Part)
6. Create OperationalConnector (type: Connector) between roles
7. Create OperationalExchange (type: InformationFlow) on connector
8. Create InformationElement "TargetData" for the exchange
9. Place all elements on diagram
```

### Pattern: Create L4 Activity Diagram
```
User: "Create L4 diagram for target detection activities"

Actions:
1. Create L4 diagram using create_or_update_diagram
2. Create OperationalActivity "DetectTargets" (type: Activity)
3. Create OperationalActivity "ClassifyTargets" (type: Activity)
4. Create OperationalActivity "ReportTargets" (type: Activity)
5. Create OperationalActivityAction elements for activity calls
6. Link activities with OperationalExchange (type: InformationFlow) for data flows
7. Add OperationalPerformer and link with IsCapableToPerform (type: Abstraction)
8. Place elements on diagram
```

### Pattern: Create L6 Sequence Diagram
```
User: "Create L6 sequence diagram for sensor-to-shooter flow"

Actions:
1. Create L6 diagram using create_or_update_diagram (type: "Sequence")
2. Create OperationalRole lifelines for each participant (SensorNode, C2Node, ShooterNode)
3. Create OperationalMessage elements (type: Message) for exchanges
4. Configure message sequence and timing
5. Place lifelines on diagram
6. Messages automatically appear between lifelines
```

### Pattern: Create L8 Constraints
```
User: "Add operational constraints for secure communications"

Actions:
1. Create or use existing L8 diagram
2. Create OperationalConstraint element (type: Class)
   {
     "name": "SecureCommunicationsConstraint",
     "type": "Class",
     "stereotype": "OperationalConstraint",
     "notes": "All operational exchanges shall use encrypted communications protocols meeting STANAG requirements.",
     "profile": "NAFv4-ADMBw"
   }
3. Create Satisfy relationship (type: Dependency) from constraint to affected elements
4. Optional: Create JustifiedBy relationship to DocumentReference for traceability
```

## Error Handling

### Element Not Found
- Use MCP `find_elements_by_name` to search for element
- If multiple matches, present list and ask user to clarify (by GUID or package path)
- If none found, offer to create the element
- Ask for element details if creation is needed

### Invalid Connection Attempt
- Load `logical_specification_viewpoints.json` and check metaconstraints
- Explain why the connection is invalid (stereotype mismatch)
- Look up valid alternatives from the same viewpoint
- Suggest correct stereotypes for both source and target

### Ambiguous Viewpoint Context
- If user says "add node" without viewpoint context, check current open diagram
- If no diagram context available, ask which viewpoint (L1-L8, Lr) they're working in
- Default to L1 for node definitions if user is unsure
- Explain briefly what each viewpoint is for

### Missing Package Path
- If package path not specified, check current package using get_current_package
- If no current package, ask user where to create element
- Suggest logical locations like "Model/Logical Specification" or similar

### Diagram Type Confusion
- Remember: L6 uses diagram type "Sequence", all others use "Custom"
- If user creates L6 with wrong type, explain the special case
- Offer to recreate with correct type if needed

## Viewpoint-Specific Guidance

### L1 - Node Types
- Focus on defining OperationalPerformer elements
- Add MeasurementType elements for measures of performance (MoPs)
- Use Exhibits relationships to link nodes to capabilities
- Use MapsToCapability to show activity-capability relationships
- Include Environment elements for operational conditions

### L2 - Logical Scenario
- Create OperationalPerformer and OperationalRole elements
- Use OperationalConnector to link roles
- Add OperationalExchange for information/resource flows
- Include InformationElement to specify what flows
- Can show flows of information, materiel, energy, or human resources

### L3 - Node Interactions
- Focus on OperationalExchange details crossing boundaries
- Use OperationalInterface and OperationalPort for interaction points
- Add ServiceSpecification for service-oriented views
- Link activities with IsCapableToPerform or PerformsInContext
- Include InformationElement details for exchanges

### L4 - Logical Activities
- Create OperationalActivity hierarchies
- Use OperationalActivityAction for activity calls
- Add OperationalExchange for activity flows
- Use ActsUpon to show activities acting on resources
- Link to OperationalPerformer with IsCapableToPerform
- Include StandardOperationalActivity for SOPs

### L5 - Logical States
- Create OperationalStateDescription (StateMachine) for nodes
- Model states and transitions for OperationalPerformer
- Show triggers, events, and actions
- Focus on behavioral aspects of nodes

### L6 - Logical Sequence
- IMPORTANT: Use diagram type "Sequence" not "Custom"
- Create OperationalRole lifelines for participants
- Add OperationalMessage for exchanges
- Can also use ServiceSpecificationRole and ServiceMessage
- Show temporal ordering of interactions

### L7 - Information Model
- Create InformationElement hierarchies
- Use InformationRole for information in context
- Add DataModel (Package) for structural specifications
- Link to L3 exchanges with Implements relationship
- Include attributes and relationships between information elements

### L8 - Logical Constraints
- Create OperationalConstraint for logical rules
- Add ResourceConstraint for implementation rules
- Use ServicePolicy for service constraints
- Create Satisfy relationships to affected elements
- Link to references with JustifiedBy or OriginatesFrom

### Lr - Lines of Development
- Create ActualProject and ActualProjectMilestone elements
- Use ProjectSequence and MilestoneDependency for dependencies
- Add IsResponsibleFor and IsAccountableFor for accountability
- Link to CapabilityConfiguration for capability delivery
- Use VersionReleased and VersionWithdrawn for releases

## Tips for Effective Usage

- **Be specific about viewpoints** - "Create L2 diagram" is clearer than generic "logical diagram"
- **Use natural language freely** - "Information flow" works as well as formal "OperationalExchange"
- **Provide context when possible** - Mentioning parent elements or current diagram helps placement
- **Combine operations** - "Create L1 with radar node exhibiting surveillance capability" is efficient and clear
- **Trust auto-naming** - For elements with long descriptions, let the skill generate concise names
- **Validate before complex operations** - For critical models, ask skill to verify connections first
- **Remember L6 is special** - It uses Sequence diagram type, not Custom

## Reference Files

This skill includes two reference files for progressive data loading:

- **references/stereotype_mappings.md** - Quick reference for stereotype lookup and natural language → formal term mapping
- **references/logical_specification_viewpoints.json** - Complete NAF v4 Logical Specification metamodel extracted from MDG with all stereotypes, properties, metaconstraints, and toolbox definitions

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/carstenlucke) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
