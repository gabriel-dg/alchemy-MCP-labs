---
name: solana-wallet-brief
description: Read-only Solana wallet or transaction brief via Alchemy MCP. Use when the user gives a Solana address or transaction signature and asks what does this Solana wallet hold, how much SOL is here in dollars, what did this wallet do last, what did this Solana transaction do, who signed it, show its NFTs or compressed NFTs, or wants a Solana briefing. Returns SOL and token balances with USD values, recent activity, one transaction decoded into plain English, assets via the Digital Asset Standard, and a coverage note.
metadata:
  version: "0.1.0"
  type: workflow
---

# solana-wallet-brief

Answer "what does this Solana address hold, what did it do last, and what does it own?" on the Free tier, with the same report shape as `multichain-brief` on a different virtual machine. Also answers "what did this transaction do?" from a signature. Never send, sign, broadcast, or request an airdrop. Prices are indicative, not a valuation.

## Allowed tools

**Admin:** `ping`, `list_apps`, `select_app`, `list_chains`

**Cluster:** `solana_getEpochInfo`, `solana_getPriorityFeeEstimate` (mainnet only)

**Account reads:** `solana_getAccountInfo`, `solana_getTokenAccountsByOwner`

**History:** `solana_getSignaturesForAddress`, `solana_getTransaction`

**Holdings with names and prices:** `getTokensByAddress` (mainnet only; the same Portfolio API as Lab 3, with `networks: ["solana-mainnet"]`)

**Prices:** `getTokenPricesByAddress`, `getTokenPricesBySymbol`

**Assets (DAS):** `solana_getAssetsByOwner`, `solana_getAssetProof`

Do not invent tool names outside this list. `solana_requestAirdrop`, `solana_simulateTransaction` and `solana_simulateBundle` exist on the server and are not used: the labs stay read-only, and simulation needs a serialized transaction the user does not have. Local arithmetic (lamports to SOL, `amount` / 10^decimals, balance times price, post minus pre) is fine and is not a tool call.

## Call conventions

- **Network ids.** `solana-mainnet` (default) and `solana-devnet`. Every `solana_*` tool takes `network`. Print it in the report. Solana has no ENS: the input is always a base58 address or a base58 signature.
- **Input type.** A signature is 86 to 88 base58 characters. An address is 32 to 44. Nothing else is accepted; do not guess.
- **Parallel is fine.** Independent calls may be issued together. Keep batches to about 4 calls; larger batches trigger 429 rate limits on a Free app. On a 429, retry that call once and list it as `x2` under Tools used.
- **Responses truncate at roughly 8 KB.** Solana JSON is verbose. Use these limits and never raise them: `getTokensByAddress` `limit` 10; `solana_getAssetsByOwner` `limit` 3 with `page`; `solana_getSignaturesForAddress` `limit` 5; `solana_getTokenAccountsByOwner` with a `mint` filter on mainnet, and only by `programId` on devnet, where truncation is accepted and reported. A truncated response ends with a "response too large" note. Report what came back as a lower bound, say it was truncated, and do not retry with the same parameters.
- **Units.** 1 SOL = 1,000,000,000 lamports. Token amounts: use `uiAmountString`, or `amount` / 10^`decimals`. `tokenBalanceDecimal` in `getTokensByAddress` is in the token's smallest unit; `tokenBalanceFormatted` is already scaled.
- **Errors that end a step.** `-32001 Unable to complete request` on a DAS tool, `400 Unsupported network`, `-32601 Method not found`, and any 400 mentioning payg, upgrade, or billing. Record under Gaps and continue. Do not retry.
- **Network not enabled.** A 403 that says the app does not support the network carries a dashboard link. Show the link, stop, and tell the user to enable Solana on the app or select another.
- Report the address as given. No case changes; base58 is case-sensitive.

## Workflow

