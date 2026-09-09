# alchemy-mcp-labs

Hands-on labs for **Alchemy MCP**: give your coding agent (Claude Code, Cursor, VS Code, Codex) live read access to 160+ blockchains, then use it for something useful.

## You paste this

```text
/multichain-brief 0x1111111111111111111111111111111111111111
```

That is the Claude Code form. In any other MCP agent you paste the four-line prompt from [Lab 3](labs/03-multichain-brief/README.md) instead, with the address at the bottom.

## Your agent answers this

| Network | Balance | Price | USD |
|---------|---------|-------|-----|
| `eth-mainnet` | 5.717181 ETH | $2,497.33 | $14,277.69 |
| `base-mainnet` | 0.353822 ETH | $2,497.33 | $883.61 |
| `arb-mainnet` | 0.004091 ETH | $2,497.33 | $10.22 |
| `opt-mainnet` | 0.001816 ETH | $2,497.33 | $4.54 |
| `matic-mainnet` | 32.833741 POL | $0.0974 | $3.20 |

**Total $15,179.25.** Five chains read in one request, with the 7-day move on what it holds: ETH +0.9%, POL +4.9%.

Nobody owns that address. Its nonce is zero, so it has never sent a transaction. People just keep paying into it by mistake, on every chain, and airdrop spam finds it anyway: three of the tokens on page one have a URL in the symbol.

Real [Lab 3](labs/03-multichain-brief/) output, observed 2026-09-08. Six tool calls, about twenty seconds. **No code, no API key, no RPC URLs.**

## Why bother

**Alchemy MCP** is a hosted server that exposes Alchemy's blockchain APIs as tools an AI agent can call. **MCP** (Model Context Protocol) is the open standard that lets agents discover and call those tools. You connect once, over OAuth, with your Alchemy account.

What that buys you over pointing an agent at a public RPC node:

- **One endpoint, 160+ chains.** Ethereum, every major L2, Solana. No per-chain URLs to collect, rotate, or paste into a config file. Lab 4 briefs a Solana wallet over the same connection you used for Ethereum.
- **One request, five chains.** The table above is a single `getTokensByAddress` call with a `networks` list: balances, metadata and USD prices come back together, already joined. That is Lab 3.
- **More than JSON-RPC.** Token balances and metadata, USD prices with a year of daily history, NFTs, transfer history, and transaction simulation that shows what an unsigned transaction would do *before* you sign it. That last one is Lab 1.
- **No API keys anywhere.** OAuth, and the server routes through the app you select. Nothing secret lands in a config file you might commit.

This repo gives you:

- **Labs** in `labs/`: step-by-step walkthroughs you run by pasting a prompt into your agent. Each one tells you what to expect, how to read the result, and how to adapt it to your own wallet or transaction.
- **Skills** in `skills/`: reusable playbooks that tell the agent exactly which tools to call and how to report. Labs use them. You can also install them so your agent runs them by name.

Everything here is read-only. Nothing signs, sends, or broadcasts.

## Who this is for

- Developers who want to see what Alchemy MCP does before writing integration code
- Anyone using a coding agent who wants live onchain data inside their workflow
- People who want a second opinion before signing a transaction

You do not need to know Solidity. You need a free Alchemy account and an agent that supports MCP.

## Quick start

**First live result: five minutes.** The full five-lab track: about fifty.

1. **Get the repo**:

   ```bash
   git clone https://github.com/gabriel-dg/alchemy-mcp-labs.git
   cd alchemy-mcp-labs
   ```

