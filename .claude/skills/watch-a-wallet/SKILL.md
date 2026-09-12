---
name: watch-a-wallet
description: Watch an EVM wallet with Alchemy MCP. Use when the user asks to notify me when this wallet moves, track treasury inflows or outflows, watch a whale, or preview wallet alerts. Previews transfers on Free, creates one temporary Address Activity webhook only after explicit consent, verifies it, and requires confirmed teardown by ID.
---

This is the Claude Code entry point for the skill. The playbook lives at the repo root so other agents can use it too.

Read `skills/watch-a-wallet/SKILL.md` and follow it exactly. Treat the user's argument as the address to watch. Default network is `eth-mainnet`. Select an Alchemy app first. Show the exact webhook proposal and wait for explicit creation consent; deletion requires separate confirmation of the temporary ID. Never sign, send, broadcast, modify apps, use gas policies, or delete a preexisting webhook.

Ready-made inputs are in `skills/watch-a-wallet/PROMPTS.md`. Reference runs are in `skills/watch-a-wallet/examples/`.
