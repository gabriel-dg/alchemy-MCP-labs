# alchemy-mcp-labs

Educational labs that showcase the Alchemy MCP server. Humans read `labs/`, agents follow `skills/`.

## When the user asks to run a lab

- Lab 0 (connection check): follow `labs/00-hello-mcp/README.md`
- Lab 1 (before-you-sign): read `skills/before-you-sign/SKILL.md` and follow it exactly. Prompts are in `skills/before-you-sign/PROMPTS.md`. Reference runs are in `skills/before-you-sign/examples/`
- Lab 2 (contract-inspector): read `skills/contract-inspector/SKILL.md` and follow it exactly. Same layout for prompts and examples
- Lab 3 (multichain-brief): read `skills/multichain-brief/SKILL.md` and follow it exactly. Same layout. This lab queries five networks by default; the network list is printed in the report

## Rules for every lab

- Read-only. Never sign, send, or broadcast a transaction. Never call `create_app`, `update_app`, or any webhook or gas-policy tool.
- Call `list_apps` and `select_app` before any RPC or data tool. If several apps exist, ask the user which one.
- Default network is `eth-mainnet`. Print the network in every report.
- Assume the Free tier. If a tool returns 400 mentioning payg, upgrade, or billing, do not retry with the same parameters. Record it under Gaps and continue.
- Use only tool names the server actually exposes. Do not invent tools. If the server offers an ENS resolution tool, use it; otherwise resolve ENS with `web3Sha3` plus `ethCall` as described in the skill.
- If the Alchemy MCP server is not connected, point the user to `SETUP.md` and stop.

## Repo layout

- `labs/` walkthroughs for humans
- `skills/<name>/SKILL.md` the playbook, `PROMPTS.md` copy-paste prompts, `examples/` reference runs
- `docs/how-it-works.md` concepts, glossary, tool map
- `.claude/skills/` thin pointers so Claude Code can invoke a skill by name
- `.claude/settings.json` pre-approves the read-only Alchemy tools the labs use, so a run needs no permission clicks
- `AGENTS.md` the same rules for Cursor, Codex, and other agents
