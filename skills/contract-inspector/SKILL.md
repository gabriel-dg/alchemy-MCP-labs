---
name: contract-inspector
description: Read-only identity check for any EVM address via Alchemy MCP. Use when the user asks who is this address, what is this contract, is this spender legit, is this token real, inspect this contract, or after a before-you-sign REVIEW names an unknown address. Returns type, identity, trust signals, red flags, and an ESTABLISHED / UNCERTAIN / RED FLAGS / NOT A CONTRACT assessment.
metadata:
  version: "0.1.3"
  type: workflow
---

# contract-inspector

Answer "what is this address?" using only Free-tier Alchemy MCP tools. Never send, sign, or broadcast. Signals are heuristics, not an audit.

## Allowed tools

**Admin:** `ping`, `list_apps`, `select_app`, `list_chains`

**Chain reads:** `ethBlockNumber`, `ethGetCode`, `ethGetStorageAt`, `ethGetBalance`, `ethGetTransactionCount`, `ethGetLogs`, `ethCall`

**ABI probe:** `simulateExecution`

**Tokens:** `getTokenMetadata`, `getTokenPricesByAddress`, `getAssetTransfers`

**NFTs:** `getContractMetadata`, `isSpamContract`

Do not invent tool names outside this list. Local arithmetic (hex to decimal, wei to ETH, block minus 4) is fine and is not a tool call.

## Call conventions

- **Parallel is fine.** Independent calls may be issued together. Where a step says "in this order, stop at the first hit", you may issue all of them at once and interpret the results in the listed order. Keep batches to about 4 calls; larger batches trigger 429 rate limits on a Free app. On a 429, retry that call once and list it as `×2` under Tools used.
- **Truncation.** The server truncates large responses with a "response too large" note. A truncated non-empty `ethGetCode` still means "has code"; do not retry. A truncated `ethGetLogs` means "10+ events, busy" and counts as activity.
- **Hex only.** Block numbers and values are hex strings. Compute `latest minus 4` yourself. The `ethBlockNumber` snapshot is "now" for the report even if the chain advances during the run.
- **Never paste bytecode** into the report. Observations from reading bytecode (strings, selectors, hard-coded returns) are allowed as a labelled heuristic ("inferred from bytecode"), never as identity.
- Report the address as given. No checksum conversion.

## Workflow

1. If the Alchemy MCP server is missing, point the user to `SETUP.md` and stop.
2. If no app is selected, call `list_apps` then `select_app`. If the user named an app, select it. Otherwise, if several apps exist, ask which one.
3. Network defaults to `eth-mainnet`. If the user names another chain, confirm the id with `list_chains`. Print the network in the report.
4. Run the steps below. Stop early where the step says so.
5. Write the report using the template at the end.

## Step 1: what kind of address

`ethGetCode` on the address.

- `0x`: **not a contract**. Call `ethGetTransactionCount` (nonce) and `ethGetBalance`. Nonce 0 means the address has never sent a transaction. If the balance is above zero, call `getAssetTransfers` with `toAddress`, `category` `["external","erc20"]`, `order` `"asc"`, `maxCount` `"0x1"`, `withMetadata` `true` to date the first inbound transfer. Write the report and stop. Do not run the other steps.
- 23 bytes starting `0xef0100`: **delegated EOA** (EIP-7702). The delegate is bytes 3 to 23. Report the type, then run Steps 2 to 5 against the **delegate** address, and say so in every heading.
- Anything else: **contract**. Continue.

## Step 2: proxy check

`ethGetStorageAt` on the address for these slots. The first non-zero value wins; its last 20 bytes are the implementation.

| Slot | Standard |
|------|----------|
| `0x360894a13ba1a3210667c828492db98dca3e2076cc3735a920a3ca505d382bbc` | EIP-1967 implementation |
| `0x7050c9e0f4ca769c69bd3a8ef740bc37934f8e2c036e5a723fd8ee048ed3f8c3` | legacy ZeppelinOS implementation (used by USDC) |
| `0xa3f0ad74e5423aebfd80d3ef4346578335a9a72aeaee59ff6cb3582b35133d50` | EIP-1967 beacon |

