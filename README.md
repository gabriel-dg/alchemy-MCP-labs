# alchemy-mcp-labs

Hands-on labs for **Alchemy MCP**: give your coding agent (Claude Code, Cursor, VS Code, Codex) live read access to 70+ blockchains, then use it for something useful.

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

## Quick start (about 10 minutes)

1. **Connect** your agent to `https://mcp.alchemy.com/mcp` and create one Alchemy app. Follow [SETUP.md](SETUP.md).
2. **Open this repo** in your agent: run `claude` from the repo folder, or open the folder in Cursor or VS Code. The prompts reference files by path, so the agent needs to be inside the repo.
3. **Run Lab 0**: paste the prompt from [labs/00-hello-mcp](labs/00-hello-mcp/README.md). Five tool calls that prove the connection works.
4. **Run Lab 1**: paste a prompt from [labs/01-before-you-sign](labs/01-before-you-sign/README.md). A real pre-sign safety report on a wallet, a mined transaction, or unsigned calldata.

## Labs

| # | Lab | Time | What you learn |
|---|-----|------|----------------|
| 0 | [hello-mcp](labs/00-hello-mcp/) | 5 min | Connection check. Select an app, list networks, read a block number and a balance. |
| 1 | [before-you-sign](labs/01-before-you-sign/) | 15 min | Preflight a wallet (ENS or address), inspect a mined transaction, or **simulate unsigned calldata** and get an **OK / REVIEW / DO NOT SIGN** verdict. Runs on the Free tier. |
| 2 | [contract-inspector](labs/02-contract-inspector/) | 10 min | Answer "what is this address?": type, proxy, verified source, token identity, price, age, activity, and an **ESTABLISHED / UNCERTAIN / RED FLAGS / NOT A CONTRACT** assessment. The follow-up to every Lab 1 REVIEW. |

## What a lab looks like

Every lab README has the same sections: **Goal**, **Run it** (a prompt to paste), **What you should see**, **Reading the output**, **Try your own**, **Troubleshooting**. When a lab uses a skill, the skill folder holds the agent playbook (`SKILL.md`), the copy-paste prompts (`PROMPTS.md`), and reference runs with real observed values (`examples/`).

## Repo map

```
labs/                  walkthroughs for humans (start here)
  00-hello-mcp/
  01-before-you-sign/
  02-contract-inspector/
skills/                playbooks for agents (what a lab runs)
  before-you-sign/
  contract-inspector/
docs/how-it-works.md   how the pieces fit, glossary, tool map
SETUP.md               connect your agent, create an app, Free vs paid
CLAUDE.md              entry point for Claude Code when it opens this repo
CONTRIBUTING.md        how to add a lab or a skill
```

## Roadmap

Planned, not yet in the repo. The first three continue the safety story of Labs 1 and 2; the rest widen the tool coverage.

- **token-check**: is this token real? Metadata, price, transfer volume, and holder spread against known clones
- **allowance-audit**: what have I already approved? Needs the Approval event history, which the Free tier caps at a 10-block log window, so this one is PAYG or uses transfers as a proxy
- **multichain-brief**: one address or ENS, tokens and NFTs across Ethereum L2s with USD totals
- **solana-wallet-brief**: the Lab 1 wallet briefing for a Solana address, via the Digital Asset Standard API
- **usage-cost**: what did all this cost me? Compute units per lab from the usage tools, as an appendix to Lab 0

## Links

- [Alchemy MCP server docs](https://www.alchemy.com/docs/alchemy-mcp-server)
- [Alchemy dashboard](https://dashboard.alchemy.com)
- [Model Context Protocol](https://modelcontextprotocol.io)

## License

MIT. See [LICENSE](LICENSE).