1. If the Alchemy MCP server is missing, point the user to `SETUP.md` and stop.
2. If no app is selected, call `list_apps` then `select_app`. If the user named an app, select it. Otherwise, if several apps exist, ask which one.
3. Network defaults to `solana-mainnet`. A `Network:` line overrides it. Only the two ids above are valid.
4. Classify the input. An address runs Steps 1 to 7 and uses the wallet template. A signature runs Step 1 and Step 5 only and uses the transaction template.
5. Write the report using the matching template at the end.

## Mainnet and devnet differ

| Capability | `solana-mainnet` | `solana-devnet` |
|------------|------------------|-----------------|
| Holdings with names and prices (`getTokensByAddress`) | Yes | No: `400 Unsupported network`. Use `solana_getTokenAccountsByOwner` by program, no prices |
| Prices | Yes | No. Test tokens have no price. Skip USD columns and the total |
| Priority fee estimate | Yes | No: `-32601 Method not found`. Skip |
| Assets via DAS | **Not on Free apps at the time of writing**: `-32001` on every DAS call. One call, then Gaps | Yes, including compressed NFTs and proofs |
| Account info, token accounts, signatures, transactions | Yes | Yes |

The same calls work against mainnet the moment the DAS gate opens for the app; the skill needs no change. Until then, the assets section runs on devnet.

## Known mints and programs

Symbols on Solana come from off-chain metadata. On mainnet `getTokensByAddress` supplies them; elsewhere the skill only names a mint it can verify against this table. Every mint here was checked on 2026-09-09: the price by mint matched the price by symbol.

| Symbol | Mint |
|--------|------|
| USDC | `EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v` |
| USDT | `Es9vMFrzaCERmJfrF4H2FYD4KCoNkY11McCe8BenwNYB` |
| wSOL | `So11111111111111111111111111111111111111112` |
| JUP | `JUPyiwrYJFskUPiHa7hkeR8VUtAeFoSYbKedZNsDvCN` |
| BONK | `DezXAZ8z7PnrnRJjz3wXBoRgixCa6xjnB7YaB1pPB263` |
| mSOL | `mSoLzYCxHdYgdzU16g5QSh3i5K3z3KZK7ytfqcJm7So` |
| JitoSOL | `J1toso1uCk3RLmjorhTtrVwY9HJ7X8V9yYac6Y7kGCPn` |
| RAY | `4k3Dyjzvzp8eMZWUXbBCjEvwSkkk59S5iCNLY3QrkX6R` |
| WIF | `EKpQGSJtjMFqKZ9KQanSqYXRcF8fBopzLHYxdM65zcjm` |

| Program | Address |
|---------|---------|
| System | `11111111111111111111111111111111` |
| Compute Budget | `ComputeBudget111111111111111111111111111111` |
| Token | `TokenkegQfeZyiNwAJbNbGKPFXCWuBvf9Ss623VQ5DA` |
| Token-2022 | `TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb` |
| Associated Token Account | `ATokenGPvbdGVxr1b2hvZbsiqW5xWH25efTNsLJA8knL` |
| Memo | `MemoSq4gqABAXKb96qnH8TysNcWxMyWCqXgDLGmfcHr` |
| Token Metadata (Metaplex) | `metaqbxxUerdq28cj1RbAWkYQm3ybzjb6a8bt518x1s` |
| Bubblegum (compressed NFTs) | `BGUMAp9Gq7iTEuizy4pqaxsTyUCBK68MDfK752saRPUY` |
| Jupiter v6 | `JUP6LkbZbjS1jKKwapdHNy74zcZ3tLUZoi5QNyVTaV4` |

Anything else is reported by address as "unknown program" or "mint …". Do not guess a name.

## Step 1: cluster pulse

`solana_getEpochInfo`. Report `absoluteSlot`, `epoch`, and progress = `slotIndex` / `slotsInEpoch` as a percent. This is the Solana equivalent of a block number and goes on the Observed line.

On mainnet, in the same batch, `solana_getPriorityFeeEstimate` with `accountKeys` = [the address] and `options` `{"includeAllPriorityFeeLevels": true}`. Report `medium` and `high` in microlamports per compute unit. The base fee is 5,000 lamports per signature on every network. On devnet skip this call.

## Step 2: account type

