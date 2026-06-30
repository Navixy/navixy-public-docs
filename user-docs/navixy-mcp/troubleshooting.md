# Troubleshooting

This page covers known issues and solutions for Navixy MCP Server across all supported AI clients.

## How do I log out or switch accounts?

* **Claude Desktop:** Delete the `.mcp-auth\mcp-remote-xxxx` folder in your user directory. This logs out all MCP servers simultaneously.
* **Cursor:** Go to ⚙️ **Cursor Settings → Tools** and click **Logout** next to the Navixy server.
* **ChatGPT:** Go to ⚙️ **Settings → Apps**, click on your MCP server, click **…**, and select **Reconnect**.

## "MCP {name}: Server disconnected" in Claude Desktop

This error appears in Claude Desktop when the MCP server connection is lost.

To resolve this:

1. Restart Claude Desktop completely via **≡ → Files → Exit**. A window reload is not enough.
2. If restarting doesn't help, delete the `.mcp-auth\mcp-remote-xxxx` folder in your user directory and reconnect.
3. If that doesn't help either, reboot your operating system.
4. If that still fails, it might be a Node.js issue. If you're using the `npx` command in [`claude_desktop_config.json`](navixy-mcp-server/getting-started/configuring-navixy-mcp-in-claude-desktop.md#configuring-mcp-via-configuration-file), try switching to `pnmp`.

## Claude Desktop can't find npx on macOS

If you encounter issues with Node.js on macOS, install it system-wide via Homebrew:

{% code overflow="wrap" %}
```bash
brew install node
```
{% endcode %}

## "I don't see any connected Navixy resources or tools available" in ChatGPT

This error appears in ChatGPT even when the MCP server has not been disconnected. The most common cause is Developer Mode being turned off.

To resolve this:

1. Click your username in the bottom-left corner.
2. Open ⚙️ **Settings → Apps → Advanced settings**.
3. Confirm that **Developer mode** is enabled. If it is off, turn it on.
4. Start a new conversation and enable the Navixy connector again.

## Codex shows MCP server as connected but only some tools are available

This is a known issue in Codex as of May 2026. In some sessions, Codex reports the MCP server as connected but only exposes a subset of the available tools. There is no workaround at this time. Starting a new conversation may restore access to the full tool set.

## Codex MCP server UI does not work correctly

Adding an MCP server via the Codex settings UI (**Settings → MCP Servers → + Add server**) is not working correctly as of May 2026. Use the conversation-based setup method instead:

```
Connect an MCP server https://platform.mcp.navixy.com/user/mcp
```

See [Configuring Navixy MCP in Codex](https://claude.ai/mcp-test/navixy-mcp-server/configuring-navixy-mcp-in-codex.md) for details.

## The assistant answers but does not cite sources

Ask for links explicitly. You can also ask it to quote a short phrase from the section it referenced to confirm it retrieved the right content.

## Pasted content looks broken

Check for wrapped lines. This is most common with `curl` commands and long API paths copied from the docs.
