---
hidden: true
noIndex: true
description: >-
  Connect Claude, Cursor, VS Code, Codex, or another AI client to your Navixy
  workspace and ask it your first question about your fleet.
tags:
  - guide
  - beta
---
# Connect Navixy MCP Server

Read this guide to connect your AI client to your Navixy workspace in a few minutes and check the connection by asking a first question. The client will answer questions from live data.

Your AI client needs two things: an API key and your server address. With these two, most clients can set up the connection themselves when you ask them.

{% hint style="warning" %}
TODO before merge: confirm the production address of Navixy MCP Server. This page uses `https://nvx4.<REGION>.navixy.com/workspaces/<WORKSPACE_ID>/mcp` for the `eu` and `us` regions: ADR-045 puts the address at the root of the region's API host, without the `/api` prefix of the GraphQL API, and the test stand serves it that way. Production doesn't route `/workspaces/` yet in either region.
{% endhint %}

Before you start, make sure that you have the following:

* An AI client that can send a request header to a remote MCP server. See [Supported AI clients](README.md#supported-ai-clients).
* Access to [Console](https://app.gitbook.com/s/9BfYVQ80Aby5RbZ9vQVj/), Navixy's administration tool, where you create an API key. Anyone with access to Console can create it.

## Steps

To connect your AI client to Navixy, follow these steps:

{% stepper %}
{% step %}

### Create an API key

The API key is how your AI client signs in to Navixy. The client can see and change only what the key's role (access rights) allows.

In Console, select your workspace in the top navigation bar. Then go to **API keys** and create a key with the role that you want the AI client to have. Name the key after the client, such as Claude Code, or any way you like. The audit log will show the changes made by the AI client under this name. Copy the key and keep it in a safe place: Console shows it only once. You can't view it again.

See [Create an API key](https://app.gitbook.com/s/9BfYVQ80Aby5RbZ9vQVj/api-keys/manage-api-keys#create-an-api-key) in the Console documentation to learn more about this process.
{% endstep %}

{% step %}

### Find your server address

Each workspace has its own server address. The address contains your region and the workspace ID:

```
https://nvx4.<REGION>.navixy.com/workspaces/<WORKSPACE_ID>/mcp
```

Replace `<REGION>` with `eu` if your Navixy account is in Europe or `us` if it's in the United States. The Console address shows your region, such as `console.us.navixy.com`.

To find the workspace ID, open **Organization** in Console and go to the **Workspaces** tab. Click the workspace's name. The side panel shows the ID, which looks like this: `01a0ef12-3c4d-8000-801f-4f52474e1a2b`.

An AI agent that already has your API key can also get the ID from the key exchange with [Navixy GraphQL API](https://app.gitbook.com/s/eaPUYn5ATRhY1fALtOkq/). To learn the exact steps for connecting to the API, see [How to choose the workspace](https://app.gitbook.com/s/eaPUYn5ATRhY1fALtOkq/authentication#how-to-choose-the-workspace) in its documentation.
{% endstep %}

{% step %}

### Ask your AI client to connect

The fastest way to connect is to ask the AI client itself. AI clients change their settings often, and the client knows its own current setup better than any guide. Send this prompt and don't forget to replace the placeholders with your region and workspace ID:

```
Add a remote MCP server named "navixy" at this address:
https://nvx4.<REGION>.navixy.com/workspaces/<WORKSPACE_ID>/mcp

The server needs my API key after "Authorization: Bearer" with every request. Don't ask me for the key in this chat. Set up everything else,
then tell me where to put the key.
```

The AI client will add the server or tell you which setting or file to change. Enter the key as instructed, then restart the client.

{% hint style="warning" %}
Don't paste the API key into the conversation. The AI client sends everything you write to its language model, so the client's vendor may be able to access the key.
{% endhint %}

If you prefer to set up the AI client manually, use these examples for your client. Replace the address with your server address and `<YOUR_API_KEY>` with your key.

{% tabs %}
{% tab title="Claude Code" %}
Replace `<REGION>` with your region, `<WORKSPACE_ID>` with your workspace ID, and `<YOUR_API_KEY>` with your key. Then run the command in a terminal, in one line:

```bash
claude mcp add --transport http --scope user navixy https://nvx4.<REGION>.navixy.com/workspaces/<WORKSPACE_ID>/mcp --header "Authorization: Bearer <YOUR_API_KEY>"
```

`--scope user` adds the server for all your projects, including Claude Code sessions in the Claude desktop app. Without it, Claude Code adds the server only for the folder where you run the command. Start a new session after you add the server.
{% endtab %}

{% tab title="Cursor" %}
Add the server to the `mcp.json` file, which opens from the MCP section of **Cursor Settings**:

```json
{
  "mcpServers": {
    "navixy": {
      "url": "https://nvx4.<REGION>.navixy.com/workspaces/<WORKSPACE_ID>/mcp",
      "headers": {
        "Authorization": "Bearer <YOUR_API_KEY>"
      }
    }
  }
}
```

{% endtab %}

{% tab title="VS Code" %}
Add the server to the `.vscode/mcp.json` file in your project. VS Code will ask for the key when it starts the server and store the key securely:

```json
{
  "inputs": [
    {
      "type": "promptString",
      "id": "navixy-api-key",
      "description": "Navixy API key",
      "password": true
    }
  ],
  "servers": {
    "navixy": {
      "type": "http",
      "url": "https://nvx4.<REGION>.navixy.com/workspaces/<WORKSPACE_ID>/mcp",
      "headers": {
        "Authorization": "Bearer ${input:navixy-api-key}"
      }
    }
  }
}
```

{% endtab %}

{% tab title="ChatGPT (Codex)" %}
Add the server to the `~/.codex/config.toml` file. Codex reads the key from the `NAVIXY_API_KEY` environment variable:

```toml
[mcp_servers.navixy]
url = "https://nvx4.<REGION>.navixy.com/workspaces/<WORKSPACE_ID>/mcp"
bearer_token_env_var = "NAVIXY_API_KEY"
```

{% endtab %}

{% tab title="Claude Desktop" %}
Claude Desktop connects through `mcp-remote`, a small program that needs [Node.js](https://nodejs.org). Go to **Settings**, select **Developer**, click **Edit Config**, and add the server to the file that opens:

```json
{
  "mcpServers": {
    "navixy": {
      "command": "npx",
      "args": [
        "mcp-remote",
        "https://nvx4.<REGION>.navixy.com/workspaces/<WORKSPACE_ID>/mcp",
        "--header",
        "Authorization:${AUTH_HEADER}"
      ],
      "env": {
        "AUTH_HEADER": "Bearer <YOUR_API_KEY>"
      }
    }
  }
}
```

Quit Claude Desktop completely and open it again.
{% endtab %}
{% endtabs %}

Any other AI client connects in the same way if it can send a header to a remote MCP server: the `Authorization` header with the word `Bearer`, a space, and the key.
{% endstep %}

{% step %}

### Ask your first question

Start a new conversation and ask this question:

```
Which Navixy workspace are you connected to?
```

The AI client answers with your workspace's name. Now ask about your fleet, for example, "Where are my vehicles now?"

{% hint style="warning" %}
If the AI client shows no Navixy tools or opens a sign-in page in your browser, the address or the key is wrong. See [Troubleshooting](troubleshooting.md).
{% endhint %}
{% endstep %}
{% endstepper %}

{% hint style="success" %}
Your AI client is connected to your Navixy workspace. It answers from live data and makes the changes that the key's role allows.
{% endhint %}

## Next steps

* [Navixy MCP Server capabilities](capabilities.md): Learn what the AI client can do in Navixy, with example prompts.
* [Troubleshooting](troubleshooting.md): Fix an AI client that can't connect or can't make a change.
* [Use Navixy documentation with AI](use-navixy-documentation-with-ai.md): Connect your AI client to the Navixy documentation too, so it can look up the API while it works.
