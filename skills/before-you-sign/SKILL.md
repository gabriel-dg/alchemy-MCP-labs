---
name: before-you-sign
description: Read-only pre-sign briefing via Alchemy MCP. Use when the user says before I sign, simulate this transaction, check this approval, inspect this hash, is this calldata safe, or wants a wallet or ENS briefing. Returns asset changes, risk flags, and an OK / REVIEW / DO NOT SIGN verdict.
metadata:
  version: "0.3.0"
  type: workflow
---

# before-you-sign

Produce a read-only briefing with Alchemy MCP tools. Never send, sign, or broadcast. Heuristics are not advice or an audit.

## Allowed tools

**Admin:** `ping`, `list_apps`, `get_app`, `select_app`, `list_chains`

**Hashing:** `web3Sha3` (keccak256, needed for ENS namehash)

**Account reads:** `ethGetBalance`, `ethGetCode`, `ethCall`

**Transaction reads:** `ethGetTransactionByHash`, `ethGetTransactionReceipt`, `getAssetTransfers`

**Simulation and fees:** `simulateAssetChanges`, `simulateExecution`, `ethEstimateGas`, `ethGasPrice`, `ethMaxPriorityFeePerGas`

**Paid, only if the user says PAYG or Enterprise:** `traceTransaction`, `debugTraceTransaction`

**Tokens and risk:** `getTokenBalancesByAddress`, `getTokensByAddress`, `getTokenAllowance`, `getTokenMetadata`, `isSpamContract`

**NFTs:** `getNFTsForOwner`, `getNFTsByAddress`

Do not invent tool names outside this list. There is no `resolveEnsName` on the hosted server. Independent calls may run in parallel; list them in the report in the order they were issued.

## Workflow

1. If the Alchemy MCP server is missing, point the user to `SETUP.md` and stop.
2. If no app is selected, call `list_apps` then `select_app`. If the user named an app, select it. Otherwise, if several apps exist, ask which one.
3. Network defaults to `eth-mainnet`. If the user names another chain, confirm the id with `list_chains`. Print the network in the report.
4. Detect the input type and take the matching branch:
   - ENS name or address → Branch A
   - `to` plus hex calldata, or a described unsigned call → Branch B
   - transaction hash of a mined transaction → Branch C
5. Write the report using the template at the end.

## Parameter conventions

- Hex everywhere for JSON-RPC values. `Value: 0` becomes `"0x0"`. An ETH amount becomes wei in hex.
- `getAssetTransfers`: `category` `["external","erc20","erc721","erc1155"]`, `order` `"desc"`, `maxCount` `"0x4"`, `withMetadata` `true`. Run once with `toAddress` (inbound) and once with `fromAddress` (outbound).
- `getTokenBalancesByAddress` and `getTokensByAddress` are multi-chain tools: pass `networks` `["<network id>"]` and always pass `limit` 10. Without a limit the response is truncated. Prefer `getTokenBalancesByAddress`.
- `getNFTsForOwner` / `getNFTsByAddress`: `pageSize` 5, `withMetadata` false, no filter parameters.
- `ethGetCode` results may be truncated by the server for large contracts. Non-empty bytes still mean "has code". Note the truncation under Gaps.
- Convert `blockTimestamp` to a UTC date in the report.

## ENS resolution recipe

Hosted MCP has no ENS tool. Resolve with `web3Sha3` for hashing and `ethCall` for the registry. Never guess a hash or an address.

The `.eth` parent node is a constant and may be used directly without recomputing:

```
node_eth = 0x93cdeb708b7545dc668eb9280176169d1c33cfd8ed6f04690a0bcc88a93fc4ae
```

For `label.eth`, lower-cased:

1. `h_label = web3Sha3(data = "0x" + hex(label))`. For `vitalik` the data is `0x766974616c696b`.
2. `node = web3Sha3(data = "0x" + node_eth_without_0x + h_label_without_0x)`. That is one hex string of 128 characters after the `0x`. Example for `vitalik`: data `0x93cdeb708b7545dc668eb9280176169d1c33cfd8ed6f04690a0bcc88a93fc4aeaf2caa1c2ca1d027f1ac823b529d0a67cd144264b2789fa2ea4d63a67c7103cc`.
3. `ethCall` to the registry `0x00000000000C2E074eC69A0dFb2997BA6C7d2e1e` with `data` `0x0178b8bf` + node (without `0x`). The resolver is the last 20 bytes of the result.
4. `ethCall` to that resolver with `data` `0x3b3b57de` + node. The address is the last 20 bytes of the result.

For names with more labels (`a.b.eth`) apply steps 1 and 2 per label from right to left, feeding each node into the next.

Sanity check: `vitalik.eth` has node `0xee6c4522aab0003e8d14cd40a6af439055fd2577951148c14b6cea9a53475835` and resolves to `0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045`.

If any step fails, stop, record it under Gaps, and set REVIEW. Do not continue with an invented address.

## EIP-7702

`ethGetCode` on a delegated account returns 23 bytes: `0xef0100` followed by the 20-byte delegate address. When you see that prefix, report the delegate address (bytes 3 to 23) and optionally `ethGetCode` the delegate to confirm it is a contract. Do not name a vendor unless you verified it.

What it means for the verdict depends on the branch:

- Branch A (the wallet itself): **REVIEW**. Signatures from this account execute through the delegate.
- Branch B (`from`): **REVIEW**, for the same reason. The simulation still runs.
- Branch C (sender of a mined transaction): informational only. The transaction already executed. Mention it under Risk flags, do not change the verdict for it.

## Plan awareness

Default profile is **Free**.

- Never pass `excludeFilters`, `includeFilters`, or `spamConfidenceLevel` to NFT tools on Free. They 400.
- `isSpamContract`: optional, NFT contracts only, at most 2 calls. Pick from the NFT page 1: one contract the page does not flag but that looks suspicious, and one it does flag. If the result disagrees with the NFT's `spamClassifications` field, report both and do not reconcile.
- Token, NFT, and transfer lists are paginated and often address-sorted. Label results **"page 1 only"**. Page 1 for a busy wallet is usually vanity spam. A token missing from page 1 is not evidence the wallet lacks it.
- `traceTransaction` / `debugTraceTransaction`: only if the user explicitly said PAYG or Enterprise.
- Any 400 mentioning payg, upgrade, or billing: do not retry with the same parameters, record tool and "paid plan required" under Gaps, continue. A paid-filter failure is never a verdict reason.

## Branch A: address or ENS

- Resolve ENS with the recipe above, or use the given address.
- Required: `select_app`, the address, `ethGetBalance`, `ethGetCode`.
- `getTokenBalancesByAddress` (limit 10), page 1 only. `getTokenMetadata` on one or two tokens: one that looks counterfeit (a well-known symbol from an unknown contract) and, if present, one that looks real, so the reader sees the difference.
- `getAssetTransfers` inbound and outbound with the conventions above. Transfers whose `from` is the subject but whose token comes from an unknown contract are forged spam. Say so rather than attributing them to the wallet's intent.
- `getNFTsForOwner`: pageSize 5, no metadata, no filters. Report the total count and note results may include spam.
- `isSpamContract`: optional, NFT contracts only, at most 2.
- Asset table: at most 6 rows. Native balance, up to 3 defensible tokens, NFT count. Direction values: `holding`, `in`, `out`. Vanity `0x0000…` dust goes under Risk flags as a pattern, not as holdings.
- Gas / fee snapshot: write "n/a (account briefing)".
- Airdropped spam tokens and forged transfers that target the wallet (lookalike "ETH" tokens, vanity dust, transfers to poisoning addresses) are noise, not evidence against the wallet. List them under Risk flags as a pattern. They are not a verdict reason.

## Branch B: unsigned call

