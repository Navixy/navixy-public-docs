# Connecting other AI tools

This page provides generic connection instructions for MCP clients not covered by the dedicated setup guides. If you are using Claude Desktop, Cursor, ChatGPT, or Codex, see the dedicated pages for those clients instead.

## Compatibility requirements

To connect to Navixy MCP Server, your AI client must meet both of the following requirements:

* It supports remote MCP servers over Streamable HTTP or SSE transport.
* It supports OAuth or allows custom request headers for authentication.

## MCP addresses

<table><thead><tr><th width="182.4000244140625">Connection</th><th>URL</th></tr></thead><tbody><tr><td>User API</td><td><code>https://platform.mcp.navixy.com/user/mcp</code></td></tr><tr><td>Admin Panel API</td><td><code>https://platform.mcp.navixy.com/panel/mcp</code></td></tr></tbody></table>

## How to connect

The easiest way to connect Navixy MCP Server to any AI client is to ask it directly. Many AI tools can set up MCP connections from a conversation. Try a prompt like:

```
Connect an MCP server https://platform.mcp.navixy.com/user/mcp
```

If your client supports conversational MCP setup, it will guide you through the remaining steps. If not, it may provide client-specific instructions for your tool.

If the conversational approach doesn't work, the sections below cover the two standard connection patterns that most MCP-compatible clients support.

## How to authenticate

If your MCP client supports OAuth, add the endpoint URL. Your client will open the Navixy login page automatically to complete authentication. No additional configuration is required.



