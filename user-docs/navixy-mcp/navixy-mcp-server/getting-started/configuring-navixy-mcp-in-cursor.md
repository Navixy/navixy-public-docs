# Configuring Navixy MCP in Cursor

This page covers how to connect Navixy MCP Server to Cursor. If you haven't connected before and want a quick walkthrough, visit [Getting started](./) instead.

**Prerequisites:**

* A Navixy user account, Admin Panel account, or both
* [Cursor](https://www.cursor.com) installed on your computer

**See also:**

* [MCP in Cursor](https://docs.cursor.com/context/model-context-protocol): Official Cursor documentation on MCP

## How to configure Navixy MCP in Cursor

{% hint style="warning" %}
AI client features change frequently. If these steps don't match your version of Cursor, consult the [official documentation](https://docs.cursor.com/context/model-context-protocol).
{% endhint %}

There are two principal methods of connecting Navixy MCP to Cursor: simply ask the agent to connect an MCP server and provide the link or directly edit `mcp.json`.

Navixy MCP Server connects to Cursor by adding an entry to Cursor's MCP configuration file. Node.js is not required.

1. Open Cursor and go to ⚙️ **Cursor Settings** (top right corner) **→ Tools & MCP**.
2. Click **New MCP Server** at the bottom of the Installed MCP Servers list. Cursor opens `mcp.json` in the editor.
3. If you don't have other MCP server entries, paste the following to connect to `navixy-user` and/or `navixy-panel`:

```json
{
  "mcpServers": {
    "navixy-user": {
      "url": "https://platform.mcp.navixy.com/user/mcp"
    },
    "navixy-panel": {
      "url": "https://platform.mcp.navixy.com/panel/mcp"
    }
  }
}
```

To connect to only `navixy-user` or `navixy-panel`, remove the block you don't need.

If the file already contains other MCP server entries, add the `navixy-user` and/or `navixy-panel` blocks inside the existing `mcpServers` object:

```json
    "navixy-user": {
      "url": "https://platform.mcp.navixy.com/user/mcp"
    },
    "navixy-panel": {
      "url": "https://platform.mcp.navixy.com/panel/mcp"
    }
```

4. Save the file.
5. Restart Cursor.

## How to authorize your Navixy account in Cursor

Cursor opens a Navixy login page in your browser automatically after restarting. Log in with your Navixy credentials.

You will not be prompted to log in again on subsequent restarts unless your session expires.

## How to validate the connection

Run this prompt to confirm the connection is working:

```
Which of my trackers are online right now in Navixy?
```

If the response includes your actual tracker data, the connection is working correctly.
