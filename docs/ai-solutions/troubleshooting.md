---
hidden: true
noIndex: true
description: >-
  Fix an AI client that can't connect to Navixy MCP Server, opens a sign-in page,
  uses the wrong workspace, can't make a change, or misses vehicles.
tags:
  - troubleshooting
  - beta
---

# Troubleshooting

Find out why your AI client can't connect to Navixy, can't make a change, or misses vehicles. Then fix the problem. The page covers Navixy MCP Server and Navixy Documentation MCP Server in any AI client. Your AI client can often find the cause itself: ask it what Navixy returned, and it tells you the error.

## The AI client shows no Navixy tools or can't connect

The AI client doesn't list Navixy among its tools, or it says that it can't reach the server.

Check these in order:

1. Restart the AI client. Most AI clients read their MCP settings only when they start. Quit the client completely, not only its window.
2. Check the server address. It starts with `https://nvx4.`, your region, and `.navixy.com`, and the region matches your Console address. It ends with `/workspaces/`, your workspace ID, and `/mcp`. A placeholder such as `<WORKSPACE_ID>` left in the address makes the server answer that it can't find the address.
3. Check the key in the AI client's settings. It starts with `nvx_`, and the header value is the word `Bearer`, a space, and the key.
4. Check in Console that the key exists and hasn't expired. Console deletes a key when the roles of the person who created it change.
5. Check that the key's role covers the workspace in the address.

A deleted or expired key can't work again. Create a new key and put it into the AI client's settings. The connection works when the client answers "Which Navixy workspace are you connected to?" with your workspace's name.

## Claude Code doesn't show the Navixy server

You added the server with `claude mcp add`, but a Claude Code session doesn't list it.

Check these in order:

1. Run `claude mcp list`. If the address of `navixy` is wrong or empty, the command was split across lines. Windows terminals don't join lines that end with `\`. Remove the server with `claude mcp remove navixy` and add it again with the command on one line.
2. Check where you ran the command. Without `--scope user`, Claude Code adds the server only for the folder where you ran it. Remove the server from that folder, then add it again with `--scope user`.
3. Start a new Claude Code session. A session that was open before you added the server doesn't see it.

## A sign-in page opens in the browser

Navixy MCP Server has no sign-in page. Some AI clients open one when the server refuses the API key or when the client sends no key. Close the page, then check the address and the key as described in [The AI client shows no Navixy tools or can't connect](#the-ai-client-shows-no-navixy-tools-or-cant-connect).

If your AI client has no setting for request headers, it can't connect at the moment. See [Supported AI clients](README.md#supported-ai-clients).

## Claude Desktop says that the server disconnected

Claude Desktop shows an error with the server's name when it loses the connection to the server.

To resolve this:

1. Quit Claude Desktop completely from its menu and open it again. Closing or reloading the window isn't enough.
2. Check that the `claude_desktop_config.json` file still has your key and your server address. See [Connect Navixy MCP Server](connect-navixy-mcp-server.md).
3. If the error remains, check that Node.js is installed. Claude Desktop connects through `mcp-remote`, which needs Node.js.

If Claude Desktop can't find `npx` on macOS, install Node.js system-wide with Homebrew:

```bash
brew install node
```

## The AI client works with the wrong workspace

The server address names one workspace, and the AI client sees only that workspace. Ask the client which workspace it's connected to. To change the workspace, replace the workspace ID in the address and restart the AI client. To work with two workspaces, add the server twice, with a different name and address for each workspace.

## A change waits or is refused

The AI client tells you why a change didn't happen. The common reasons are the following:

* **The change waits for approval.** Deletions, device activations, and changes over the daily limit need a person's approval. Open the link that the AI client gives you, approve the change in Navixy, and ask the client to continue. See [How changes work](capabilities.md#how-changes-work).
* **The key's role doesn't allow the change.** Create a key with a role that allows the change.
* **Someone changed it in the meantime.** Someone changed the asset, geo object, or asset group in Navixy after the AI client read it. Ask the client to try again.
* **A required custom field is empty.** The AI client fills in custom fields only when it creates an asset. Fill in the field in Navixy first.
* **The GPS device belongs to another asset.** The AI client doesn't move a device from one asset to another. Remove the device from the other asset in Navixy first.
* **It's still in use.** An asset, a geo object, or an asset group that is still in use can't be deleted, such as an asset that belongs to a group. Remove it from the group in Navixy first.
* **The key can't make changes on its own.** For some keys, every change waits for approval, or no change is allowed. Contact Navixy.

## The AI client says that personal data is off

Positions, trips, sensor readings, and custom field values count as personal data. Whether AI clients may read personal data is a setting of the Navixy server, and it's off unless Navixy turns it on. While it's off, the client can't search for vehicles by name or license plate. It also can't read their positions, trips, or sensor values, or check which vehicles are silent, idle, or inside a geo object. It can't create assets either. It can still list assets, asset groups, and geo objects by their titles, and it can still make other changes. Contact Navixy to learn whether the setting is on.

## Answers miss vehicles

Check these in order:

1. **The key's role.** The AI client sees only the vehicles that the role shows.
2. **The size of the fleet.** One check for silent, idle, or geo object vehicles reads up to 1,000 vehicles. Name an asset group in your question.
3. **The length of the period.** A long period of trips or readings comes in parts. Ask for a shorter period.

## The AI client says that it sent too many requests

One API key can send 100 requests per minute, and every AI client that uses the key shares that number. Wait a minute and ask again. Give each AI client its own key.

## The AI client answers about Navixy but doesn't cite sources

Navixy Documentation MCP Server returns pages with their links, but an AI client doesn't always show the links. Ask for links explicitly. You can also ask the AI client to quote a short phrase from the section that it used, to confirm that it retrieved the right content.

## Pasted content looks broken

Check for wrapped lines. Lines break most often in `curl` commands and long addresses copied from the documentation.

## Connection errors

If the AI client shows an HTTP status from Navixy MCP Server, this table gives the cause:

| Status | Cause | What to do |
| ------ | ----- | ---------- |
| 401 | The request has no key, or the key is wrong, deleted, or expired. | Check the key. Create a new key if the old key is deleted or expired. |
| 403 | The key's role doesn't cover the workspace in the address, the key needs a second sign-in step that a key can't give, or your organization doesn't have access to Navixy MCP Server. | Check the workspace ID and the key's role. Contact Navixy if both are right. |
| 404 | The address isn't a workspace's server address. | Check the address and the workspace ID. |
| 429 | The key sent too many requests. | Wait a minute and try again. |
| 502, 503 | The sign-in with the key is unavailable. | Try again later. Contact Navixy if the error repeats. |

## See also

* [Connect Navixy MCP Server](connect-navixy-mcp-server.md): Check your AI client's settings against the steps.
* [Navixy MCP Server capabilities](capabilities.md): Learn what the AI client can do and how changes are approved.
* [Use Navixy documentation with AI](use-navixy-documentation-with-ai.md): Connect your AI client to the Navixy documentation.
