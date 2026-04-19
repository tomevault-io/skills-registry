---
name: create-an-edge-app
description: Use when working with the recommended way to create an Edge App
metadata:
  author: screenly
---

# Creating an Edge App

## When Creating an Edge App

- Scaffold the new Edge App using the `bun create` template from inside the `edge-apps/` directory:
  ```bash
  bun create edge-app-template --no-git <app-name>
  ```
  - The app name should follow the `kebab-case` naming convention.
- After scaffolding, add an `id` field to `screenly.yml` and `screenly_qc.yml` before running `bun run dev`.
- **Consult Figma designs** before starting implementation.
  - Ensure the [Figma MCP server](https://mcp.figma.com/mcp) is set up in Claude Code.
  - Use the Figma MCP server to access design specifications, mockups, and UI requirements.
  - Extract design tokens such as colors, spacing, typography, and component specifications from Figma.
  - Ensure the implementation matches the approved designs in Figma before proceeding with development.

## Reference Apps

For reference on more complex implementations, consult:

- QR Code (`edge-apps/qr-code/`) — simple, low-footprint example
- Menu Board (`edge-apps/menu-board/`) — more complex layout
- CAP Alerting (`edge-apps/cap-alerting/`) — advanced settings and data fetching

All apps depend on the `@screenly/edge-apps` NPM package and use `edge-apps-scripts` for tooling.

### About the Manifest Files

- Update the `categories` key to reflect the app's purpose.
- Add any app-specific settings under the `settings` key, sorted alphabetically.
- More information about manifest files can be found in the [Edge Apps documentation in the `Screenly/cli` repository](https://raw.githubusercontent.com/Screenly/cli/refs/heads/master/docs/EdgeApps.md).

### About `index.html`

- Organize HTML code into templates and Web Components as the app grows in complexity.
- Use HTML content templates first for simpler structures.
- Consider using Web Components for more complex UI components that require encapsulation and reusability.

### About `README.md`

- Include instructions on how to create, build, test, format, lint, and deploy the app.
- Do not add details like the directory structure, as the code frequently changes.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/screenly) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
