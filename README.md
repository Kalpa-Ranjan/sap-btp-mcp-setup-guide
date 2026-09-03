# ⚡ SAP BTP Administration MCP Server Integration Guide

[![Claude](https://img.shields.io/badge/Client-Claude%20Desktop-D97706?style=for-the-badge&logo=anthropic&logoColor=white)](https://anthropic.com)
[![Antigravity](https://img.shields.io/badge/Client-Antigravity--IDE-4285F4?style=for-the-badge)](https://cloud.google.com)
[![SAP BTP](https://img.shields.io/badge/SAP%20BTP-Administration-008FD3?style=for-the-badge&logo=sap&logoColor=white)](https://help.sap.com)
[![Model Context Protocol](https://img.shields.io/badge/MCP-Protocol-7057ff?style=for-the-badge&logo=json&logoColor=white)](https://modelcontextprotocol.io)

A clean, security-first guide for configuring the **SAP BTP Administration MCP (Model Context Protocol) Server** directly using app GUI settings for **Claude Desktop App** and **Google Antigravity IDE** — without requiring CLI commands or web connectors.

---

## 📌 Table of Contents
- [📖 Architecture Overview](#-architecture-overview)
- [🔑 Prerequisites & Authentication](#-prerequisites--authentication)
- [🤖 Setup Guide for Claude Desktop App](#-setup-guide-for-claude-desktop-app)
  - [Method 1: Extensions Form GUI (UI Setup)](#method-1-extensions-form-gui-ui-setup)
  - [Method 2: Developer Config File (JSON Setup)](#method-2-developer-config-file-json-setup)
- [🚀 Setup Guide for Antigravity IDE](#-setup-guide-for-antigravity-ide)
- [🛠️ Troubleshooting & Technical Notes](#%EF%B8%8F-troubleshooting--technical-notes)
- [🔐 Security Best Practices](#-security-best-practices)

---

## 📖 Architecture Overview

This configuration model operates **100% locally via GUI menus**, connecting your AI desktop clients directly to the SAP BTP Administration proxy endpoint without needing terminal CLI tools or Web Connectors.

| Client / Interface | GUI Navigation Path | Auth Method | Terminal CLI Needed? | Web Connector Needed? |
| :--- | :--- | :--- | :---: | :---: |
| **Claude Desktop App** | `Profile ➔ Settings ➔ Extensions / Developer` | Direct Connection | ❌ No | ❌ No |
| **Google Antigravity IDE** | `Installed MCP Servers ➔ Open MCP Config` | Direct Connection | ❌ No | ❌ No |

---

## 🔑 Prerequisites & Authentication

### Direct Connection Mode
* **Endpoint URL**: `https://proxy.c-769d49e.kyma.ondemand.com/mcp`
* **Credentials Required**:
  - `Authorization`: `Basic <Base64-encoded username:password>`
  - `X-Platform-Origin`: `<your-ias-tenant-subdomain>` (when using config JSON)

> [!TIP]
> **How to Base64 encode credentials (in browser)**:
> 1. Open browser console (`F12` -> Console tab).
> 2. Execute: `btoa("YOUR_USERNAME:YOUR_PASSWORD")`
> 3. Copy the resulting string.

---

## 🤖 Setup Guide for Claude Desktop App

You can configure Claude Desktop using either the **Extensions Form GUI** or the **Developer Config File**.

### Method 1: Extensions Form GUI (UI Setup)

1. Open **Claude Desktop App**.
2. Click your **Profile Icon / Avatar** ➔ **Settings**.
3. Go to **Extensions** (or *Connectors*) and click **Add Custom Extension** (or *Add*).
4. Fill in the fields:
   - **Name**: `BTP Administration`
   - **URL**: `https://proxy.c-769d49e.kyma.ondemand.com/mcp`
   - **Authentication**:
     - `Authorization`: `Basic <YOUR_BASE64_ENCODED_CREDENTIALS>`
5. Click **Save / Add**.
6. Open a new chat in Claude Desktop, click the **🔌 (hammer / MCP tools)** icon, and verify `BTP Administration` is active.

---

### Method 2: Developer Config File (JSON Setup)

1. Open **Claude Desktop App**.
2. Click your **Profile Icon / Avatar** ➔ **Settings**.
3. Go to the **Developer** tab and click **Edit Config** (opens `claude_desktop_config.json`).
4. Add the **BTP Administration** server configuration:

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

5. Save the file and completely **Quit and Restart Claude Desktop**.

---

## 🚀 Setup Guide for Antigravity IDE

### Step-by-Step GUI Process:

1. Open **Antigravity IDE**.
2. In the sidebar panel, expand **Installed MCP Servers**.
3. Click the **`Open MCP Config`** button (top right of panel).
4. Update your `mcp_config.json` file:

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

5. Save the file and click **Refresh 🔄** in Antigravity.
6. The server status light will turn **green (`●`)**, enabling BTP Cockpit tools (e.g. `Navigation-buildLink`).

---

## 🛠️ Troubleshooting & Technical Notes

| Issue / Error | Cause | Resolution |
| :--- | :--- | :--- |
| `401 Unauthorized` | Missing or invalid `Authorization` header | Verify Base64 string encoding (`username:password`) |
| `406 Not Acceptable` | Missing stream headers | Include `Accept: application/json, text/event-stream` |
| `SyntaxError: Unexpected token 'o', "[object Response]"` | `mcp-remote` attempted OAuth DCR | Use native `url` transport instead of `mcp-remote` |
| `OpenID provider cannot process the request` | Web redirect URI not whitelisted by SAP | Use Direct Connection headers in desktop app |

---

## 🔐 Security Best Practices

> [!CAUTION]
> - Never commit real Base64 credentials or passwords into source code or public repositories.
> - Store credentials in local environment configuration files or secure password managers.
> - BTP Administration MCP operates with full privileges of your SAP user identity. Restrict trial accounts to separate test emails.

---