- **Required:** `simulateAssetChanges` and `simulateExecution` with the given `from`, `to`, `value` (hex), and `data`. This is the point of the branch. A Branch B report without a `simulate*` call is invalid. The `from` address does not need a balance for the simulation to run.
- Take symbol and decimals from the simulation result. Call `getTokenMetadata` only if the simulation did not return them.
- `ethGetCode` on `to`, on `from` (EIP-7702), and on the spender if `data` is `approve` (`0x095ea7b3`) or `increaseAllowance` (decode the spender from the first argument).
- If `simulateExecution` shows a `DELEGATECALL` to an implementation, report the implementation address. Do not chase it further.
- `getTokenAllowance` for approvals, to report the current allowance.
- Fee snapshot: `ethEstimateGas` × `ethGasPrice`, shown in ETH, plus `ethMaxPriorityFeePerGas`. Also show the simulation's `gasUsed`.
- Asset table row for an approval: amount is the allowance, direction is `approval (no tokens move now)`.
- Spender classification for an unlimited approval:
  - spender code is `0x` → **DO NOT SIGN**. Nothing but a private key stands behind it.
  - spender has code but you cannot tie it to the protocol the user is interacting with → **REVIEW**.
  - spender has code and matches the protocol named in the user's context → note the unlimited amount under Risk flags; verdict per the general rules.
- Never send, sign, or broadcast.

## Branch C: mined hash

- Required: `select_app`, `ethGetTransactionByHash`, `ethGetTransactionReceipt`. Receipt `status` `0x1` is success, `0x0` is revert.
- Decode ERC-20 `Transfer` logs (topic `0xddf252ad…`) from the receipt: token is the log address, from and to are topics 1 and 2, amount is data. `getTokenMetadata` for decimals and symbol.
- `ethGetCode` on `to` and on any recipient decoded from the calldata. `ethGetCode` on the sender is optional and informational (see EIP-7702).
- `getAssetTransfers` is optional. Use it only if the receipt logs are not enough to explain the transaction.
- Fee is `gasUsed × effectiveGasPrice`, shown in ETH. Do not call `ethGasPrice` for a mined transaction.
- Direction is relative to the sender.
- Write a plain-language summary of what happened, with the block number and UTC date.
- Verdict on Branch C describes what happened, since signing is moot: **OK** if the transaction succeeded and the asset movements match the calldata's intent with no approvals or unexpected side effects; **REVIEW** if it reverted, granted an approval, or moved assets the calldata does not explain. DO NOT SIGN does not apply.

## Verdicts

- **DO NOT SIGN** (Branch B only): simulation reverts, unlimited approve to a code-less spender, spam contract, drain pattern (assets leave the wallet with nothing coming back).
- **REVIEW**: EIP-7702 delegation on the wallet or `from` (A and B), unlimited approve to a contract you cannot identify, unverified or surprising recipient in B, unusually high gas, missing simulation on Branch B, a Branch C revert or unexplained movement, or a required tool failed and was not recovered.
- **OK**: simulation succeeded where applicable, counterparties are consistent, no spam or unlimited flags on Free-safe evidence.

Required tools per branch: A needs `select_app`, the address, `ethGetBalance`, `ethGetCode`. B needs `select_app` and at least one `simulate*` call. C needs `select_app`, `ethGetTransactionByHash`, `ethGetTransactionReceipt`. Any other failure degrades to Gaps only and does not change the verdict.

## Report template

Use this shape. Bold labels as bullets, a Markdown table for asset changes, bare tool names without the `mcp__alchemy__` prefix. Repeated calls may be listed once with a count.

```markdown
# Before you sign
- **Input:** what was given, plus the resolved address for ENS
- **Network:** id
- **One-sentence summary:** plain language
- **Asset changes** (table: asset, from, to, amount, direction)
- **Risk flags:** bullets, or "none observed"
- **Gas / fee snapshot:** per branch
- **Verdict:** OK | REVIEW | DO NOT SIGN, with a one-line reason
- **Tools used (in order):** exact MCP names
- **Gaps:** what was not checked and why
```

These heuristics are not financial advice and not a security audit.
