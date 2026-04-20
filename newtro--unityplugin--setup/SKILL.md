---
name: setup
description: Set up and validate the Unity MCP server connection. Checks Python/uv installation, starts the MCP server, and verifies communication with Unity Editor. Use when this capability is needed.
metadata:
  author: newtro
---

Help the user set up the CoplayDev/unity-mcp server for their Unity project.

## Steps

1. **Check prerequisites**: Verify Python 3.10+ and uv are installed
2. **Install MCP server**: Run `uvx --from mcpforunityserver mcp-for-unity` if not already installed
3. **Check Unity package**: Verify the MCPForUnity package is installed in the Unity project
4. **Configure Claude Code**: Set up the MCP server in Claude Code settings
5. **Validate connection**: Test that Claude Code can communicate with Unity Editor
6. **Report status**: Show what tools are available and confirm everything works

## Troubleshooting

If the connection fails:
- Ensure Unity Editor is open with the project loaded
- Check that the MCPForUnity package is properly imported
- Verify no firewall is blocking localhost connections
- Check Unity Console for MCP-related error messages

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/newtro) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
