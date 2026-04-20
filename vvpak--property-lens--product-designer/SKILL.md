---
name: product-designer
description: Skill for UI/UX design, design system management, and data visualization architecture. Use for creating wireframes, defining visual hierarchy, and ensuring B2B usability. Use when this capability is needed.
metadata:
  author: vvpak
---

# Product Designer Skill

## Objective
Design high-performance, data-driven interfaces for Property Lens that minimize time-to-insight for B2B investors and developers.

## Design Principles (Pragmatic Focus)
1. **Data Density vs. Clarity**: Prioritize high data density without clutter. Use progressive disclosure for complex real estate metrics.
2. **Visual Hierarchy**: Critical financial KPIs (ROI, Price per SQFT) must have the highest visual weight.
3. **Consistency**: Adhere strictly to the defined design system (typography, spacing, component states).
4. **Mobile-First for On-the-Go**: All analytics must be legible on mobile devices for investors "in the field."

## Protocol: Designing a Component
1. **User Persona**: Is this for a Broker (speed), an Investor (depth), or a Developer (trends)?
2. **Success Metric**: What is the primary action on this screen? (e.g., "Analyze Area", "Export Report").
3. **Constraint Check**: Validate accessibility (WCAG 2.1) and technical feasibility (responsive breakpoints).

## Data Visualization Guidelines
- **Color Logic**: 
  - Green: Growth / Positive ROI.
  - Red: Decline / Negative Trends.
  - Neutral/Blue: Information / Neutral data.
- **Chart Selection**:
  - Time Series: For price history.
  - Bar Charts: For comparing supply vs demand across districts.
  - Treemaps: For portfolio distribution.
- **Empty States**: Always design "No Data Found" states with clear CTA to adjust filters.

## Technical Standards
- **Grid System**: 12-column grid for desktop, 4-column for mobile.
- **Typography**: Optimized for readability of numerical data (monospaced numbers where alignment is critical).
- **Design-to-Code**: Ensure all designs are exportable via Figma/Tokens for rapid frontend implementation.

## Agent Instructions
When asked to "design a screen," "improve UI," or "create a dashboard":
1. Map out the user flow and identify the "Hero Metric."
2. Propose a layout structure (Wireframe logic) before detailing styles.
3. Justify design decisions with UX laws (e.g., Fitts's Law, Hick's Law) and ROI impact.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vvpak) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
