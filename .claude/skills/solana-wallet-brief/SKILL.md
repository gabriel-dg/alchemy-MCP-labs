---
name: solana-wallet-brief
description: Read-only Solana wallet or transaction brief via Alchemy MCP. Use when the user gives a Solana address or transaction signature and asks what does this Solana wallet hold, how much SOL is here in dollars, what did this wallet do last, what did this Solana transaction do, who signed it, show its NFTs or compressed NFTs, or wants a Solana briefing. Returns SOL and token balances with USD values, recent activity, one transaction decoded into plain English, assets via the Digital Asset Standard, and a coverage note.
---

This is the Claude Code entry point for the skill. The playbook lives at the repo root so other agents can use it too.

Read `skills/solana-wallet-brief/SKILL.md` and follow it exactly. Treat the user's argument as the Solana address or transaction signature to brief. Default network is `solana-mainnet`; a second argument `devnet` or a `Network: solana-devnet` line switches to devnet. Select an Alchemy app first if none is selected. Never sign, send, broadcast, or request an airdrop.

Ready-made inputs are in `skills/solana-wallet-brief/PROMPTS.md`. Reference runs are in `skills/solana-wallet-brief/examples/`.