`solana_getAccountInfo` with the default `jsonParsed` encoding. Solana has no `ethGetCode`; every account has an `owner` program, and the owner says what the account is:

- `value` is `null`: the account has never been funded. Balance 0. Still run Step 4; it may have signatures from a closed history, usually none.
- `owner` is the System program and `executable` is false: a **wallet**. `lamports` / 10^9 is the SOL balance. This is the normal case.
- `executable` is true: a **program**. The brief still runs, but holdings are usually empty and `getTokensByAddress` may answer "Internal server error" for it; report that under Gaps rather than retrying.
- `owner` is Token or Token-2022 and the parsed `type` is `account`: a **token account**, not a wallet. Report its `mint` and its `owner` wallet, and suggest briefing the owner instead. Stop after Step 4.
- `owner` is Token or Token-2022 and the parsed `type` is `mint`: a **token mint**. Report `supply` / 10^`decimals` and the authorities. Stop after Step 4.
- Any other owner: a **program-owned account** (a PDA, a stake account, a vault). Name the owner program from the table or by address. The brief runs, but holdings usually belong to the program's logic, not the address.

`rentEpoch` of 18446744073709552000 means rent-exempt, which every live account is. Ignore it.

## Step 3: holdings

**Mainnet.** One call: `getTokensByAddress` with `address`, `networks: ["solana-mainnet"]`, `limit` 10. The response is one flat `tokens` array:

- The **native row** comes first, `tokenAddress` `null`, empty metadata, a `tokenPrices` entry. SOL = `tokenBalanceDecimal` / 10^9. USD = SOL x price. If `tokenPrices` is empty, call `getTokenPricesBySymbol` with `["SOL"]`.
- **Token rows** follow, sorted by mint address, not by value. This is **page 1 only**: the MCP tool exposes no `pageKey` parameter, so there is no page 2. It covers both token programs: a mint held under Token-2022 appears here like any other. The first page of a busy wallet is mostly airdropped spam. A token missing from page 1 is not evidence the wallet lacks it; use the watchlist in Step 3b.

Only priced holdings and Step 3b watchlist rows go in the holdings table. Every other row is counted in Noise and never listed as a holding. If page 1 has no priced token, write one line instead of a table: "No priced token on page 1. See Noise."

Classify every token row into exactly one bucket:

- **Priced holding:** `tokenBalanceDecimal` > 0 and `tokenPrices` non-empty. Goes in the holdings table with balance = `tokenBalanceFormatted`, USD = balance x price.
- **Counterfeit symbol:** the `symbol` or `name` claims a well-known asset (SOL, USDC, USDT, JUP, BONK, or any symbol in the table above) but the mint is not the table's mint. Name it in Noise as `counterfeit symbol`. Never count it, priced or not. Symbols are free text on Solana; only the mint identifies a token.
- **NFT-shaped:** `decimals` 0 and balance 1. The Portfolio API lists NFTs as tokens. Count them and say "see Assets". Do not price.
- **Zero balance:** `tokenBalanceDecimal` is `0`. An empty token account. Count only; see the rent note in Step 7.
- **Unpriced:** balance > 0, no price. Could be real and illiquid, or spam. Count, and name the symbol if it looks spam-shaped: a URL, "claim", "visit", "reward", an emoji, a `pump` suffix on the mint with a huge balance the wallet never bought.

If `getTokensByAddress` itself fails, fall back to `solana_getTokenAccountsByOwner` twice, once per `programId` (Token, then Token-2022), and price what you can name from the table with `getTokenPricesByAddress`. Say so under Gaps. That fallback truncates on busy wallets; report the count as a lower bound.

**Devnet.** `getTokensByAddress` answers `400 Unsupported network`. Do not call it. Call `solana_getTokenAccountsByOwner` twice, once per `programId`. Querying only the classic Token program **silently misses** every Token-2022 balance; both calls are mandatory. No prices exist: list up to 8 rows with mint, program, and balance, count the rest, and mark truncation as a lower bound. SOL comes from Step 2. No total.

### Step 3b: watchlist (optional)