If an implementation is found, `ethGetCode` on it to confirm it has code, and name the slot that matched in the Type line. All three zero means "no proxy detected", which is not proof; some proxies use other patterns.

## Step 3: verified-source probe

Alchemy decodes simulated calls with the Etherscan ABI when the contract is verified. Use that as a Free-tier "verified source" signal.

`simulateExecution` from `0x1234567890abcdef1234567890abcdef12345678`, `value` `"0x0"`, to the address, with these selectors. The first response where any entry in `calls` has a `decoded` block with `authority` `"ETHERSCAN"` is a match.

1. `0x06fdde03` `name()`
2. `0x8da5cb5b` `owner()`
3. `0x3644e515` `DOMAIN_SEPARATOR()`

Interpret the three outcomes:

- A `decoded` block → **verified (ABI matched on `<method>`)**. If the decode sits on a `DELEGATECALL`, it belongs to the implementation; report both addresses.
- A successful return with data but no `decoded` block → the function exists and Etherscan has no ABI for it → **unverified**.
- A successful return with empty output `0x` and no `decoded` block → a fallback swallowed the call; the function most likely does not exist. Not a match.
- All three revert or return empty with no `decoded` block → **no ABI match on 3 probes**: unverified, or none of these functions exist. Say "could not confirm".

## Step 4: identity

`getTokenMetadata`. If it returns name, symbol, decimals: the address is an **ERC-20**. This is the name to report; if the Step 3 decode returned a different `name()` string, mention it in parentheses. Record whether `logo` is present; Alchemy curates logos, so a logo is a legitimacy signal. Then:

- `getTokenPricesByAddress` with `["<network>:<address>"]`. A price means a market and an index listing. "Price not found" is normal for new or tiny tokens and a red flag when combined with a well-known symbol.
- **Counterfeit check.** Compare symbol and name against this table and add a `counterfeit check` row to the signals. A well-known symbol at a different address, or a symbol naming a native asset (`ETH`, `BNB`, `MATIC`, `POL`, `SOL`, `AVAX`) on an ERC-20, is a red flag. Unicode lookalikes count as the symbol they imitate.

| Symbol | Canonical eth-mainnet address |
|--------|-------------------------------|
| USDC | `0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48` |
| USDT | `0xdAC17F958D2ee523a2206206994597C13D831ec7` |
| WETH | `0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2` |
| DAI | `0x6B175474E89094C44Da98b954EedeAC495271d0F` |
| WBTC | `0x2260FAC5E5542a773Aa44fBCfeDf7C193bc2C599` |
| LINK | `0x514910771AF9Ca656af840dff83E8264EcF986CA` |
| UNI | `0x1f9840a85d5aF5bf1D1762F925BDADdC4201F984` |

On other networks skip the address comparison and keep the native-asset rule.

If `getTokenMetadata` errors with "expected a valid token contract address", or returns empty name and symbol with null decimals: not an ERC-20. Call `getContractMetadata`. If `tokenType` is `ERC721` or `ERC1155`: **NFT contract**. Record `isSpam`, `spamClassifications`, and `openSeaMetadata.safelistRequestStatus`, then call `isSpamContract` once. Otherwise: **non-token contract**; still report `isSpam` and `spamClassifications` if they are set. Non-token contracts stay unnamed on Free; `getContractMetadata` may still return a name from OpenSea ingestion, and bytecode strings or recognisable event topics may hint at what it is. Report such hints as "inferred", never as identity. **Do not name a vendor, product, or company unless you verified it** — a bytecode pattern that resembles a known implementation is not verification, and a hedged brand name is still the name a reader will remember. Describe the shape ("a smart-account implementation with session-key privileges") and leave the attribution to a block explorer.

## Step 5: age and activity

**Age.** `getAssetTransfers` with `order` `"asc"`, `maxCount` `"0x1"`, `withMetadata` `true`:

