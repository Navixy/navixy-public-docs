---
description: >-
  Get answers from the Navixy documentation in ChatGPT, Claude, Cursor, or
  another AI tool: open a page in an AI chat or connect the documentation.
tags:
  - guide
noIndex: true
---

# Use Navixy documentation with AI

{% columns %}
{% column width="41.66666666666667%" %}
Get answers from the Navixy documentation in the AI tool that you already use. Every documentation page has shortcuts that copy the page or open it in ChatGPT or Claude. You can also connect your AI tool to the whole documentation through MCP.
{% endcolumn %}

{% column width="58.33333333333333%" %}
<figure><img src=".gitbook/assets/image (1).png" alt="The Copy menu of a documentation page, opened from the arrow next to Copy. It has the items Copy page, View as Markdown, Open in ChatGPT, Open in Claude, Connect with MCP, Connect to VSCode, and Export as PDF, each with a short description." width="269"><figcaption></figcaption></figure>
{% endcolumn %}
{% endcolumns %}

## Use the page shortcuts

| Shortcut                  | What it does                                      | When to use it                                                                        |
| ------------------------- | ------------------------------------------------- | ------------------------------------------------------------------------------------- |
| **Copy page**             | Copies the full page as Markdown                  | Paste full context into any AI assistant                                              |
| **View as Markdown**      | Opens the page as plain text in a new tab         | Inspect the raw source or copy specific sections manually                             |
| **Copy** (on code blocks) | Copies a code block directly                      | Use for `curl` commands, JSON payloads, and config snippets to avoid selection errors |
| **Open in ChatGPT**       | Opens the page in ChatGPT with content pre-loaded | Ask questions or summarize without pasting anything                                   |
| **Open in Claude**        | Opens the page in Claude with content pre-loaded  | Ask questions or summarize without pasting anything                                   |
| **Export as PDF**         | Downloads the page as a PDF                       | Save a snapshot for offline use or share outside the docs                             |

## Open a page in ChatGPT or Claude

Both shortcuts send the current page to the AI chat. You can start asking at once, without copying or pasting. Use them for a quick answer about one page.

{% columns %}
{% column %}
**Good prompts to start with:**

* Summarize this page before I read it in full.
* Turn this into a checklist of prerequisites.
* Generate edge cases and test ideas from this reference.
* Explain what \[parameter] does and when I'd change it.
{% endcolumn %}

{% column %}
**Getting better output:**

Add a constraint to shape the response. Ask for "steps only", "return JSON", or "assumptions first". If the page is long, specify which section you want the assistant to focus on.
{% endcolumn %}
{% endcolumns %}

## Connect the whole documentation through MCP

Page shortcuts work well when you already have the right page open. MCP (Model Context Protocol) goes further: your AI tool searches the Navixy documentation and reads any page when it needs one, without you finding and opening the page first. The connection helps most during development, when you move across several API references, device configurations, and integration guides in one session.

Once connected, your AI tool retrieves answers directly from the published documentation and can link to the sections that it used.

Navixy Documentation MCP Server is separate from Navixy MCP Server, which is coming soon. The documentation server gives your AI tool the documentation, and Navixy MCP Server will give it the data of your workspace.

## Find the documentation address

The documentation server is public and requires no authentication:

```
https://navixy.com/docs/~gitbook/mcp
```

You can also copy this address from the **Connect with MCP** option in the page shortcuts menu.

## Add the documentation server to your AI tool

The fastest way to connect is to ask your AI client. AI clients change their settings often, and the client knows its own current setup better than any guide. Send this prompt:

```
Add a remote MCP server named "navixy-docs" at this address:
https://navixy.com/docs/~gitbook/mcp
It needs no authentication.
```

If you prefer to set up the AI client by hand, use the example for your client.

{% tabs %}
{% tab title="Claude Code" %}
Run this command in a terminal:

```bash
claude mcp add --transport http --scope user navixy-docs https://navixy.com/docs/~gitbook/mcp
```

`--scope user` adds the server for all your projects. Without it, Claude Code adds the server only for the folder where you run the command.
{% endtab %}

{% tab title="Cursor" %}
Add the server to the `mcp.json` file, which opens from the MCP section of **Cursor Settings**. For one project only, use `.cursor/mcp.json` in the project's root folder.

```json
{
  "mcpServers": {
    "navixy-docs": {
      "url": "https://navixy.com/docs/~gitbook/mcp"
    }
  }
}
```

Quit Cursor completely and start it again.
{% endtab %}

{% tab title="VS Code" %}
The fastest way is the **Connect to VSCode** option in the page shortcuts menu. It opens a prompt that installs the server in VS Code.

To set it up by hand, add the server to the `.vscode/mcp.json` file in your project:

```json
{
  "servers": {
    "navixy-docs": {
      "type": "http",
      "url": "https://navixy.com/docs/~gitbook/mcp"
    }
  }
}
```
{% endtab %}

{% tab title="Codex" %}
Run this command in a terminal:

```bash
codex mcp add navixy-docs --url https://navixy.com/docs/~gitbook/mcp
```
{% endtab %}

{% tab title="Claude Desktop" %}
Go to **Settings**, then **Connectors**, and click **Add custom connector**. Enter the name `navixy-docs` and the address `https://navixy.com/docs/~gitbook/mcp`.

If your Claude plan has no custom connectors, connect through `mcp-remote`, a small program that needs [Node.js](https://nodejs.org). Go to **Settings**, select **Developer**, click **Edit Config**, and add the server to the file that opens:

```json
{
  "mcpServers": {
    "navixy-docs": {
      "command": "npx",
      "args": [
        "mcp-remote",
        "https://navixy.com/docs/~gitbook/mcp"
      ]
    }
  }
}
```

Quit Claude Desktop completely and start it again. A window reload is not enough.
{% endtab %}

{% tab title="Other tools" %}
If your tool connects to a remote MCP server over HTTP, add the address `https://navixy.com/docs/~gitbook/mcp`.

If your tool supports only local MCP commands, use the Claude Desktop configuration with `mcp-remote` from the Claude Desktop tab.

In both cases, restart the tool completely after you add the server.
{% endtab %}
{% endtabs %}

## Check the connection

After setup, run this prompt to confirm that the server is responding:

```
Use the docs MCP server navixy-docs.
What pages cover IoT Logic flows?
List the pages you found and link each one.
```

If the response includes links to specific Navixy documentation pages, the connection works. You can then ask the AI tool to fetch any page, find endpoints, or follow a full workflow across several sections.

## See also

* [Navixy MCP Overview](README.md): Learn which Navixy AI tools there are and how to choose between them.
* [Navixy AI Assistant](navixy-ai-assistant/README.md): Ask the chat built into Navixy about your fleet.
