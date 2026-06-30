# Getting started

This tutorial walks you through connecting Navixy MCP Server's User API to Claude Desktop and asking your first question. The whole process takes about five minutes.

**Prerequisites:**

* A Navixy user account
* [Claude Desktop](https://claude.ai/download) installed on your computer.
* [Node.js](https://nodejs.org) installed on your computer. If you prefer not to install Node.js, see [Navixy MCP in Claude Desktop](configuring-navixy-mcp-in-claude-desktop.md#configuring-mcp-via-custom-connector) for an alternative, UI-based setup method that's only available to certain subscription levels.

**See also:**

* [Navixy MCP in Claude Desktop](configuring-navixy-mcp-in-claude-desktop.md): Details on configuring Navixy MCP Server in Claude Desktop
* [Navixy MCP in Cursor](configuring-navixy-mcp-in-cursor.md): Details on configuring Navixy MCP Server in Cursor
* [Navixy MCP in ChatGPT](configuring-navixy-mcp-in-chatgpt.md): Details on configuring Navixy MCP Server in ChatGPT
* [Navixy MCP in Codex](configuring-navixy-mcp-in-codex.md): Details on configuring Navixy MCP Server in Codex
* [Connecting other AI tools](connecting-other-ai-tools.md): Information on connecting other AI clients

## Quick start for installing Navixy User MCP

{% hint style="warning" %}
AI client features change frequently. If these steps don't match your version of Claude Desktop, consult the [official documentation](https://code.claude.com/docs/en/mcp).
{% endhint %}

Follow these instructions to start using Navixy MCP Server in Claude Desktop:

{% stepper %}
{% step %}
### Open the Claude Desktop configuration file

1. Open Claude Desktop.
2. Go to **Settings** and select **Developer**.
3. Select **Edit Config**.

Claude Desktop opens `claude_desktop_config.json` in your default text editor.
{% endstep %}

{% step %}
### Add Navixy MCP Server

If the file is empty, paste the following text into the file:

```json
{
  "mcpServers": {
    "navixy-user": {
      "command": "npx",
      "args": ["mcp-remote", "https://platform.mcp.navixy.com/user/mcp"]
    }
  }
}
```

If the file already contains preferences, add an `mcpServers` block inside the existing object:

```json
{
  "mcpServers": {
    "navixy-user": {
      "command": "npx",
      "args": [
        "mcp-remote",
        "https://platform.mcp.navixy.com/user/mcp"
      ]
    }
  },
  ... //preferences and other data
```

Save the file.
{% endstep %}

{% step %}
### Restart Claude Desktop

Quit Claude Desktop completely (**≡** → **Files** → **Exit**) and open it again. Simply closing the window isn't enough, as the configuration is read on startup.
{% endstep %}

{% step %}
### Connect your Navixy account

A Navixy login page automatically opens in your browser. Log in with your Navixy account credentials.
{% endstep %}

{% step %}
### Ask your first question

Try this prompt to confirm the connection is working:

```
Which of my trackers are online right now in Navixy?
```

Claude retrieves the current status of your trackers directly from your Navixy account and returns the result. If you see your actual tracker data, the connection is working correctly.
{% endstep %}
{% endstepper %}

## **Next steps**

* [Navixy User MCP](../navixy-user-mcp.md): Complete list of operations available via the User connection
