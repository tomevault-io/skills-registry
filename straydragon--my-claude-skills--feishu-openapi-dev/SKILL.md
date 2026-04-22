---
name: feishu-openapi-dev
description: Expert guidance for Feishu (飞书) / Lark OpenAPI Python development. Build Feishu applications, robots, handle event subscriptions, card callbacks, and API integrations. Use when working with Feishu SDK, lark-oapi, building Feishu bots, or mentioning 飞书 development. Use when this capability is needed.
metadata:
  author: straydragon
---

# Feishu OpenAPI Python Development Expert

Expert guidance for Feishu (飞书) / Lark Open Platform Python development, covering API calls, event handling, robot development, and more.

## 📚 Source Documentation

This skill includes three official/community source repositories (managed via git submodule):

### 1. oapi-sdk-python (Official SDK)

**Path**: `source/oapi-sdk-python/`

Official Feishu Python SDK providing complete type system and semantic programming interface.

**Core Directories**:
- `lark_oapi/` - SDK core code
- `samples/` - Official sample code
- `doc/` - Documentation resources

**Main Features**:
- Server-side API calls
- Event subscription handling
- Card callback processing
- Automatic access_token management
- Data encryption/decryption and signature verification

### 2. oapi-sdk-python-compact (Convenience Wrapper)

**Path**: `source/oapi-sdk-python-compact/`

Enhanced wrapper based on official SDK, providing convenient shortcut functions.

**Core Directories**:
- `src/lark_oapi_compact/shortcut/` - High-level convenience APIs
  - `sheets/` - 电子表格 (Spreadsheet) operations
  - `driver/` - 云文档/云空间 (Drive) operations
  - `group_robot/` - 群机器人 (Group robot) messaging
  - `message/` - Message handling
  - `compact/` - Core configuration
- `tests/` - Test cases

**Configuration Guide**: See `CLAUDE.md` for development setup instructions.

### 3. lark-samples (Official Examples)

**Path**: `source/lark-samples/`

Official Feishu sample code collection with complete scenario-based implementations.

**Example Projects**:
- `robot_quick_start/` - Quick start robot development
- `web_app_with_jssdk/` - 网页应用 (Web app) development
- `web_app_with_auth/` - Web app with authentication
- `echo_bot/` - Echo bot (multi-language)
- `card_interaction_bot/` - Card interaction bot
- `mcp_larkbot_demo/` - MCP intelligent Agent
- `mcp_quick_demo/` - MCP quick start

## Quick Start

### Installation

```bash
pip install lark-oapi
# Or use the enhanced version
pip install lark-oapi-compact
```

### Basic Configuration

```python
import lark_oapi as lark

# Create client
client = lark.Client.builder() \
    .app_id("your_app_id") \
    .app_secret("your_app_secret") \
    .build()
```

### Environment Variables

Development and testing require these environment variables:
- `FEISHU_APP_ID` - Application ID (应用 ID)
- `FEISHU_APP_SECRET` - Application Secret (应用密钥)
- `FEISHU_GROUP_ROBOT_WEBHOOK_URL` - Group robot Webhook (optional)

## Usage Guide

### Finding API Usage

1. **Basic API calls**: Check `source/oapi-sdk-python/samples/`
2. **Convenience wrappers**: Check `source/oapi-sdk-python-compact/src/lark_oapi_compact/shortcut/`
3. **Complete scenarios**: Check corresponding example projects in `source/lark-samples/`

### Recommended Development Workflow

1. Identify your scenario (机器人/robot, 网页应用/web app, API call)
2. Find similar examples in `lark-samples`
3. Use `oapi-sdk-python` for API calls
4. For complex scenarios, use `oapi-sdk-python-compact` shortcut functions

## Updating Source

```bash
# Update all submodules
cd source
git submodule update --remote

# Update single repository
cd source/oapi-sdk-python
git pull origin v2_main
```

## Official Resources

- [Feishu Open Platform Docs (飞书开放平台文档)](https://open.feishu.cn/document/home/index)
- [Python SDK Documentation](https://open.feishu.cn/document/uAjLw4CM/ukTMukTMukTM/server-side-sdk/python--sdk/preparations-before-development)
- [API Explorer](https://open.feishu.cn/api-explorer/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/straydragon) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
