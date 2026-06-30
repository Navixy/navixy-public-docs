# Configuring Navixy MCP in Claude Desktop

This page covers all available methods for connecting Navixy MCP Server to Claude Desktop. If you haven't connected before and want a quick walkthrough, visit [Getting started](./) instead.

**Prerequisites:**

* A Navixy user account, Admin Panel account, or both
* [Claude Desktop](https://claude.ai/download) installed on your computer
* [Node.js](https://nodejs.org/) installed on your computer, if using the configuration file method

**See also:**

* [Connect Claude Code to tools via MCP](https://code.claude.com/docs/en/mcp): Official developer documentation on connecting Claude Code via MCP

## MCP connection methods

{% hint style="warning" %}
AI client features change frequently. If these instructions don't match your version of Claude Desktop, consult the [official documentation](https://code.claude.com/docs/en/mcp).
{% endhint %}

Two methods are available for adding Navixy MCP Server to Claude Desktop:

<table><thead><tr><th width="169.79998779296875">Method</th><th width="218.19989013671875">How it works</th><th>Best for</th></tr></thead><tbody><tr><td>Configuration file</td><td>Edit <code>claude_desktop_config.json</code> directly</td><td>Developers, connecting multiple servers</td></tr><tr><td>Custom connector</td><td>Add a URL through Claude settings</td><td>Paid subscription users, non-enterprise users</td></tr></tbody></table>

On Team and Enterprise plans, an Owner must add the connector to the organization before members can connect.

## Configuring MCP via configuration file

This method requires Node.js and works for all Claude subscription plans independently of organization settings.

1. Open Claude Desktop.
2. Go to **Settings** and select **Developer**.
3. Select **Edit Config** to open `claude_desktop_config.json` in your default text editor.
4. If you don't have other MCP server entries, paste the following text to connect to both `navixy-user` and `navixy-admin`:

```json
{
  "mcpServers": {
    "navixy-user": {
      "command": "npx",
      "args": ["mcp-remote", "https://platform.mcp.navixy.com/user/mcp"]
    },
    "navixy-panel": {
      "command": "npx",
      "args": ["mcp-remote", "https://platform.mcp.navixy.com/panel/mcp"]
    }
  }
```

To connect to only `navixy-user` or `navixy-admin`, remove the block you don't need.

If your JSON file already contains other MCP server entries, add the `navixy-user` and/or `navixy-admin` blocks inside the existing `mcpServers` object:

```json
{
    "navixy-user": {
      "command": "npx",
      "args": ["mcp-remote", "https://platform.mcp.navixy.com/user/mcp"]
    },
    "navixy-panel": {
      "command": "npx",
      "args": ["mcp-remote", "https://platform.mcp.navixy.com/panel/mcp"]
  }
```

5. Save the file.
6. Quit Claude Desktop completely (**≡** → **Files** → **Exit**) and open it again. A window reload is not enough.

## Configuring MCP via custom connector

{% hint style="warning" %}
Claude's free plan allows only one custom connector at a time. [Configuring MCP via the configuration file](configuring-navixy-mcp-in-claude-desktop.md#configuring-mcp-via-configuration-file) bypasses this restriction.
{% endhint %}

{% tabs %}
{% tab title="For Free, Pro, and Max plans" %}
1. Go to **Customize > Connectors** (or click **Connectors** on the left sidebar if it's available).
2. Click **+**, then **Add custom connector**.
3. Enter the Navixy MCP Server URL: `https://platform.mcp.navixy.com/user/mcp`
4. Click **Add**.

After setup, enable the connector for a conversation by clicking **+** at the bottom left of the chat interface, selecting **Connectors**, and toggling it on.
{% endtab %}

{% tab title="For Team and Enterprise plans" %}
An Owner must first add the connector to the organization:

1. Go to **Organization settings > Connectors**.
2. Click **Add**, hover over **Custom**, then select **Web**.
3. Enter the Navixy MCP Server URL: `https://platform.mcp.navixy.com/user/mcp`
4. Click **Add**.

After the Owner adds the connector, each member connects individually:

1. Go to [Customize > Connectors](https://claude.ai/customize/connectors).
2. Find the Navixy connector in the list and click **Connect**.

After setup, enable the connector for a conversation by clicking **+** at the bottom left of the chat interface, selecting **Connectors**, and toggling it on.
{% endtab %}
{% endtabs %}

## How to authorize your Navixy account in Claude

After restarting, Claude Desktop opens a Navixy login page in your browser automatically. Log in with your Navixy credentials and confirm the authorization request.

You won't be prompted to log in again on subsequent restarts unless your session expires.

## How to validate the connection

Run this prompt to confirm the connection is working:

```
Which of my trackers are online right now in Navixy?
```

If the response includes your actual tracker data, the connection is working correctly.
