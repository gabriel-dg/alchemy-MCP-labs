---
name: before-you-sign
description: Read-only pre-sign briefing via Alchemy MCP. Use when the user says before I sign, simulate this transaction, check this approval, inspect this hash, is this calldata safe, or wants a wallet or ENS briefing. Returns asset changes, risk flags, and an OK / REVIEW / DO NOT SIGN verdict.
---

This is the Claude Code entry point for the skill. The playbook lives at the repo root so other agents can use it too.

Read `skills/before-you-sign/SKILL.md` and follow it exactly. Treat the user's argument as the input: an ENS name or address (Branch A), a `to` plus calldata (Branch B), or a mined transaction hash (Branch C). Default network `eth-mainnet`. Select an Alchemy app first if none is selected. Never sign, send, or broadcast.

Ready-made inputs are in `skills/before-you-sign/PROMPTS.md`. Reference runs are in `skills/before-you-sign/examples/`.
