# before-you-sign — copy-paste prompt

Follow `skills/before-you-sign/SKILL.md` exactly. Remind yourself to run `select_app` if an Alchemy app is not already selected. Produce the full report template below (same headings as the skill), including **Tools used** with exact MCP tool names in call order. Use only allowed tools. Never send, sign, or broadcast.

Hosted MCP has no `resolveEnsName` — use `ethCall`.
Free tier — no SPAM filters, no traces.

Required report headings:

```markdown
# Before you sign
- Input
- Network
- One-sentence summary
- Asset changes (table: asset, from, to, amount, direction)
- Risk flags (bullets, or "none observed")
- Gas / fee snapshot
- Verdict: OK | REVIEW | DO NOT SIGN
- Tools used (exact MCP names, in order)
- Gaps
```

Input 1 is required. Inputs 2 and 3 are optional until you supply a real mined tx hash or unsigned calldata. Placeholders are not live data — do not claim they are confirmed onchain facts.

## Input 1 — ENS (required)

`vitalik.eth`

## Input 2 — mined tx hash (optional; placeholder until you supply a real hash)

`PLACEHOLDER_TX_HASH_0xREPLACE_ME`

## Input 3 — unlimited-approve calldata (optional; placeholder until you supply real calldata)

`PLACEHOLDER_UNLIMITED_APPROVE_CALLDATA`
