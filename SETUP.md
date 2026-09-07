# Setup: Alchemy hosted MCP

Connect your coding agent to Alchemy's hosted MCP server. No API key belongs in client config.

## Endpoint

- MCP URL: `https://mcp.alchemy.com/mcp`
- Auth: OAuth with your Alchemy account
- Do not paste API keys into MCP client JSON

Create an account or sign in at https://dashboard.alchemy.com (free is enough).

## Claude Code

```bash
claude mcp add alchemy --transport http https://mcp.alchemy.com/mcp
```

Then restart Claude Code or start a new session. Run `/mcp`, select `alchemy`, and complete browser OAuth on the first tool call.

## Codex

```bash
codex mcp add alchemy --url https://mcp.alchemy.com/mcp
```

## Cursor / VS Code / Claude Desktop

Add a hosted HTTP MCP server entry that points at:

```json
{
  "mcpServers": {
    "alchemy": {
      "type": "streamable-http",
      "url": "https://mcp.alchemy.com/mcp"
    }
  }
}
```

The URL must be `https://mcp.alchemy.com/mcp`. Complete OAuth when prompted. Do not add an API key field.

### Cursor

Config file paths:

- Project: `.cursor/mcp.json` (inside the repo)
- Global (Windows): `%USERPROFILE%\.cursor\mcp.json`
- Global (macOS/Linux): `~/.cursor/mcp.json`

Use the `mcpServers` JSON shape above. Then restart Cursor and check Settings → MCP for "alchemy".

### VS Code Copilot

Config file path:

- Project: `.vscode/mcp.json`

Official shape uses a top-level `"servers"` key (not `"mcpServers"`):

```json
{
  "servers": {
    "alchemy": {
      "type": "http",
      "url": "https://mcp.alchemy.com/mcp"
    }
  }
}
```

### Claude Desktop

Config file paths:

- Windows: `%APPDATA%\Claude\claude_desktop_config.json`
- macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`

Use the `mcpServers` JSON shape above. Restart Claude Desktop after save.

## After connect: select an app

You must run `select_app` (ask the agent: "Select an Alchemy app") before most tools work. Skipping this is the most common cause of empty or missing tools.

### What is an Alchemy app

- An Alchemy app is a project in https://dashboard.alchemy.com
- `list_apps` shows yours; `select_app` caches one for RPC/Data tools
- If you have none, create one in the dashboard. Do not ask the agent to `create_app` during the skill run.
- Pick any app that includes the network you will query (`eth-mainnet` for the first skill)

## Free vs PAYG (pay-as-you-go)

| | |
| --- | --- |
| **Free** | RPC reads, transfers, token balances, simulation, NFTs without spam filters |
| **PAYG** | NFT `excludeFilters` SPAM, Trace API, Debug API. `isSpamContract` may 400 |

First skill is designed to complete on Free.

## Networks

Use `list_chains` for exact network name strings. Default for skills in this lab is `eth-mainnet` unless the user names another network.

## Troubleshoot

| Symptom | Likely fix |
| --- | --- |
| Tools missing / empty list | Re-auth OAuth; confirm the server URL; run `select_app` |
| Calls fail after connect | Forgot `select_app` — select an Alchemy app |
| Wrong chain data | Pass the correct network from `list_chains` |
| Rate limits / throttling | Slow retries; check app plan limits in the Alchemy dashboard |

## Legacy local STDIO

A legacy local STDIO MCP server path exists in older docs. It is **not** the recommended path for this lab. Prefer the hosted HTTP endpoint above.

## Docs

- [Alchemy MCP server](https://www.alchemy.com/docs/alchemy-mcp-server)
- [Build with AI](https://www.alchemy.com/docs/build-with-ai)
