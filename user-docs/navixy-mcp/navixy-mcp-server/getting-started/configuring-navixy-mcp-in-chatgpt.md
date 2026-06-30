# Configuring Navixy MCP in ChatGPT

{% hint style="warning" %}
ChatGPT Developer Mode with MCP support is currently in beta. The interface and available options may change.
{% endhint %}

This page covers how to connect Navixy MCP Server to ChatGPT using Developer Mode.

**Prerequisites:**

* A Navixy user account, Admin Panel account, or both
* A ChatGPT Plus, Pro, Business, Enterprise, or Education account.&#x20;

{% hint style="warning" %}
Developer Mode and custom MCP apps aren't available on the ChatGPT Free plan. Business and Enterprise plans require admin rights to enable Developer Mode and custom MCPs.
{% endhint %}

**See also:**

* [Developer Mode in ChatGPT](https://platform.openai.com/docs/guides/developer-mode): Official OpenAI documentation

## How to configure Navixy MCP in ChatGPT

{% hint style="warning" %}
AI client features change frequently. If these steps don't match your version of ChatGPT, consult the [official documentation](https://platform.openai.com/docs/guides/developer-mode).
{% endhint %}

Navixy MCP Server connects to ChatGPT through Developer Mode, which lets you add custom MCP apps directly in the ChatGPT interface. No configuration file or Node.js installation is required.

1. Click your username in the bottom-left corner.
2. Open ⚙️ **Settings**.
3. Select **Apps**, then **Advanced settings**.
4. Enable **Developer mode**.
5. Click **Create app**.
6. Enter an app name, for example, `Navixy User MCP` or `Navixy Panel MCP`.
7. Enter the MCP Server URL:
   * User API: `https://platform.mcp.navixy.com/user/mcp`
   * Admin Panel API: `https://platform.mcp.navixy.com/panel/mcp`
8. Select **OAuth** as the authentication method.
9. Leave the remaining settings at their defaults.
10. Check **I understand and want to continue**.
11. Click **Create**.

A Navixy login page opens in your browser automatically. Log in with your Navixy credentials to complete the connection.

To add another connection, repeat the steps above with another URL.

## How to use Navixy MCP in a conversation

After setup, Navixy MCP apps aren't enabled by default in each conversation. To use them:

1. Open a new chat.
2. Click **+** at the bottom left of the chat interface.
3. Select the Navixy app you want to use.

## How to validate the connection

Run this prompt to confirm the connection is working:

```
Which of my trackers are online right now?
```

If the response includes your actual tracker data, the connection is working correctly.

## Business and Enterprise plans

On Business and Enterprise plans, an admin or owner must enable Developer Mode and publish the MCP app to the workspace before members can use it. Individual members cannot add custom MCP apps themselves. Contact your workspace administrator to request access.
