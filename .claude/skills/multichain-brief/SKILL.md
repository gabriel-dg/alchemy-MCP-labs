---
name: multichain-brief
description: Read-only multi-chain holdings brief via Alchemy MCP. Use when the user asks what does this wallet hold across chains, how much is this address worth, show balances on Ethereum and its L2s in dollars, what is this wallet's exposure, or wants a 7-day price change on what an address holds. Returns native and token balances per network with USD values, a total, a 7-day price move, and a coverage note.
---

This is the Claude Code entry point for the skill. The playbook lives at the repo root so other agents can use it too.

Read `skills/multichain-brief/SKILL.md` and follow it exactly. Treat the user's argument as the address or ENS name to brief. Default networks are the five in the skill. Select an Alchemy app first if none is selected. Never sign, send, or broadcast.

Ready-made inputs are in `skills/multichain-brief/PROMPTS.md`. Reference runs are in `skills/multichain-brief/examples/`.