2. **Connect** your agent to `https://mcp.alchemy.com/mcp` and pick one Alchemy app. Follow [SETUP.md](SETUP.md). Run the connect command from inside the repo folder, or the server may not be visible when you open it.
3. **Open the repo** in your agent: run `claude` (or your agent's equivalent) from the repo folder, or open the folder in Cursor, VS Code, or another MCP client. The prompts reference files by path, so the agent needs to be inside the repo.
4. **Run Lab 0**: paste the prompt from [labs/00-hello-mcp](labs/00-hello-mcp/README.md). Five tool calls that prove the connection works.
5. **Run Lab 1**: paste a prompt from [labs/01-before-you-sign](labs/01-before-you-sign/README.md). A real pre-sign safety report on a wallet, a mined transaction, or unsigned calldata.
6. **Run Lab 2**: paste a prompt from [labs/02-contract-inspector](labs/02-contract-inspector/README.md) on an address Lab 1 told you to look at. That loop is the point of the pair.
7. **Run Lab 3**: paste a prompt from [labs/03-multichain-brief](labs/03-multichain-brief/README.md). The same address on five chains, in dollars, from one call.
8. **Run Lab 4**: paste a prompt from [labs/04-solana-wallet-brief](labs/04-solana-wallet-brief/README.md). A Solana wallet in dollars, its newest transaction in one sentence, and its NFTs. Same connection, other VM. Needs Solana enabled on your app.

## Labs

| # | Lab | Time | What you learn |
|---|-----|------|----------------|
| 0 | [hello-mcp](labs/00-hello-mcp/) | 5 min | Connection check. Select an app, list networks, read a block number and a balance. |
| 1 | [before-you-sign](labs/01-before-you-sign/) | 15 min | Preflight a wallet (ENS or address), inspect a mined transaction, or **simulate unsigned calldata** and get an **OK / REVIEW / DO NOT SIGN** verdict. Runs on the Free tier. |
| 2 | [contract-inspector](labs/02-contract-inspector/) | 10 min | Answer "what is this address?": type, proxy, verified source, token identity, price, age, activity, and an **ESTABLISHED / UNCERTAIN / RED FLAGS / NOT A CONTRACT** assessment. The follow-up to every Lab 1 REVIEW. |
| 3 | [multichain-brief](labs/03-multichain-brief/) | 10 min | One call, every chain, in dollars. Native and token balances for an address or ENS name across Ethereum, Base, Arbitrum, OP Mainnet and Polygon, a USD total with a coverage line, a watchlist for tokens page 1 cannot see, and the 7-day price change. |
| 4 | [solana-wallet-brief](labs/04-solana-wallet-brief/) | 10 min | Same brief, other VM. SOL and tokens in dollars, a watchlist by mint, the last five signatures, the newest transaction decoded into one sentence (who signed, what moved), and NFTs including compressed ones via DAS. Paste a signature instead of an address to decode any transaction. Assets run on devnet until DAS opens on mainnet for Free apps. |

## What a lab looks like

Every lab README has the same sections: **Goal**, **Run it** (a prompt to paste), **What you should see**, **Reading the output**, **Try your own**, **Troubleshooting**. When a lab uses a skill, the skill folder holds the agent playbook (`SKILL.md`), the copy-paste prompts (`PROMPTS.md`), and reference runs with real observed values (`examples/`).

## Repo map

```
labs/                  walkthroughs for humans (start here)
  00-hello-mcp/
  01-before-you-sign/
  02-contract-inspector/
  03-multichain-brief/
  04-solana-wallet-brief/
skills/                playbooks for agents (what a lab runs)
  before-you-sign/
  contract-inspector/
  multichain-brief/
  solana-wallet-brief/
docs/how-it-works.md   how the pieces fit, glossary, tool map
SETUP.md               connect your agent, create an app, Free vs paid
CLAUDE.md              entry point for Claude Code when it opens this repo
CONTRIBUTING.md        how to add a lab or a skill
```

## Roadmap

Planned, not yet in the repo. Every tool below was probed on the Free tier and works. Labs 1 and 2 are the **safety** track; Labs 3 and 4 are the **explore** track, one for EVM chains and one for Solana; the next one continues it with a tool family the repo has not touched yet, and the last three are **operate**.

| # | Lab | One-line hook | Tool family it introduces |
|---|-----|---------------|---------------------------|
| 5 | **nft-collection-brief** | Is this NFT worth what they say? Collection metadata, floor price, holder count, and the rarity of one token id. | NFT API: floor price, owners, attributes, rarity |
| 6 | **allowance-check** | Which well-known spenders can already move my tokens? A matrix of your address against a list of known routers and marketplaces. Honest scope: unknown spenders need event history, which Free caps at 10 blocks. | Allowance reads at scale |
| 7 | **wallet-checkup** | One prompt that runs Labs 1, 2, and 6 on your own wallet and merges them into a single report. A capstone that shows skills composing. | Agent orchestration, no new tools |
| 8 | **watch-a-wallet** | Get notified when an address moves. Creates an Alchemy webhook, so it is the one lab that writes to your account. Opt-in, clearly labelled, with teardown. | Notify webhooks |

Dropped from the earlier list: **token-check**, because Lab 2 already covers metadata, price, counterfeit detection, and burst patterns for a token address. **aa-session-lab** is folded into a possible Lab 1 appendix that explains a UserOp by hash, read-only. Usage and cost tools are a step in Lab 0 rather than a lab of their own. **solana-wallet-brief** moved up from 5 to 4 and shipped, because it is the one lab that turns "labs for Ethereum" into "one MCP, two VMs".

## Links

- [Alchemy MCP server docs](https://www.alchemy.com/docs/alchemy-mcp-server) - the server these labs talk to
- [Alchemy dashboard](https://dashboard.alchemy.com) - create a free app, watch your compute units
- [Alchemy API reference](https://www.alchemy.com/docs/reference/api-overview) - the same data over HTTP, for when you move from asking to shipping
- [Model Context Protocol](https://modelcontextprotocol.io) - the open standard underneath

## License

MIT. See [LICENSE](LICENSE).