- ERC-20 or NFT: `contractAddresses` `[address]`, `category` `["erc20"]` (or `["erc721","erc1155"]`). The first transfer's timestamp is the token's first activity.
- Non-token contract: `toAddress` address, `category` `["external","erc20"]`. The first inbound transfer is a lower bound on age, not a deployment date. Empty result: age unknown on Free.

**Last activity.** Same call with `order` `"desc"`. For tokens, this is the last transfer and a strong signal. For non-token contracts inbound transfers are usually accidental dust and a weak signal; also run the `desc` query with `fromAddress` to catch contracts that only send (smart wallets do), and prefer the log window below.

**Right now.** `ethBlockNumber`, then `ethGetLogs` with `address`, `fromBlock` latest minus 4, `toBlock` latest, both as numeric hex (5 blocks, about one minute). Free tier allows at most a 10-block range. Report the count of events, or "10+ events, busy" if truncated. Zero events in 5 blocks is normal for most contracts; it only matters combined with the other signals.

**Burst pattern.** Tokens only, on their own transfer history: first and last transfer within 24 hours of each other and zero events in the 5-block window. It counts no matter how long ago the burst was. A non-token contract that once received someone else's airdrop is not a burst.

**Balance.** `ethGetBalance`, reported in ETH to 4 decimals. Contracts holding ETH is neither good nor bad.

**Implementation contracts are a blind spot.** This applies whenever the address looks like code that runs in someone else's context rather than its own: an EIP-7702 delegate target, a proxy implementation, a smart-account or library contract. You do not need to have been told; the shape is enough, and Step 4's bytecode hints (`validateUserOp`, `isValidSignature`, receiver hooks, a privilege or nonce map) are the usual tell. Such a contract executes under `DELEGATECALL`, so its logs, transfers and balance appear at the calling account's address and never at this one. Zero events, zero balance and a lone unsolicited airdrop as the only inbound transfer are exactly what a correctly functioning implementation looks like. Say so in the report and give these signals no weight in either direction. They must not push the assessment toward RED FLAGS. This exception is about *quiet* signals only: it never softens a counterfeit symbol, a spam classification, or a burst pattern, and it does not apply to tokens.

## Assessment

State today's date once in the report (an `Observed` line) so "days ago" figures can be checked later. Pick exactly one assessment, in this order of precedence:

- **NOT A CONTRACT**: code is `0x`. Nothing but a private key stands behind it. If the user was about to approve or send to it, say that no contract logic constrains what the holder can do.
- **RED FLAGS**: any of: counterfeit or native-asset symbol; `isSpam` true or non-empty `spamClassifications`; ERC-20 with no price, no logo, no ABI match, and first activity under 90 days ago; burst pattern.
- **ESTABLISHED**: ABI matched, and at least one of: price feed, logo, OpenSea safelist `verified`, first activity over one year ago; and some activity: events in the 5-block window (including "10+, busy"), or for tokens a transfer in the last 30 days.
- **UNCERTAIN**: everything else. Say which signals are missing.

For a delegated EOA, the assessment describes the delegate. Prefix it: "delegate is …".

## Report template

Bold labels as bullets, a Markdown table for signals, bare tool names. Omit signal rows that do not apply (for example token rows on a non-token contract, or everything past balance on a non-contract). Repeated calls may be listed once with a count.

```markdown
# Contract inspector
- **Address:** as given
- **Network:** id
- **Observed:** date
- **Type:** not a contract | delegated EOA → delegate | contract | proxy (slot) → implementation
- **Identity:** ERC-20 name / symbol / decimals, NFT name / type, or "non-token contract" plus any inferred hint
- **Signals** (table: signal, result)
  has code · proxy · verified source · token metadata · logo · price feed · counterfeit check · NFT spam flags · first activity · last activity · events in last 5 blocks · ETH balance · nonce
- **Red flags:** bullets, or "none observed"
- **Assessment:** ESTABLISHED | UNCERTAIN | RED FLAGS | NOT A CONTRACT, with a one-line reason
- **What to do next:** one or two sentences for the reader
- **Tools used (in order):** bare MCP names, counts for repeats
- **Gaps:** what was not checked and why
```

These heuristics are not financial advice and not a security audit. "Established" means widely used and verifiable, not safe.