If the prompt has `Tokens:` lines with mint addresses, at most 5, read each one directly. This is how the brief reaches past page 1.

For each mint, `solana_getTokenAccountsByOwner` with `owner` and `mint`. The `mint` filter finds accounts under both token programs. A wallet normally has one associated token account per mint; an exchange may have many. Sum `tokenAmount.uiAmountString` across every account returned; if the response truncates, report the sum as a lower bound and say so. Then one `getTokenPricesByAddress` with every `solana-mainnet:<mint>` pair (mainnet only). The symbol comes from the known-mints table; a mint not in the table is shown as `mint …` with no symbol, never a guessed one. Add the rows to the holdings table marked `watchlist`. A zero balance is still reported: it answers the question the user asked.

If the user asks about a token by symbol only, ask for the mint address or point to the wallet's tokens tab on a block explorer. Do not guess a mint.

## Step 4: recent activity

`solana_getSignaturesForAddress` with `limit` 5. Newest first. For each: `blockTime` as a UTC timestamp, the signature shortened to its first 8 and last 6 characters, `err` (`null` is success; anything else is a failed transaction that still paid a fee), and `memo`.

**Memos are the spam channel of Solana.** A memo with a URL, a "claim", a token pitch or social links is an advertisement attached to a dust transfer. Quote at most the first 60 characters and mark it `memo spam`. The wallet did not sign those.

An address that answers `[]` has no history on this network.

## Step 5: decode one transaction

For an address input, decode the newest signature from Step 4. For a signature input, decode that one. `solana_getTransaction` with `signature`, `maxSupportedTransactionVersion` 0, and the default `jsonParsed` encoding. If the response truncates before `preBalances`, retry once with `encoding` `json` (the same `meta`, smaller instructions) and list the call as `x2`. If it still truncates, report fee and status only and record the rest under Gaps.

Everything needed is in `meta` and `transaction.message.accountKeys`:

- **Status, fee, time, slot, compute.** `meta.err` (`null` is success), `meta.fee` in lamports, `blockTime`, `slot`, `meta.computeUnitsConsumed`.
- **Signers.** In `jsonParsed`, keys with `signer: true`. In `json`, the first `header.numRequiredSignatures` keys. The first key is the fee payer. For an address input, state plainly whether the briefed address signed. If it did not, the transaction was done *to* it, not *by* it.
- **SOL moves.** For every index i, delta = `postBalances[i]` - `preBalances[i]`. List the non-zero ones as SOL with sign, naming the account: the briefed address, a program from the table, or the address shortened. A key with `pre` 0 and `post` > 0 is a **new account**; the lamports are its rent deposit, paid by the fee payer. The fee payer's delta includes the fee. On mainnet with a SOL price from Step 3, add USD.
- **Token moves.** Pair `preTokenBalances` and `postTokenBalances` by `accountIndex`. Delta = post `uiTokenAmount.uiAmountString` - pre. A missing pre entry means the account was created in this transaction (pre 0); a missing post entry means it was closed (post 0). Report delta, the `owner` wallet, the `mint` (symbol from the table, else `mint …`), and the token `programId` when it is Token-2022.
- **Programs invoked.** From `meta.logMessages`, the lines matching `Program <address> invoke [1]`, deduplicated in order. Name them from the table or as `unknown program <address>`. Depth `[2]` and deeper are inner calls; skip them.
- **One sentence in plain words.** Who did what to whom: "`7Ua7…Qexe` created a token account for `86xC…2MMY`, paid its 0.00186 SOL rent, and moved 20,000,000 units of `mint 3T3m…pump` into it. The briefed wallet signed nothing." Write it from the moves above, never from the instruction data.

Do not attempt to decode instruction `data`. The balance deltas are the ground truth and they are already decoded.

## Step 6: assets (DAS)

`solana_getAssetsByOwner` with `ownerAddress`, `limit` 3, `page` 1. If a page returns 3 items, fetch the next page, up to 3 pages (9 assets). The `total` field echoes the `limit`, not the true count; never report it. Say "at least N" when the last page fetched was full.

