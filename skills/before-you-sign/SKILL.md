---
name: before-you-sign
description: Pre-sign onchain briefing via Alchemy MCP - balances, sims, approvals, and mined-tx inspection when the user says before I sign, simulate this tx, check this approval, inspect this hash, or wants an ENS briefing
metadata:
  version: "0.1.0"
  type: workflow
---

# before-you-sign

Run a read-only, pre-sign briefing with Alchemy MCP tools. Never send, sign, or broadcast. Heuristics are not advice or an audit.

## Allowed tools only

**Admin:** `ping`, `list_apps`, `get_app`, `select_app`, `list_chains`

**Resolve / account:** `ethGetBalance`, `ethGetCode`, `ethCall`

**Tx read:** `ethGetTransactionByHash`, `ethGetTransactionReceipt`, `getAssetTransfers`

**Sim / fee / debug:** `simulateAssetChanges`, `simulateExecution`, `ethEstimateGas`, `ethGasPrice`, `ethMaxPriorityFeePerGas`, `ethCall`, `traceTransaction`, `debugTraceTransaction`

**Tokens / risk:** `getTokenBalancesByAddress`, `getTokensByAddress`, `getTokenAllowance`, `getTokenMetadata`, `isSpamContract`

**NFTs:** `getNFTsForOwner`, `getNFTsByAddress`

Do not invent tool names outside this list.

## Workflow

1. If Alchemy MCP is missing, point the user to `SETUP.md` and stop.
2. If no app is selected, run `select_app` (or ask the user to pick one).
3. Detect input type:
   - ENS name or address
   - Mined transaction hash
   - Unsigned call / calldata / pending approval
4. If the input is an ENS name, resolve it with `ethCall` (ENS Registry `resolver(bytes32)` then resolver `addr(bytes32)`). Hosted MCP has no `resolveEnsName`. If resolve fails, stop and Gaps — do not invent the address.
5. Default network is `eth-mainnet`. Always print the network in the report. Use `list_chains` if the user names another chain.
6. Always `ethGetCode` on the subject address (EIP-7702 check).

## Plan-aware calls

Default profile is **Free**.

- `getNFTsForOwner` / `getNFTsByAddress`: `pageSize` 5, `withMetadata` false. NEVER pass `excludeFilters`, `includeFilters`, or `spamConfidenceLevel` (PAYG — 400 on Free).
- `isSpamContract`: optional, **NFT contracts only**, at most 1–2 calls. If 400 payg / upgrade / billing: Gaps ("paid plan required"), skip, do not retry.
- `getTokenBalancesByAddress` / `getTokensByAddress` / NFT lists / `getAssetTransfers`: **paginated**, often **address-sorted**. Label results **"page 1 only"**. Page 1 for a famous ENS (e.g. `vitalik.eth`) is often vanity-spam first, not the real portfolio. Do not treat a missing token (e.g. USDC) as "wallet has no USDC".
- Do not call Trace API / Debug API (`traceTransaction`, `debugTrace*`) in the default path. PAYG only if the user explicitly says so.
- If a tool returns **400** mentioning payg / upgrade / billing: do not retry with the same params; record tool + "paid plan required" in Gaps; continue. A paid-filter 400 that you retried **without** the filter is **not** a verdict reason.

## ENS resolve (no `resolveEnsName`)

Hosted MCP does **not** expose `resolveEnsName`. Resolve with `ethCall`:

1. ENS Registry `resolver(bytes32 namehash)`
2. That resolver's `addr(bytes32 namehash)`

If either call fails, stop. Put the failure in Gaps. Do not invent the address.

## EIP-7702

- Always `ethGetCode` on the subject address.
- If bytecode starts with `0xef0100`, flag **REVIEW**: account is delegated; signatures are interpreted by the delegate, not a plain EOA. Decode and report the delegate address from the delegation designator.
- Optionally `ethGetCode` the delegate. Do not invent the vendor.

### Branch A — address or ENS

- Resolve ENS via `ethCall` as above, or use the given address
- Required: `select_app`, resolved/given address, `ethGetBalance`
- Always `ethGetCode` (EIP-7702); `0xef0100` → REVIEW + decode delegate
- `getTokenBalancesByAddress` / `getTokensByAddress` (**page 1 only**, address-sorted; missing token ≠ absent)
- `getTokenMetadata` when needed to spot counterfeit symbols (e.g. fake POL)
- `getAssetTransfers` — max **4 inbound + 4 outbound**, label **"page 1 only"**. If `from`/`to` is a well-known address, cross-check `ethGetTransactionReceipt` / Swap logs before attributing the tx — forged `Transfer.from` can fake an outbound from a famous wallet
- `getNFTsForOwner` or `getNFTsByAddress`: `pageSize` 5, `withMetadata` false; no spam filters; note results may include spam; **page 1 only**
- `isSpamContract` optional, NFT contracts only, ≤1–2; on 400 payg → Gaps and skip
- Address-briefing table: **max 6 rows** = native ETH + up to 3 defensible tokens + NFT count. Prefer canonical name/symbol + non-vanity address. Vanity `0x0000…` dust → Risk flags as a pattern, not holdings line items
- Flag unlimited approvals only when transfer data supports that conclusion

### Branch B — unsigned call

- **Required:** `simulateAssetChanges` and/or `simulateExecution` (work on Free; this is the point of Branch B)
- `ethEstimateGas` / `ethGasPrice` / `ethMaxPriorityFeePerGas` when useful
- `ethGetCode` on `to` (and on the subject/`from` for EIP-7702)
- `isSpamContract` optional, NFT contracts only, ≤1–2; on 400 payg → Gaps and skip
- `getTokenAllowance` only for `approve` / `increaseAllowance`
- NEVER send, sign, or broadcast

### Branch C — mined hash

- `ethGetTransactionByHash`, `ethGetTransactionReceipt`
- `getAssetTransfers` for that tx if possible
- Receipt + transfers only on Free. Traces are PAYG — do not call `traceTransaction` / `debugTrace*` unless the user said PAYG / Enterprise
- Plain-language summary of what happened

## Report template (exact headings)

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

## Verdicts

- **DO NOT SIGN** — spam contract, simulation reverts, unlimited approve to unknown spender, drain pattern
- **REVIEW** — missing simulation, unverified contract, surprising recipient, high gas, EIP-7702 delegation, or a **required** tool failed and was not recovered
- **OK** — simulation ok (when applicable), consistent counterparties, no spam / unlimited flags on Free-safe evidence

### Tool failure and verdict

- Put every failure in Gaps.
- Set **REVIEW** only if a **required** tool failed **and** was not recovered.
- Required on Branch A: `select_app`, ENS resolve (via `ethCall`) or given address, `ethGetBalance`.
- Required on Branch B: `select_app`, plus `simulateAssetChanges` and/or `simulateExecution`.
- Everything else degrades to Gaps only.
- A paid-filter 400 that you retried without the filter (or skipped) is **not** a verdict reason. Do not upgrade to **DO NOT SIGN** just because a paid filter failed.

These heuristics are not financial advice and not a security audit.
