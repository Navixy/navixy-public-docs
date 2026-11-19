---
description: >-
  Ask the AI Assistant built into Navixy about your fleet, or connect Claude,
  Cursor, or another AI client to your Navixy data and documentation.
tags:
  - overview
  - beta
noIndex: true
---

# AI solutions

To find an answer about your fleet, you usually open the right screen, set filters, and read through the results. With the Navixy AI tools, you ask in plain language instead: in the AI Assistant built into Navixy, or in the AI client that you already use, such as Claude or Cursor.

Two of these tools connect your own AI client to Navixy through [MCP (Model Context Protocol)](https://modelcontextprotocol.io/). MCP is an open standard that Claude, Cursor, and many other AI tools support. One tool connects the client to your live data, and the other connects it to the Navixy documentation.

{% hint style="info" %}
**Coming soon.** Navixy MCP Server isn't available yet. You can already use Navixy AI Assistant and Navixy Documentation MCP Server.
{% endhint %}

You can ask questions like these:

* "Where is Volvo FH 4521 now?"
* "Show me the trips of Volvo FH 4521 yesterday."
* "How did the fuel level of Volvo FH 4521 change over the last three days?"
* "Which assets are inside the Main depot geo object?"

## Navixy AI tools

Navixy offers three AI tools:

| Tool | What it does | Setup required |
| ---- | ------------ | -------------- |
| [Navixy AI Assistant](navixy-ai-assistant/README.md) | Built into Navixy products. It answers questions about your work from your live data. | None |
| Navixy MCP Server | Connects your AI client to the live data of your Navixy workspace and lets it make changes that you ask for. | Coming soon |
| [Navixy Documentation MCP Server](use-navixy-documentation-with-ai.md) | Gives your AI client access to the full Navixy documentation, so it can find an answer on any page when it needs one. | Connect your client to the documentation address. It needs no key. |

The three tools work independently. You can use any of them or all three.

## Navixy AI Assistant

[Navixy AI Assistant](navixy-ai-assistant/README.md) is built into Navixy products. In each product, it's specialized in the work that you do there. For example, it builds a report from a request in plain language, or answers questions about the figures on a dashboard. It works with your own sign-in and needs no setup.

## Navixy MCP Server

Navixy MCP Server connects your own AI client to one Navixy workspace. Once connected, your AI client can:

* Find assets and check their position, motion status, and latest sensor values
* Review trips, stops, routes, harsh driving events, and sensor history
* Find assets whose devices stopped reporting, assets that haven't moved, and assets inside a geo object
* Look up asset types, asset groups, devices, geo objects, schedules, tags, and the audit log
* Create, rename, and delete assets
* Create asset types and geo object types
* Create, change, and delete geo objects and asset groups
* Activate GPS devices

Deletions and device activations wait for a person's approval in Navixy. You name assets, geo objects, and asset groups as you see them in Navixy, and the AI client finds them on its own.

For example, ask your AI client:

* "Which assets had harsh braking or harsh acceleration this week?"
* "Which assets haven't moved for a week?"
* "Which devices haven't reported since yesterday?"

### How the connection works

An AI client can't see your data on its own. If you ask Claude or any other AI about your fleet, it doesn't know that your vehicles exist.

Navixy MCP Server connects your AI client to a single Navixy workspace. When you ask, the client looks up live data in Navixy and makes the changes that you ask for. You don't need to understand MCP to use it. The server describes its own tools to your AI client, so the client already knows what it can do with Navixy. When you aren't sure how to do something, ask the client.

The AI client signs in to Navixy with an API key created in Console. The key decides what the client can do:

* It works with one workspace, the workspace that you connect it to.
* It sees and changes only what the key's role allows.
* Every change that it makes is recorded in the audit log under the key's name.
* Deleting the key in Console disconnects the AI client within 5 minutes.

Create a separate key for each AI client, with a role that covers only what the client needs. The client sends Navixy's answers to its language model, so the client's vendor receives the data that the client reads.

### Supported AI clients

Navixy MCP Server works with AI clients that connect to a remote MCP server and send an API key with every request. These clients can do that:

* Claude Code
* Claude Desktop
* Cursor
* VS Code with GitHub Copilot
* ChatGPT and Codex
* Gemini CLI and opencode

## Navixy Documentation MCP Server

[Navixy Documentation MCP Server](use-navixy-documentation-with-ai.md) gives your AI client access to the full Navixy documentation. The client searches for a page and reads it when it needs one, for example, when you work across several API references in one session. The documentation server needs no key.

## How to choose the right tool

| If you want to... | Use this tool |
| ----------------- | ------------- |
| Ask about your fleet without setting anything up | Navixy AI Assistant |
| Ask about your fleet while you work in Navixy | Navixy AI Assistant |
| Ask about your fleet from the AI client that you already use every day | Navixy MCP Server |
| Create, change, or delete assets, geo objects, and asset groups, or activate devices, from your AI client | Navixy MCP Server |
| Let a coding agent see your real data while it builds an app | Navixy MCP Server |
| Give your AI client access to Navixy documentation | Navixy Documentation MCP Server |
| Ask an AI tool that supports MCP about Navixy features, even without a Navixy workspace | Navixy Documentation MCP Server |

{% hint style="info" %}
An app that a coding agent builds for you doesn't use Navixy MCP Server. It calls [Navixy GraphQL API](https://app.gitbook.com/s/eaPUYn5ATRhY1fALtOkq/) with an API key of its own.
{% endhint %}

## Where to start

* [Navixy AI Assistant](navixy-ai-assistant/README.md): Ask the assistant built into Navixy products about your fleet.
* [Use Navixy documentation with AI](use-navixy-documentation-with-ai.md): Connect your AI client to the Navixy documentation.

## See also

* [API keys](https://app.gitbook.com/s/9BfYVQ80Aby5RbZ9vQVj/api-keys) in the Console documentation: Learn how API keys work and when a key stops working.
* [Navixy GraphQL API](https://app.gitbook.com/s/eaPUYn5ATRhY1fALtOkq/): Build your own integration over the same data.
