---
description: >-
  Connect Navixy MCP Server to ChatGPT as a plugin or to Codex as a local MCP
  server, then check that the connection returns current data.
---

# How to configure Navixy MCP in ChatGPT and Codex

This page covers all available methods for connecting Navixy MCP Server to ChatGPT and Codex. If you haven't connected before and want a quick walkthrough, visit [Getting started](./) instead.

**Prerequisites:**

- A Navixy user account, Navixy Admin Panel account, or both
- A ChatGPT Plus, Pro, Business, Enterprise, or Education account for the ChatGPT plugin setup
- The ChatGPT desktop app installed on your computer for the Codex setup
- The same ChatGPT account in ChatGPT web and the ChatGPT desktop app if you want to use the plugin in both

**See also:**

- [ChatGPT developer mode](https://developers.openai.com/api/docs/guides/developer-mode): Official OpenAI documentation on plugins and custom MCP apps
- [Model Context Protocol in Codex](https://learn.chatgpt.com/docs/extend/mcp): Official OpenAI documentation on MCP servers in Codex

{% hint style="warning" %}
AI client features change frequently. If these steps don't match your version of ChatGPT, consult the official OpenAI documentation linked above.
{% endhint %}

## MCP connection methods

In ChatGPT, a plugin is a type of integration, and an MCP server is a type of plugin. In Codex, an MCP server is stored on your computer instead of in your ChatGPT account.

The two lists are separate. A plugin that you add in ChatGPT doesn't appear in Codex, and an MCP server that you add in Codex doesn't appear in ChatGPT conversations. To use Navixy in both, set up each connection separately.

| Where you work                                   | Setup method                                                                                                            |
| ------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------- |
| ChatGPT web or ChatGPT desktop app conversations | Add a plugin in ChatGPT web. The plugin also appears in the desktop app when you sign in with the same ChatGPT account. |
| Codex in the ChatGPT desktop app, or Codex CLI   | Add an MCP server in Codex settings, or ask Codex to add it in a conversation.                                          |

If you aren't sure which method to use, start with the ChatGPT web setup.

{% hint style="warning" %}
Developer mode and custom MCP apps aren't available on the ChatGPT Free plan. A workspace administrator may need to enable these features for managed accounts in Enterprise-style subscriptions.
{% endhint %}

## How to connect Navixy MCP as a ChatGPT web plugin

Adding a plugin in ChatGPT web is the easiest setup method. After setup, the same plugin is available in the ChatGPT desktop app.

{% stepper %}
{% step %}

### Turn on developer mode

1. Open **Settings** in ChatGPT web.
2. Select **Security and login**.
3. Turn on **Developer mode**.

{% endstep %}

{% step %}

### Add the Navixy plugin

1. Open **Plugins** from the sidebar on the ChatGPT home page or from **Settings → Plugins**.
2. Select **Browse plugins**.
3. Click **+** in the top-right corner.
4. Enter the name and URL for the connection that you need:

| Connection                 | Name               | URL                                         |
| -------------------------- | ------------------ | ------------------------------------------- |
| Navixy user account        | `Navixy User MCP`  | `https://platform.mcp.navixy.com/user/mcp`  |
| Navixy Admin Panel account | `Navixy Panel MCP` | `https://platform.mcp.navixy.com/panel/mcp` |

To use both connections, add each one separately.
{% endstep %}

{% step %}

### Sign in to Navixy

1. Select **OAuth** as the authentication method.
2. Leave the remaining settings at their default values.
3. Click **I understand and want to continue**.
4. Click **Create**.
5. Click **Sign in with Navixy User MCP** or **Sign in with Navixy Panel MCP**.
6. Enter your Navixy credentials.

{% endstep %}

{% step %}

### Use the plugin

Start a new ChatGPT conversation and select the Navixy plugin from the **Developer mode** tool in the composer.

In the ChatGPT desktop app, sign in with the same ChatGPT account. If the plugin doesn't appear, restart the desktop app.
{% endstep %}
{% endstepper %}

## How to connect Navixy MCP as an MCP server in Codex

Codex uses an MCP server that is stored on your computer. This connection is separate from the plugin that ChatGPT web and the ChatGPT desktop app share.

Both methods below produce the same result. Asking Codex in a conversation takes fewer steps, and the settings method gives you the full form.

### How to add an MCP server via conversation

1. Switch to Codex mode in the ChatGPT desktop app.
2. Open a project. Codex can't change MCP settings without an open project.
3. Send a prompt with the server name and address:

```
Add MCP server navixy-user https://platform.mcp.navixy.com/user/mcp
```

4. Follow the steps that Codex suggests and enter your Navixy credentials when the browser opens.

For the Navixy Admin Panel connection, repeat the prompt with `navixy-panel` and `https://platform.mcp.navixy.com/panel/mcp`.

### How to add an MCP server via Codex settings

1. Open **Settings → MCP servers** in the ChatGPT desktop app. In some versions, open **Settings → Plugins → MCPs**.
2. Click **Add server**.
3. Enter `navixy-user` for a Navixy user account or `navixy-panel` for a Navixy Admin Panel account.
4. Select **Streamable HTTP**.
5. Enter the matching URL:
   - Navixy user account: `https://platform.mcp.navixy.com/user/mcp`
   - Navixy Admin Panel account: `https://platform.mcp.navixy.com/panel/mcp`
6. Click **Save**.
7. Restart the ChatGPT desktop app when prompted.
8. Find the server in the MCP server list and click **Authenticate**, then enter your Navixy credentials.

The **Authenticate** button appears in the MCP server list after you add the server, not in the form that you use to add it. If the button doesn't appear right away, restart the desktop app and open the list again.

If **Authenticate** still doesn't appear, or if you receive a 401 error, follow the [401 authentication troubleshooting steps](../../troubleshooting.md#navixy-mcp-returns-a-401-error-in-codex).

If a Codex conversation doesn't start at all, the cause is usually the model rather than the MCP connection. See [Codex shows an unsupported model error](../../troubleshooting.md#codex-shows-an-unsupported-model-error).

## Check the connection

For ChatGPT web or the ChatGPT desktop app, select the Navixy plugin from the **Developer mode** tool in the composer. For Codex, type `/mcp` in the composer or in Codex CLI and confirm that `navixy-user` or `navixy-panel` is connected.

Then send the following prompt:

```
Which of my GPS devices are online right now?
```

The connection works if the response includes current data from your Navixy account.

{% hint style="info" %}
Don't type `@navixy-user` in a ChatGPT conversation. Select the Navixy plugin from the **Developer mode** tool instead.
{% endhint %}
