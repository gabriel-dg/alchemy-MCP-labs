---
name: contract-inspector
description: Read-only identity check for any EVM address via Alchemy MCP. Use when the user asks who is this address, what is this contract, is this spender legit, is this token real, inspect this contract, or after a before-you-sign REVIEW names an unknown address. Returns type, identity, trust signals, red flags, and an ESTABLISHED / UNCERTAIN / RED FLAGS / NOT A CONTRACT assessment.
---

This is the Claude Code entry point for the skill. The playbook lives at the repo root so other agents can use it too.

Read `skills/contract-inspector/SKILL.md` and follow it exactly. Treat the user's argument as the address to inspect. Default network `eth-mainnet`. Select an Alchemy app first if none is selected. Never sign, send, or broadcast.

Ready-made inputs are in `skills/contract-inspector/PROMPTS.md`. Reference runs are in `skills/contract-inspector/examples/`.
