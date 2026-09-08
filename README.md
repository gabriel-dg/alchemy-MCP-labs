# alchemy-mcp-labs

Hands-on labs for **Alchemy MCP**: give your coding agent (Claude Code, Cursor, VS Code, Codex) live read access to 160+ blockchains, then use it for something useful.

**Alchemy MCP** is a hosted server that exposes Alchemy's blockchain APIs as tools an AI agent can call: balances, token holdings, NFTs, transaction lookups, transaction simulation, Solana data. **MCP** (Model Context Protocol) is the open standard that lets agents discover and call those tools. You connect once with your Alchemy account. No API keys in config files, no code to write.

This repo gives you:

- **Labs** in `labs/`: step-by-step walkthroughs you run by pasting a prompt into your agent. Each one tells you what to expect, how to read the result, and how to adapt it to your own wallet or transaction.
- **Skills** in `skills/`: reusable playbooks that tell the agent exactly which tools to call and how to report. Labs use them. You can also install them so your agent runs them by name.

Everything here is read-only. Nothing signs, sends, or broadcasts.

## Who this is for

- Developers who want to see what Alchemy MCP does before writing integration code
- Anyone using a coding agent who wants live onchain data inside their workflow
- People who want a second opinion before signing a transaction

You do not need to know Solidity. You need a free Alchemy account and an agent that supports MCP.

## Quick start (about 40 minutes)

1. **Get the repo**:

   ```bash
   git clone https://github.com/gabriel-dg/alchemy-MCP-labs.git
   cd alchemy-MCP-labs
   ```

2. **Connect** your agent to `https://mcp.alchemy.com/mcp` and pick one Alchemy app. Follow [SETUP.md](SETUP.md). Run the connect command from inside the repo folder, or the server may not be visible when you open it.
3. **Open the repo** in your agent: run `claude` (or your agent's equivalent) from the repo folder, or open the folder in Cursor, VS Code, or another MCP client. The prompts reference files by path, so the agent needs to be inside the repo.
4. **Run Lab 0**: paste the prompt from [labs/00-hello-mcp](labs/00-hello-mcp/README.md). Five tool calls that prove the connection works.
5. **Run Lab 1**: paste a prompt from [labs/01-before-you-sign](labs/01-before-you-sign/README.md). A real pre-sign safety report on a wallet, a mined transaction, or unsigned calldata.
6. **Run Lab 2**: paste a prompt from [labs/02-contract-inspector](labs/02-contract-inspector/README.md) on an address Lab 1 told you to look at. That loop is the point of the pair.
7. **Run Lab 3**: paste a prompt from [labs/03-multichain-brief](labs/03-multichain-brief/README.md). The same address on five chains, in dollars, from one call.

## Labs

| # | Lab | Time | What you learn |
|---|-----|------|----------------|
| 0 | [hello-mcp](labs/00-hello-mcp/) | 5 min | Connection check. Select an app, list networks, read a block number and a balance. |
| 1 | [before-you-sign](labs/01-before-you-sign/) | 15 min | Preflight a wallet (ENS or address), inspect a mined transaction, or **simulate unsigned calldata** and get an **OK / REVIEW / DO NOT SIGN** verdict. Runs on the Free tier. |
| 2 | [contract-inspector](labs/02-contract-inspector/) | 10 min | Answer "what is this address?": type, proxy, verified source, token identity, price, age, activity, and an **ESTABLISHED / UNCERTAIN / RED FLAGS / NOT A CONTRACT** assessment. The follow-up to every Lab 1 REVIEW. |
| 3 | [multichain-brief](labs/03-multichain-brief/) | 10 min | One call, every chain, in dollars. Native and token balances for an address or ENS name across Ethereum, Base, Arbitrum, OP Mainnet and Polygon, a USD total with a coverage line, a watchlist for tokens page 1 cannot see, and the 7-day price change. |

## What a lab looks like

Every lab README has the same sections: **Goal**, **Run it** (a prompt to paste), **What you should see**, **Reading the output**, **Try your own**, **Troubleshooting**. When a lab uses a skill, the skill folder holds the agent playbook (`SKILL.md`), the copy-paste prompts (`PROMPTS.md`), and reference runs with real observed values (`examples/`).

## Repo map

```
labs/                  walkthroughs for humans (start here)
  00-hello-mcp/
  01-before-you-sign/
  02-contract-inspector/
  03-multichain-brief/
skills/                playbooks for agents (what a lab runs)
  before-you-sign/
  contract-inspector/
  multichain-brief/
docs/how-it-works.md   how the pieces fit, glossary, tool map
SETUP.md               connect your agent, create an app, Free vs paid
CLAUDE.md              entry point for Claude Code when it opens this repo
CONTRIBUTING.md        how to add a lab or a skill
```

## Roadmap

Planned, not yet in the repo. Every tool below was probed on the Free tier on 2026-09-07 and works. Labs 1 and 2 are the **safety** track; Lab 3 opened the **explore** track, and the next two continue it, each showing a tool family the repo has not touched yet; the last two are **operate**.

| # | Lab | One-line hook | Tool family it introduces |
|---|-----|---------------|---------------------------|
| 4 | **nft-collection-brief** | Is this NFT worth what they say? Collection metadata, floor price, holder count, and the rarity of one token id. | NFT API: floor price, owners, attributes, rarity |
| 5 | **solana-wallet-brief** | The Lab 1 wallet briefing for a Solana address: balance, assets via the Digital Asset Standard, recent signatures. Needs Solana enabled on your app. | Solana RPC and DAS |
| 6 | **allowance-check** | Which well-known spenders can already move my tokens? A matrix of your address against a list of known routers and marketplaces. Honest scope: unknown spenders need event history, which Free caps at 10 blocks. | Allowance reads at scale |
| 7 | **wallet-checkup** | One prompt that runs Labs 1, 2, and 6 on your own wallet and merges them into a single report. A capstone that shows skills composing. | Agent orchestration, no new tools |
| 8 | **watch-a-wallet** | Get notified when an address moves. Creates an Alchemy webhook, so it is the one lab that writes to your account. Opt-in, clearly labelled, with teardown. | Notify webhooks |

Dropped from the earlier list: **token-check**, because Lab 2 already covers metadata, price, counterfeit detection, and burst patterns for a token address. **aa-session-lab** is folded into a possible Lab 1 appendix that explains a UserOp by hash, read-only. Usage and cost tools are a step in Lab 0 rather than a lab of their own.

## Links

- [Alchemy MCP server docs](https://www.alchemy.com/docs/alchemy-mcp-server)
- [Alchemy dashboard](https://dashboard.alchemy.com)
- [Model Context Protocol](https://modelcontextprotocol.io)

## License

MIT. See [LICENSE](LICENSE).
