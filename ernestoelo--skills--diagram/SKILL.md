---
name: diagram
description: Use when the user asks to "create a diagram", "generate PlantUML", "draw architecture", mentions "PlantUML", "Graphviz", ".puml", ".dot", or discusses architecture and workflow diagrams.
metadata:
  author: ernestoelo
---

# Diagram Generator Skill

Generates diagrams using PlantUML and Graphviz (dot) for architecture, workflows, and documentation. Mermaid is fully discarded. Provides bundled templates and examples for quick setup and reproducibility.

## When to Use
- Creating system architecture diagrams.
- Documenting workflows like Gitflow.
- Generating visual overviews for projects.
- Ensuring consistency in team diagrams.

## Usage
Follow this unified workflow for diagram generation and automation:

1. **Install PlantUML and Graphviz**: On Arch Linux, run `sudo pacman -S plantuml graphviz` (see compatibility and best practices in @sys-env).
2. **Choose template**: Use assets/ (examples/, imgs/, templates/) as reference.
3. **Edit .puml or .dot files**: Create or edit files using PlantUML syntax (`@startuml ... @enduml`) or Graphviz (`digraph {...}`).
4. **Generate diagrams automatically**: Run the script `scripts/generate_diagrams.sh [directory]` to convert all .puml and .dot files to PNG/SVG using PlantUML and Graphviz (see scripts/README.md). This enables integration and reproducibility, aligned with @architect and @dev-workflow.
5. **Integrate and version**: Add generated PNG/SVG files to documentation/repositories and version .puml/.dot files in Git.

## Anatomy
Skill structure:
- SKILL.md (metadata and instructions)
- scripts/ (automation and utilities)
- assets/ (resources: examples/, imgs/, templates/)

## Best Practices
- Use assets to maintain visual consistency.
- Version .puml and .dot files in Git (as source code).
- Automate diagram generation with scripts/ for reproducibility.
- Integrate with @dev-workflow for robust workflows.
- Consult @sys-env before installing dependencies or modifying the environment.

## Resources
- Included assets: see assets/ for templates and examples.
- scripts/: automation utilities.
- PlantUML and Graphviz documentation.
- Inspiration: @anthropic-examples (theme-showcase.pdf for diagram styles).
- Cross-references: @architect for structure and automation, @dev-workflow for integration, @sys-env for safe environment.
## Example
- PlantUML: assets/examples/skill_structure_example.puml (compliant syntax, only top-level folders)
- Graphviz: add .dot examples in assets/examples/

## Company Standards
- Templates and reference diagrams are located in assets/templates/ and assets/examples/.
- Standard workflow diagrams (Gitflow, branches) are in assets/imgs/.
- All PlantUML examples must use only top-level folders, no nested folders or files, to avoid syntax errors and ensure compatibility.
- See assets/templates/system-architecture.png, system-overview.png, system-prod-env.png for company architecture standards.
- See assets/examples/biomass-web-architecture.png, biomass-web-overview.png, biomass-web-prod-env.png for real project examples.
- See assets/imgs/gitflow.png, gitflow-feature-branch.png, gitflow-release-branch.png for workflow standards.

## PlantUML Syntax Rules
- Use only top-level folders or rectangles — avoid nested folders/files to prevent syntax errors
- Use explicit arrows (`-->`), rectangles, and hidden links for valid visualization
- All diagrams must be based on templates in assets/templates/ and assets/examples/
- Only PlantUML and Graphviz are supported (no Mermaid)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ernestoelo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
