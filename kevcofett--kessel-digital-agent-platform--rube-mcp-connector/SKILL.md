---
name: rube-mcp-connector
description: > Use when this capability is needed.
metadata:
  author: kevcofett
---

# Rube MCP Connector

## Commands

- `/rube connect <app>` — Connect to a specific app via MCP
- `/rube status` — Show connection status for all configured services
- `/rube list-apps` — List all available app integrations
- `/rube disconnect <app>` — Remove a connection

## Overview

Rube provides a single MCP gateway that routes to multiple backend services. Instead of configuring individual MCP servers for each integration, Rube acts as a unified proxy.

## Procedure

### Connect a New App

1. Check if the app is available: `/rube list-apps`
2. Initiate connection: `/rube connect <app>`
3. Follow authentication flow (OAuth, API key, etc.)
4. Verify connection: `/rube status`

### Check Status

1. Query all configured connections
2. Report status for each: CONNECTED / DISCONNECTED / ERROR
3. For errors, show the error message and suggested fix

### Available Integrations

Rube supports integrations across categories:

- Communication: Slack, Discord, Microsoft Teams
- Email: Gmail, Outlook
- Project Management: Jira, Linear, Asana, Trello
- Code: GitHub, GitLab, Bitbucket
- Documents: Google Docs, Notion, Confluence
- CRM: Salesforce, HubSpot
- Cloud: AWS, Azure, GCP
- Data: PostgreSQL, MySQL, MongoDB

## Configuration

Rube configuration is stored in `.mcp.json` at the repo root. The gateway entry handles routing to all backends.

## MCMAP Usage

Primary integrations for this project:
- GitHub (kevcofett/Kessel-Digital-Agent-Platform)
- SharePoint (kesseldigitalcom.sharepoint.com)
- Dataverse (via pac CLI, not Rube — use pac directly)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kevcofett) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
