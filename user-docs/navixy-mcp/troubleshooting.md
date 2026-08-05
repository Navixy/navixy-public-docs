---
description: >-
  Fix common Navixy MCP Server problems across AI clients, including
  disconnected servers, missing tools, 401 errors, and account switching.
---

# Troubleshooting

This page covers known issues and solutions for Navixy MCP Server across all supported AI clients.

## How do I log out or switch accounts?

* **Claude Desktop:** Delete the `.mcp-auth\mcp-remote-xxxx` folder in your user directory. This logs out all MCP servers simultaneously.
* **Cursor:** Go to ⚙️ **Cursor Settings → Tools** and click **Logout** next to the Navixy server.
* **ChatGPT web:** Go to **Settings → Plugins**, select the Navixy plugin, click **…**, and select **Reconnect**.
* **Codex:** Run `codex mcp logout navixy-user`, then run `codex mcp login navixy-user`.

## "MCP {name}: Server disconnected" in Claude Desktop

This error appears in Claude Desktop when the MCP server connection is lost.

To resolve this:

1. Restart Claude Desktop completely via **≡ → Files → Exit**. A window reload is not enough.
2. If restarting doesn't help, delete the `.mcp-auth\mcp-remote-xxxx` folder in your user directory and reconnect.
3. If that doesn't help either, reboot your operating system.
4. If that still fails, it might be a Node.js/npm issue. Try installing [pnpm](https://pnpm.io/) and replacing `npx` in [`claude_desktop_config.json`](navixy-mcp-server/getting-started/configuring-navixy-mcp-in-claude-desktop.md#configuring-mcp-via-configuration-file) with  `pnmp`.

## Claude Desktop can't find npx on macOS

If you encounter issues with Node.js on macOS, install it system-wide via Homebrew:

{% code overflow="wrap" %}
```bash
brew install node
```
{% endcode %}

## "I don't see any connected Navixy resources or tools available" in ChatGPT

This error appears in ChatGPT even when the MCP server has not been disconnected. The most common cause is that **Developer mode** is turned off.

To resolve this:

1. Open **Settings → Security and login**.
2. Confirm that **Developer mode** is enabled. If it is off, turn it on.
3. Start a new conversation.
4. Select the Navixy plugin from the **Developer mode** tool in the composer.

## Navixy MCP returns a 401 error in Codex

A 401 error means that the MCP server on your computer doesn't have valid sign-in information. Try to authenticate in the ChatGPT desktop app first.

1. Open **Settings → MCP servers**. In some versions, open **Settings → Plugins → MCPs**.
2. Find `navixy-user` or `navixy-panel` in the MCP server list.
3. Click **Authenticate** and enter your Navixy credentials.
4. Restart the ChatGPT desktop app.
5. Open a Codex conversation and enter `/mcp`.

The server is ready when `/mcp` shows it as connected.

### Use the command-line fallback

Use this fallback only if **Authenticate** doesn't appear or the 401 error continues.

1. Open **Terminal** on macOS or **PowerShell** on Windows.
2. Run `codex --version` to check whether Codex CLI is available.
3. Run the login command for your connection.

For Navixy User MCP, run the following command:

```bash
codex mcp login navixy-user
```

For Navixy Panel MCP, run the following command:

```bash
codex mcp login navixy-panel
```

Enter your Navixy credentials in the browser, then restart the ChatGPT desktop app. Enter `/mcp` in a Codex conversation to confirm the connection.

If the terminal reports that the server doesn't exist, add and authenticate it with the matching commands:

{% tabs %}
{% tab title="Navixy user account" %}
```bash
codex mcp add navixy-user --url https://platform.mcp.navixy.com/user/mcp
codex mcp login navixy-user
```
{% endtab %}

{% tab title="Navixy Admin Panel" %}
```bash
codex mcp add navixy-panel --url https://platform.mcp.navixy.com/panel/mcp
codex mcp login navixy-panel
```
{% endtab %}
{% endtabs %}

### If the codex command isn't available

On macOS, the ChatGPT desktop app includes Codex CLI inside the app. Use the following path in place of `codex`:

```bash
/Applications/ChatGPT.app/Contents/Resources/codex
```

Some upgraded installations use `/Applications/Codex.app/Contents/Resources/codex` instead.

On macOS or Windows, you can also install [Node.js](https://nodejs.org/) and then install Codex CLI:

```bash
npm install -g @openai/codex
```

See [Configuring Navixy MCP in ChatGPT and Codex](navixy-mcp-server/getting-started/configuring-navixy-mcp-in-chatgpt.md#how-to-connect-navixy-mcp-as-an-mcp-server-in-codex) for the complete setup.

## Codex shows an unsupported model error

The ChatGPT desktop app and Codex CLI share the model setting in `~/.codex/config.toml`. An unavailable model can prevent a Codex conversation from starting.

Use the model control below the desktop composer to select an available model. In Codex CLI, enter `/model` and select an available model.

If the error continues, open `~/.codex/config.toml`. Remove or update the `model` value. For example, `gpt-5.2-codex` may not be available with ChatGPT authentication.

## The assistant answers but does not cite sources

Ask for links explicitly. You can also ask it to quote a short phrase from the section it referenced to confirm it retrieved the right content.

## Pasted content looks broken

Check for wrapped lines. This is most common with `curl` commands and long API paths copied from the docs.
