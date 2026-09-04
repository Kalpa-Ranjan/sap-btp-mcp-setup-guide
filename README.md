# ⚡ SAP BTP Administration MCP Server Integration Guide

[![Claude](https://img.shields.io/badge/Client-Claude%20Desktop-D97706?style=for-the-badge&logo=anthropic&logoColor=white)](https://anthropic.com)
[![Antigravity](https://img.shields.io/badge/Client-Antigravity--IDE-4285F4?style=for-the-badge)](https://cloud.google.com)
[![SAP BTP](https://img.shields.io/badge/SAP%20BTP-Administration-008FD3?style=for-the-badge&logo=sap&logoColor=white)](https://help.sap.com)
[![Model Context Protocol](https://img.shields.io/badge/MCP-Protocol-7057ff?style=for-the-badge&logo=json&logoColor=white)](https://modelcontextprotocol.io)

A security-first, step-by-step guide for connecting the **SAP BTP Administration MCP (Model Context Protocol) Server** directly to **Claude Desktop App** (via Add Extension GUI) and **Google Antigravity IDE** (via MCP Config).

---

## 📌 Table of Contents
- [📖 Architecture Overview](#-architecture-overview)
- [🔑 Prerequisites & Authentication](#-prerequisites--authentication)
- [🤖 Setup Guide for Claude Desktop App](#-setup-guide-for-claude-desktop-app)
- [🚀 Setup Guide for Antigravity IDE](#-setup-guide-for-antigravity-ide)
- [🛠️ Troubleshooting & Technical Notes](#%EF%B8%8F-troubleshooting--technical-notes)
- [🔐 Security Best Practices](#-security-best-practices)

---

## 📖 Architecture Overview

This configuration connects AI desktop clients directly to the SAP BTP Administration proxy endpoint using standard Streamable HTTP (SSE) transport with Basic Authentication.

| Client / Interface | GUI Navigation Path | Configuration Method | Auth Header Required | CLI / Web Connector Needed? |
| :--- | :--- | :--- | :--- | :---: |
| **Claude Desktop App** | `Profile ➔ Settings ➔ Extensions ➔ Add Custom Extension` | Add Extension GUI | `Authorization` | ❌ No |
| **Google Antigravity IDE** | `Installed MCP Servers ➔ Open MCP Config` | `mcp_config.json` | `Authorization` | ❌ No |

---

## 🔑 Prerequisites & Authentication

### 1. Server Endpoint
* **MCP Proxy Endpoint URL**: `https://<YOUR_BTP_MCP_PROXY_HOST>/mcp`

### 2. Basic Authentication
To generate your Base64-encoded credentials:
1. Combine your SAP user/service credentials: `USERNAME:PASSWORD`
2. Encode in Base64 (e.g. using browser dev console: `btoa("USERNAME:PASSWORD")` or terminal `echo -n "USERNAME:PASSWORD" | base64`)
3. Format as standard authorization header value:
   `Basic <YOUR_BASE64_ENCODED_CREDENTIALS>`

---

## 🤖 Setup Guide for Claude Desktop App

Claude Desktop is configured entirely via the **Add Extension** UI option without editing any configuration files.

### Step-by-Step Instructions:

1. Open **Claude Desktop App**.
2. Click your **Profile Icon / Avatar** in the app header ➔ Select **Settings**.
3. In the Settings menu, navigate to **Extensions**.
4. Click the **Add Custom Extension** (or **Add Extension**) button.
5. Fill out the extension configuration form:
   - **Extension Name**: `BTP Administration`
   - **URL / Server Endpoint**: `https://<YOUR_BTP_MCP_PROXY_HOST>/mcp`
   - **Authentication**:
     - **Header Name**: `Authorization`
     - **Header Value**: `Basic <YOUR_BASE64_ENCODED_CREDENTIALS>`
6. Click **Add Extension** / **Save**.
7. Open a new chat session in Claude Desktop, click the **🔌 Tools / MCP** icon, and confirm that `BTP Administration` is connected and active.

> [!NOTE]
> No local JSON config files or terminal commands are needed for Claude Desktop.

---

## 🚀 Setup Guide for Antigravity IDE

Google Antigravity IDE connects using native Streamable HTTP configured in `mcp_config.json`.

### Step-by-Step Instructions:

1. Open **Antigravity IDE**.
2. In the sidebar panel, locate **Installed MCP Servers**.
3. Click **`Open MCP Config`** (top-right icon of the MCP section).
4. Add the `BTP Administration` entry to `mcpServers`:

```json
{
  "mcpServers": {
    "BTP Administration": {
      "url": "https://<YOUR_BTP_MCP_PROXY_HOST>/mcp",
      "headers": {
        "Authorization": "Basic <YOUR_BASE64_ENCODED_CREDENTIALS>"
      }
    }
  }
}
```

5. Save the `mcp_config.json` file.
6. Click **Refresh 🔄** in the Installed MCP Servers panel.
7. Verify the status indicator turns **green (`●`)**.

---

## 🛠️ Troubleshooting & Technical Notes

| Symptom / Error | Cause | Resolution |
| :--- | :--- | :--- |
| `401 Unauthorized` | Invalid or missing `Authorization` header | Ensure header format is `Basic <Base64String>` |
| `OAuth Redirect Failure` | OAuth SSO flow blocked on Web Connectors | Use Direct Desktop connection instead of Web Connectors |
| Extension connection stays offline | Incorrect server proxy URL | Double-check HTTPS proxy URL `/mcp` endpoint path |

---

## 🔐 Security Best Practices

> [!CAUTION]
> - Never store or commit actual Base64 authorization tokens in public source code or shared files.
> - Always replace `<YOUR_BASE64_ENCODED_CREDENTIALS>` and `<YOUR_BTP_MCP_PROXY_HOST>` with your actual deployment values locally.
> - Ensure all connections use HTTPS for encrypted transmission.
