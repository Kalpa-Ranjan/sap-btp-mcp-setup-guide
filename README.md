# ⚡ SAP BTP Administration MCP Server Integration Guide

[![SAP BTP](https://img.shields.io/badge/SAP%20BTP-Administration-008FD3?style=for-the-badge&logo=sap&logoColor=white)](https://help.sap.com)
[![Model Context Protocol](https://img.shields.io/badge/MCP-Protocol-7057ff?style=for-the-badge&logo=json&logoColor=white)](https://modelcontextprotocol.io)
[![Antigravity](https://img.shields.io/badge/Client-Antigravity--IDE-4285F4?style=for-the-badge)](https://cloud.google.com)
[![Claude](https://img.shields.io/badge/Client-Claude%20Desktop%2FCode-D97706?style=for-the-badge&logo=anthropic&logoColor=white)](https://anthropic.com)

A comprehensive, security-first guide for configuring and connecting the **SAP BTP Administration MCP (Model Context Protocol) Server** with **Antigravity IDE**, **Claude Desktop App**, **Claude Code CLI**, and **Claude.ai Web Connectors**.

---

## 📌 Table of Contents
- [📖 Overview](#-overview)
- [🔑 Prerequisites & Authentication Modes](#-prerequisites--authentication-modes)
- [🚀 Setup Guide for Antigravity IDE](#-setup-guide-for-antigravity-ide)
- [🤖 Setup Guide for Claude](#-setup-guide-for-claude)
  - [Option A: Claude Desktop App (Windows / macOS)](#option-a-claude-desktop-app-windows--macos)
  - [Option B: Claude Code CLI (Single Sign-On)](#option-b-claude-code-cli-single-sign-on)
  - [Option C: Claude.ai Web Custom Connectors](#option-c-claudeai-web-custom-connectors)
- [🛠️ Troubleshooting & Technical Notes](#%EF%B8%8F-troubleshooting--technical-notes)
- [🔐 Security Best Practices](#-security-best-practices)

---

## 📖 Overview

The **MCP Server for SAP BTP Administration** allows AI agents and assistants to query, manage, and navigate your SAP Business Technology Platform (BTP) accounts using natural language.

| Client / Interface | Support Status | Preferred Auth Mode | Transport Type |
| :--- | :---: | :--- | :--- |
| **Google Antigravity IDE** | 🟢 Supported | Direct Connection (Headers) | Native `streamableHttp` |
| **Claude Desktop App** | 🟢 Supported | Direct Connection (Headers) | Native `streamableHttp` |
| **Claude Code CLI** | 🟢 Supported | Single Sign-On (SSO) | HTTP (`claude mcp add`) |
| **Claude.ai Web Connectors** | 🟢 Supported | Direct Connection (Headers) | Web Connectors GUI |

---

## 🔑 Prerequisites & Authentication Modes

Before starting, determine which authentication method applies to your SAP setup:

### 1️⃣ Single Sign-On (SSO)
* **When to use**: Your BTP platform user originates from default identity provider (`accounts.sap.com`).
* **Endpoint URL**: `https://sso.mcp.btp.cloud.sap/mcp`
* **Static OAuth Client ID**: `e789ba01-5612-47ee-bfe7-79e26411c1ca`

### 2️⃣ Direct Connection
* **When to use**: Your BTP user comes from a custom Identity Authentication Service (IAS) tenant.
* **Endpoint URL**: `https://proxy.c-769d49e.kyma.ondemand.com/mcp`
* **Credentials Required**:
  - `Authorization`: `Basic <Base64-encoded username:password>`
  - `X-Platform-Origin`: `<your-ias-tenant-subdomain>`

> [!TIP]
> **How to Base64 encode credentials (without terminal commands)**:
> 1. Open browser developer console (`F12` -> Console tab).
> 2. Execute: `btoa("YOUR_USERNAME:YOUR_PASSWORD")`
> 3. Copy the resulting encoded string.

---

## 🚀 Setup Guide for Antigravity IDE

Antigravity IDE supports native Streamable HTTP connections directly in `mcp_config.json`.

### Step-by-Step Configuration:

1. Click **`Open MCP Config`** at the top right of the Installed MCP Servers panel.
2. Update your `mcp_config.json` file:

```json
{
  "mcpServers": {
    "BTP Administration": {
      "url": "https://proxy.c-769d49e.kyma.ondemand.com/mcp",
      "headers": {
        "Authorization": "Basic <YOUR_BASE64_ENCODED_CREDENTIALS>",
        "X-Platform-Origin": "<YOUR_IAS_TENANT_SUBDOMAIN>",
        "Accept": "application/json, text/event-stream",
        "Content-Type": "application/json"
      }
    }
  }
}
```

3. Save the file and click **Refresh 🔄** in Antigravity.
4. The server status light will turn **green (`●`)**, enabling BTP Cockpit tools (e.g. `Navigation-buildLink`).

---

## 🤖 Setup Guide for Claude

### Option A: Claude Desktop App (Windows / macOS)

Claude Desktop app configuration file locations:
- **Windows (Standard)**: `%APPDATA%\Claude\claude_desktop_config.json`
- **Windows (MSIX / Store App)**: `%LOCALAPPDATA%\Packages\Claude_*\LocalCache\Roaming\Claude\claude_desktop_config.json`
- **macOS**: `~/Library/Application Support/Claude/claude_desktop_config.json`

#### Configuration JSON:

```json
{
  "mcpServers": {
    "BTP Administration": {
      "url": "https://proxy.c-769d49e.kyma.ondemand.com/mcp",
      "headers": {
        "Authorization": "Basic <YOUR_BASE64_ENCODED_CREDENTIALS>",
        "X-Platform-Origin": "<YOUR_IAS_TENANT_SUBDOMAIN>",
        "Accept": "application/json, text/event-stream",
        "Content-Type": "application/json"
      }
    }
  }
}
```

*Save the file and completely restart Claude Desktop.*

---

### Option B: Claude Code CLI (Single Sign-On)

To connect using Claude Code CLI with SSO:

1. Open terminal and run:
   ```bash
   claude mcp add --transport http BTP-Administration \
     "https://sso.mcp.btp.cloud.sap/mcp" \
     --client-id e789ba01-5612-47ee-bfe7-79e26411c1ca
   ```
2. Start Claude Code: `claude`
3. Type `/mcp` and select `BTP-Administration`.
4. Complete SAP login in the browser window (`http://127.0.0.1:*`).

---

### Option C: Claude.ai Web Custom Connectors

When adding via Claude Web UI (**Connectors > Add Custom Connector**):

| Setting Field | Configuration Value |
| :--- | :--- |
| **Connector Name** | `BTP Administration` |
| **Server URL** | `https://proxy.c-769d49e.kyma.ondemand.com/mcp` |
| **Header 1** | `Authorization`: `Basic <YOUR_BASE64_ENCODED_CREDENTIALS>` |

> [!WARNING]
> Do NOT select SSO OAuth on `claude.ai` Web Connectors. SAP blocks `https://claude.ai/api/mcp/auth_callback` redirect URIs. Use Direct Connection Basic Authentication instead.

---

## 🛠️ Troubleshooting & Technical Notes

| Issue / Error | Cause | Resolution |
| :--- | :--- | :--- |
| `401 Unauthorized` | Missing or invalid `Authorization` header | Verify Base64 string encoding (`username:password`) |
| `406 Not Acceptable` | Missing stream headers | Include `Accept: application/json, text/event-stream` |
| `SyntaxError: Unexpected token 'o', "[object Response]"` | `mcp-remote` attempted OAuth DCR | Use native `url` transport instead of `mcp-remote` |
| `OpenID provider cannot process the request` | Web redirect URI not whitelisted by SAP | Use Direct Connection headers or local CLI loopback |

---

## 🔐 Security Best Practices

> [!CAUTION]
> - Never commit real Base64 credentials or passwords into source code or public repositories.
> - Store credentials in local environment configuration files or secure password managers.
> - BTP Administration MCP operates with full privileges of your SAP user identity. Restrict trial accounts to separate test emails.

---