If the first call answers `-32001 Unable to complete request`, the DAS API is closed for this app on this network. Write one line under Assets: "DAS unavailable on `solana-mainnet` for this app (-32001); run the same input with `Network: solana-devnet` to see this section, or against mainnet once the app has DAS access." No second call. Record under Gaps.

For each item: `content.metadata.name` (or "unnamed" if empty), `content.metadata.symbol`, the collection from `grouping` where `group_key` is `collection` (shortened), `compression.compressed`, `interface`, and `id`. If `ownership.delegated` is true, add `delegated to …`. An item with no name and no `json_uri` is reported as "unnamed asset" and nothing more is inferred from it.

For the **first compressed asset**, `solana_getAssetProof` with its `id`. Report `tree_id`, `node_index`, the number of hashes in `proof`, and `root`. That proof is what a program checks before it lets the asset move; the asset itself lives as a leaf in a Merkle tree, not as a token account, which is why it costs a fraction of a cent to mint.

## Step 7: total and coverage

Mainnet only. Total = SOL + priced page-1 tokens + watchlist. Show it with a coverage line that says exactly what is in it, for example "SOL plus 2 watchlist tokens. Page 1 held 9 token rows: 1 NFT-shaped, 8 unpriced, 0 counterfeit." Rows under $0.01 show as `<$0.01` and still count. Never present the total as a portfolio value: it is what the Free-tier calls could price.

**Rent note.** Every token account holds a rent deposit, 0.00203928 SOL for a standard 165-byte account and slightly more under Token-2022, refunded when the account is closed. Zero-balance rows in Step 3 are empty accounts whose deposit the wallet can reclaim. State the count and the deposit per account in Noise. Do not sum rent for non-empty accounts; those are in use.

Three things this brief does not cover: SOL in stake accounts, positions held inside DeFi programs, and NFT valuations. Say so under Gaps.

## Wallet template

Bold labels as bullets, Markdown tables for balances, bare tool names. Omit the watchlist rows if none were given. On devnet, omit prices, USD and Total, and say why on the Network line.

```markdown
# Solana wallet brief
- **Input:** as given
- **Network:** solana-mainnet | solana-devnet, plus one line on what devnet skips
- **Observed:** date, slot, epoch and progress, price lastUpdatedAt
- **Account type:** wallet | program | token account of … | token mint | program-owned account (owner …)
- **SOL balance** (table: SOL, price, USD)
- **Token holdings** priced and watchlist rows only (table: token, mint, balance, price, USD, note) with `page 1` or `watchlist` in the note; if page 1 has no priced token, one line pointing to Noise
- **Total:** USD, with the coverage line
- **Recent activity** (table: when UTC, signature, status, memo)
- **Last transaction, decoded:** signature, when, status, fee, signers and whether the briefed address is one, SOL moves, token moves, programs invoked, one plain sentence
- **Assets:** table (name, collection, compressed, id) plus one proof line, or the DAS gap line
- **Fees now:** base 0.000005 SOL per signature; priority medium and high in microlamports per CU (mainnet)
- **Noise:** counts of NFT-shaped, zero-balance, unpriced and counterfeit-symbol rows, spam-shaped names quoted, memo spam count, rent per empty account
- **Tools used (in order):** bare MCP names, counts for repeats
- **Gaps:** page 1 only, DAS gate, truncations, stake and DeFi positions, anything that failed
```

## Transaction template

```markdown
# Solana transaction brief
- **Input:** the signature
- **Network:** as used
- **Observed:** date, current slot
- **Status:** success | failed (error), slot, block time UTC, fee in SOL, compute units
- **Signers:** addresses; fee payer first
- **SOL moves** (table: account, before, after, delta, note)
- **Token moves** (table: owner, mint, delta, program)
- **Programs invoked:** names in order
- **In plain words:** one sentence
- **Tools used (in order)**
- **Gaps**
```

Prices come from Alchemy's price feed and lag the market by minutes. This is not financial advice and not a valuation. Nothing in this skill signs, sends, or broadcasts.
