# alchemy-mcp-labs

A lab of Alchemy MCP skills: live onchain tools inside your coding agent — no glue code.

Hosted MCP is Alchemy’s tool server at https://mcp.alchemy.com/mcp (OAuth, no API key). MCP (Model Context Protocol) is the tools your coding agent can call.

## What you can do

Connect MCP once, then run a skill in Claude Code or Cursor.

**before-you-sign.** Preflight a wallet, a mined transaction, or calldata you are about to sign.
The agent calls Alchemy MCP and returns asset changes, risk flags, and **OK / REVIEW / DO NOT SIGN**.
Read-only: it does not broadcast.

**Next.** A multi-chain wallet snapshot, a smart-account session lab, and a Solana DAS gallery.

## Setup

Connect the hosted MCP server first: see [SETUP.md](SETUP.md).

## Skills

| Skill                                      | Status                 | What it does                                                                                                                       |
| ------------------------------------------ | ---------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| [before-you-sign](skills/before-you-sign/) | shipped — Alchemy Free | Preflight a wallet, a mined tx, or unsigned calldata. First run: `vitalik.eth`. The useful run: simulate an approve — do not send. |
| multichain-brief                           | planned                | One address or ENS → tokens (USD when the API has them) + NFTs across Ethereum L2s. A short briefing, not a portfolio app.         |
| aa-session-lab                             | planned                | ERC-4337 / smart-wallet session: capabilities, prepare calls, **simulate** a UserOp. Send stays optional and explicit.             |
| solana-das-gallery                         | planned                | Solana wallet or creator → assets via DAS. The other half of Alchemy MCP if you only know EVM.                                     |

Planned rows are index-only; no folders yet.

## 60-second path

1. Connect hosted MCP ([SETUP.md](SETUP.md))
2. Tell the agent: **Select an Alchemy app**
3. Paste [skills/before-you-sign/PROMPT.md](skills/before-you-sign/PROMPT.md) and run **Input 1 (`vitalik.eth`) only**. Placeholders (Inputs 2–3) are later.

## Unsigned calldata (Branch B)

If you later supply unsigned calldata, the agent must call `simulate*` (`simulateAssetChanges` / `simulateExecution`). Missing those calls on Branch B means you are not using the product. The first ENS run does not simulate.

## Deep link

- Skill: [skills/before-you-sign/](skills/before-you-sign/)
- Skill body: [skills/before-you-sign/SKILL.md](skills/before-you-sign/SKILL.md)
- Prompt: [skills/before-you-sign/PROMPT.md](skills/before-you-sign/PROMPT.md)
