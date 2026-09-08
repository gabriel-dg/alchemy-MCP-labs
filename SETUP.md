# Setup: connect your agent to Alchemy MCP

Time: about 5 minutes. You will create a free Alchemy account and app, connect your agent to the hosted MCP server, and select the app once.

## Prerequisites

- An Alchemy account. Free is enough for every lab here. Sign up at https://dashboard.alchemy.com
- An agent that speaks MCP. Claude Code, Cursor, VS Code with Copilot agent mode, Codex CLI and Claude Desktop have step-by-step instructions below; any other MCP client works too
- A browser for the one-time OAuth login

You do **not** need an API key. The hosted server authenticates with OAuth and uses the app you select. Never paste an API key into an MCP config file.

## Step 1: create or pick an Alchemy app

An "app" is a project in the Alchemy dashboard. The MCP server needs one to route your requests.

**Already have one?** If any of your apps has **Ethereum Mainnet** enabled, reuse it and skip to Step 2. You do not need a fresh app for these labs.

Otherwise:

1. Open https://dashboard.alchemy.com and sign in
2. Click **Apps**, then **Create new app**
3. Give it any name, for example `mcp-labs`
4. Make sure **Ethereum Mainnet** is enabled for it. Labs default to `eth-mainnet`. You can enable more networks later.
5. Save. You do not need to copy the API key.

The dashboard shows the app's API key on this screen. Ignore it: the hosted MCP server authenticates over OAuth and never asks for one.

## Step 2: connect your agent

The server URL is always:

```
https://mcp.alchemy.com/mcp
```

### Claude Code

Run this **in your terminal, from inside the cloned repo folder**, with Claude Code closed:

```bash
claude mcp add --transport http alchemy https://mcp.alchemy.com/mcp
```

`claude mcp add` defaults to `--scope local`, which registers the server for the directory you run it in. Run it anywhere else and `/mcp` will show no `alchemy` server when you open the repo. Add `-s user` instead if you want the connection available in every project.

Then start the agent from the repo folder with `claude`, run `/mcp`, choose `alchemy`, and pick **Authenticate**. A browser window opens for the Alchemy login. When it says connected, you are done. If Claude Code was already running when you added the server, restart it.

When you open this repo, Claude Code picks up `.claude/settings.json`, which pre-approves the read-only Alchemy tools the labs use. You will not be asked to confirm each call. Paid and account-mutating tools are not on the list.

### Cursor

Create `.cursor/mcp.json` in this repo (project scope) or `~/.cursor/mcp.json` (global; on Windows `%USERPROFILE%\.cursor\mcp.json`):

```json
{
  "mcpServers": {
    "alchemy": {
      "url": "https://mcp.alchemy.com/mcp"
    }
  }
}
```

Restart Cursor, open Settings, MCP, and confirm `alchemy` shows a green status. Complete the OAuth prompt if one appears.

### VS Code (Copilot agent mode)

Create `.vscode/mcp.json` in this repo. VS Code uses a top-level `servers` key:

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

Open the file and click **Start** above the server entry, or run **MCP: List Servers** from the command palette.

### Codex CLI

```bash
codex mcp add alchemy --url https://mcp.alchemy.com/mcp
```

### Claude Desktop

Claude Desktop connects to remote MCP servers through the UI, not the JSON config file (that file is for local stdio servers only). Open **Settings**, **Connectors**, **Add custom connector**, name it `alchemy`, paste the URL, and complete the login.

### Any other MCP client

The labs need only two things from an agent: an MCP client that can reach a **remote HTTP server with OAuth**, and the ability to **read files in this repo by path**. Point your client at `https://mcp.alchemy.com/mcp` with no API key and no headers.

Field names differ between clients, so check your client's own docs rather than copying a block above. Cursor uses `url`, VS Code uses `servers` plus `type`, and Antigravity CLI uses `serverUrl` in `.agents/mcp_config.json` and rejects `url`. Without a `.claude/` folder you will approve tool calls manually, and you run the full prompts from each skill's `PROMPTS.md` instead of slash commands. Everything else works the same.

## Step 3: select an app

Most tools need an app selected first. In your agent, say:

> List my Alchemy apps and select one.

The agent calls `list_apps`, then `select_app`. If you have several apps it will ask which one. The selection lasts for the session. Forgetting this step is the most common cause of "tools fail after connecting".

Do not ask the agent to create apps during a lab. Create them in the dashboard.

## Step 4: verify

Run [Lab 0](labs/00-hello-mcp/README.md). It takes two minutes and exercises the whole path.

## Free vs pay-as-you-go

Every lab in this repo completes on the Free tier. Some tools are paid and return a 400 error with a message mentioning "payg", "upgrade", or "billing". The skills know to skip those and note them in the report.

| Tier | What works |
|------|------------|
| Free | JSON-RPC reads, asset transfers, token balances, metadata and prices, transaction simulation, NFT lists without spam filters, event logs over a 10-block window |
| PAYG | NFT spam filters (`excludeFilters`, `spamConfidenceLevel`), Trace API, Debug API, event logs over wider block ranges. `isSpamContract` may also require it |

If you are on a paid plan and want traces or spam filters, say so in your prompt, for example "I am on PAYG, traces are fine."

## Networks

Network ids look like `eth-mainnet`, `base-mainnet`, `arb-mainnet`, `polygon-mainnet`, `solana-mainnet`. Ask the agent to call `list_chains` for the full list. Your selected app must have the network enabled in the dashboard.

## Troubleshooting

| Symptom | Likely fix |
|---------|-----------|
| `alchemy` server missing or no tools listed | Claude Code: `claude mcp add` registers the server for the directory you ran it in, so re-run it from the repo folder (or with `-s user`). Any client: check the URL is exactly `https://mcp.alchemy.com/mcp`, re-run the OAuth login, restart the agent. |
| Tool calls fail right after connecting | You skipped **Step 3**. Say "select an Alchemy app". |
| "app does not support network" or empty data on another chain | Enable that network for the app in the dashboard, or select a different app. |
| 400 error mentioning payg / upgrade / billing | Paid feature. The lab continues without it. See Free vs PAYG above. |
| Rate limit or throttling | Free apps have compute-unit limits. Wait a moment and retry, or check usage in the dashboard. |
| ENS name fails to resolve | The skill computes the namehash with `web3Sha3` and reads the ENS registry with two `ethCall`s, which works on every tier. See the recipe in [skills/before-you-sign/SKILL.md](skills/before-you-sign/SKILL.md). |

## Docs

- [Alchemy MCP server](https://www.alchemy.com/docs/alchemy-mcp-server)
- [Build with AI](https://www.alchemy.com/docs/build-with-ai)
- [Claude Code MCP](https://docs.claude.com/en/docs/claude-code/mcp)
