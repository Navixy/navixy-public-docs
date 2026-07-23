# Configuring Navixy MCP in ChatGPT

This page covers how to connect Navixy MCP Server to the browser and desktop versions of ChatGPT, which share a common list of MCP servers (called plugins) per account.

**Prerequisites:**

* A Navixy user account, Admin Panel account, or both
* A ChatGPT Plus, Pro, Business, Enterprise, or Education account.

{% hint style="warning" %}
Developer Mode and custom MCP apps aren't available on the ChatGPT Free plan. Business and Enterprise plans require admin rights to enable Developer Mode and custom MCPs.
{% endhint %}

**See also:**

* [Developer Mode in ChatGPT](https://platform.openai.com/docs/guides/developer-mode): Official OpenAI documentation

## How to configure Navixy MCP in ChatGPT (browser version)

{% hint style="warning" %}
AI client features change frequently. If these steps don't match your version of ChatGPT, consult the [official documentation](https://platform.openai.com/docs/guides/developer-mode).
{% endhint %}

Navixy MCP Server connects to the web version of ChatGPT through Developer Mode, which lets you add custom MCP apps directly in the ChatGPT interface. No configuration file or Node.js installation is required.

1. Click your username in the bottom-left corner.
2. Open ⚙️ **Settings**.
3. Select **Plugins**, then **Advanced settings** or select **Security and login** and scroll down to the **Developer mode** section.
4. Enable **Developer mode**.
5. Navigate to **Plugins → Browse plugins** and click **+** in the top-right corner of the screen.
6. Enter an app name, for example, `Navixy User MCP` or `Navixy Panel MCP`.
7. Enter the MCP Server URL:
   * User API: `https://platform.mcp.navixy.com/user/mcp`
   * Admin Panel API: `https://platform.mcp.navixy.com/panel/mcp`
8. Select **OAuth** as the authentication method.
9. Leave the remaining settings at their defaults.
10. Check **I understand and want to continue**.
11. Click **Create**.
12. Click **Sign in with Navixy User MCP** (or **Sign in with Navixy Panel MCP**) and enter your Navixy credentials to complete the connection.

To add another plugin, repeat the steps above with another URL.

## How to configure Navixy MCP in ChatGPT (desktop version)

The recommended method is to [set up the connection via the browser version](configuring-navixy-mcp-in-chatgpt.md#how-to-configure-navixy-mcp-in-chatgpt-browser-version). The ChatGPT MCP settings UI is currently unreliable and may not work correctly.

## How to validate the connection

In the browser version, run this prompt to confirm the connection is working:

```
Which of my trackers are online right now?
```

In the desktop version, you need to call the specific MCP using **@** and its name, for example:

<pre><code><strong>@navixy-user, which of my trackers are online right now?
</strong></code></pre>

If the response includes your actual tracker data, the connection is working correctly.
