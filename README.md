# alchemy-mcp-labs

A lab of Alchemy MCP skills: live onchain tools inside your coding agent — no glue code.

Hosted MCP is Alchemy’s tool server at https://mcp.alchemy.com/mcp (OAuth, no API key). MCP (Model Context Protocol) is the tools your coding agent can call.

## Setup

Connect the hosted MCP server first: see [SETUP.md](SETUP.md).

## Skills

| Skill | Status |
| --- | --- |
| [before-you-sign](skills/before-you-sign/) | shipped — validated on Alchemy Free with hosted MCP |
| multichain-brief | planned |
| aa-session-lab | planned |
| solana-das-gallery | planned |

Planned names are index-only; no folders yet.

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
