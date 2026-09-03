# ⚡ SAP BTP Administration MCP Server Integration Guide

[![SAP BTP](https://img.shields.io/badge/SAP%20BTP-Administration-008FD3?style=for-the-badge&logo=sap&logoColor=white)](https://help.sap.com)
[![Model Context Protocol](https://img.shields.io/badge/MCP-Protocol-7057ff?style=for-the-badge&logo=json&logoColor=white)](https://modelcontextprotocol.io)
[![Antigravity](https://img.shields.io/badge/Client-Antigravity--IDE-4285F4?style=for-the-badge)](https://cloud.google.com)
[![Claude](https://img.shields.io/badge/Client-Claude%20Desktop-D97706?style=for-the-badge&logo=anthropic&logoColor=white)](https://anthropic.com)

A clean, security-first guide for configuring the **SAP BTP Administration MCP (Model Context Protocol) Server** directly using local configuration files for **Antigravity IDE** and **Claude Desktop App** — without requiring CLI commands or web connectors.

---

## 📌 Table of Contents
- [📖 Architecture Overview](#-architecture-overview)
- [🔑 Prerequisites & Authentication](#-prerequisites--authentication)
- [🚀 Setup Guide for Antigravity IDE](#-setup-guide-for-antigravity-ide)
- [🤖 Setup Guide for Claude Desktop App](#-setup-guide-for-claude-desktop-app)
- [🛠️ Troubleshooting & Technical Notes](#%EF%B8%8F-troubleshooting--technical-notes)
- [🔐 Security Best Practices](#-security-best-practices)

---

## 📖 Architecture Overview

This configuration model operates **100% locally via GUI configuration files**, connecting your AI desktop clients directly to the SAP BTP Administration proxy endpoint without needing terminal CLI tools or Web Connectors.

| Client / Interface | Configuration File | Auth Method | Terminal CLI Needed? | Web Connector Needed? |
| :--- | :--- | :--- | :---: | :---: |
| **Google Antigravity IDE** | `mcp_config.json` | Direct Connection | ❌ No | ❌ No |
| **Claude Desktop App** | `claude_desktop_config.json` | Direct Connection | ❌ No | ❌ No |

---

## 🔑 Prerequisites & Authentication

### Direct Connection Mode
* **Endpoint URL**: `https://proxy.c-769d49e.kyma.ondemand.com/mcp`
* **Headers Required**:
  - `Authorization`: `Basic <Base64-encoded username:password>`
  - `X-Platform-Origin`: `<your-ias-tenant-subdomain>`
  - `Accept`: `application/json, text/event-stream`
  - `Content-Type`: `application/json`

> [!TIP]
> **How to Base64 encode credentials (in browser)**:
> 1. Open browser console (`F12` -> Console tab).
> 2. Execute: `btoa("YOUR_USERNAME:YOUR_PASSWORD")`
> 3. Copy the resulting string.

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

## 🤖 Setup Guide for Claude Desktop App

Claude Desktop app configuration file location:
- **Windows (Standard)**: `%APPDATA%\Claude\claude_desktop_config.json`
- **Windows (MSIX / Store App)**: `%LOCALAPPDATA%\Packages\Claude_*\LocalCache\Roaming\Claude\claude_desktop_config.json`
- **macOS**: `~/Library/Application Support/Claude/claude_desktop_config.json`

### Configuration JSON:

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

## 🛠️ Troubleshooting & Technical Notes

| Issue / Error | Cause | Resolution |
| :--- | :--- | :--- |
| `401 Unauthorized` | Missing or invalid `Authorization` header | Verify Base64 string encoding (`username:password`) |
| `406 Not Acceptable` | Missing stream headers | Include `Accept: application/json, text/event-stream` |
| `SyntaxError: Unexpected token 'o', "[object Response]"` | `mcp-remote` attempted OAuth DCR | Use native `url` transport instead of `mcp-remote` |
| `OpenID provider cannot process the request` | Web redirect URI not whitelisted by SAP | Use Direct Connection headers in desktop config file |

---

## 🔐 Security Best Practices

> [!CAUTION]
> - Never commit real Base64 credentials or passwords into source code or public repositories.
> - Store credentials in local environment configuration files or secure password managers.
> - BTP Administration MCP operates with full privileges of your SAP user identity. Restrict trial accounts to separate test emails.

---
