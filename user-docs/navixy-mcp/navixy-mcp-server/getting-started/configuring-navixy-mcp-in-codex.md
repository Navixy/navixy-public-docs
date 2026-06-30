# Configuring Navixy MCP in Codex

{% hint style="warning" %}
Codex is primarily designed for software development and coding tasks. It connects to Navixy MCP Server the same way other AI clients do, but interactive map and chart visualizations are not supported.
{% endhint %}

This page covers how to connect Navixy MCP Server to the Codex desktop app.

**Prerequisites:**

* A Navixy user account, Admin Panel account, or both
* [Codex](https://codex.openai.com/) installed on your computer
* A ChatGPT Plus, Pro, Business, Enterprise, or Education account

**See also:**

* [Configuring Navixy MCP in ChatGPT](configuring-navixy-mcp-in-chatgpt.md)
* [MCP in Codex](https://developers.openai.com/codex): Official Codex documentation

## How to configure Navixy MCP in Codex

{% hint style="warning" %}
AI client features change frequently. If these steps don't match your version of Codex, consult the [official documentation](https://developers.openai.com/codex).
{% endhint %}

The recommended method is to set up the connection via conversation. The Codex MCP settings UI is currently unreliable and may not work correctly.

### Via conversation

Open Codex and type a prompt like:

```
Connect an MCP server https://platform.mcp.navixy.com/user/mcp
```

Codex will guide you through the remaining steps. To add the Admin Panel connection, repeat with `https://platform.mcp.navixy.com/panel/mcp`.

### Via ChatGPT Apps

If you have already connected Navixy MCP Server in ChatGPT using the same account, Codex picks it up automatically without any additional setup or authentication. The connection is carried over from your account's Apps backend. It won't appear in **Settings → MCP Servers** alongside manually added servers, but it's active and usable in conversations.

See [Configuring Navixy MCP in ChatGPT](configuring-navixy-mcp-in-chatgpt.md) for setup instructions.

### Via the Codex interface

{% hint style="danger" %}
The Codex MCP settings UI is currently not working correctly. Use the conversation method above instead.
{% endhint %}

If you still want to try the UI method:

1. Open Codex and go to **Settings → MCP Servers**.
2. Click **+ Add server**.
3. Enter a name, for example, `Navixy User MCP` or `Navixy Panel MCP`.
4. Select **Streamable HTTP** as the server type.
5. Enter the MCP Server URL:
   * User API: `https://platform.mcp.navixy.com/user/mcp`
   * Admin Panel API: `https://platform.mcp.navixy.com/panel/mcp`
6. Click **Save**.
7. Click **Authenticate** next to the newly created server's name and log in with your Navixy credentials.
8. Fully restart Codex.

## How to validate the connection

Run this prompt to confirm the connection is working:

```
Which of my trackers are online right now?
```

If the response includes your actual tracker data, the connection is working correctly.

## Limitations

Interactive map and chart visualizations (`show_tracker_map`, `show_iot_query_dashboard`) aren't supported in Codex. However, the underlying data is still accessible. Codex returns it as text instead of rendering a visual widget and might choose to try and display it using other methods.
