---
name: multichain-brief
description: Read-only multi-chain holdings brief via Alchemy MCP. Use when the user asks what does this wallet hold across chains, how much is this address worth, show balances on Ethereum and its L2s in dollars, what is this wallet's exposure, or wants a 7-day price change on what an address holds. Returns native and token balances per network with USD values, a total, a 7-day price move, and a coverage note.
metadata:
  version: "0.1.0"
  type: workflow
---

# multichain-brief

Answer "what does this address hold, on every chain, in dollars?" with one multi-chain call plus a price history call, all on the Free tier. Never send, sign, or broadcast. Prices are indicative, not a valuation.

## Allowed tools

**Admin:** `ping`, `list_apps`, `select_app`, `list_chains`

**Hashing:** `web3Sha3` (keccak256, needed for ENS namehash)

**Account reads:** `ethGetCode`, `ethCall`

**Multi-chain holdings:** `getTokensByAddress` (the one that matters), `getTokenBalancesByAddress` (fallback, no prices)

**Prices:** `getTokenPricesBySymbol`, `getTokenPricesByAddress`, `getHistoricalTokenPrices`

**Token identity:** `getTokenMetadata`

Do not invent tool names outside this list. If the server exposes an ENS resolution tool, you may use it; see the ENS section. Local arithmetic (hex to decimal, wei to ETH, balance times price, percent change) is fine and is not a tool call.

## Call conventions

- **Parallel is fine.** Independent calls may be issued together. Keep batches to about 4 calls; larger batches trigger 429 rate limits on a Free app. On a 429, retry that call once and list it as `x2` under Tools used.
- **Always pass `limit`** to `getTokensByAddress`. Without it the response is truncated. Use `limit` 12 for the five default networks (six native rows, six token rows). `limit` 15 was already truncated in testing. If a response still comes back with a "response too large" note, retry once with a smaller limit and list the call as `x2` under Tools used. Fewer networks leave room for more token rows: `limit` = networks + 7, never above 12.
- **Network ids.** The Polygon id is `matic-mainnet`. The tool accepts `polygon-mainnet` but echoes `matic-mainnet` in the response; use the canonical id from `list_chains` so the request and the report agree.
- **Unknown networks are dropped silently.** If a network you passed does not appear in the response, the tool does not support it or the app does not have it enabled. Report it under Gaps. Do not assume a zero balance.
- **Decimal field.** `tokenBalance` is hex; `tokenBalanceDecimal` is the same value in decimal. Use the decimal field. Native balances are 18-decimal on every default network.
- Report the address as given. No checksum conversion. Prices carry a `lastUpdatedAt`; quote the earliest one in the Observed line.

## Workflow

1. If the Alchemy MCP server is missing, point the user to `SETUP.md` and stop.
2. If no app is selected, call `list_apps` then `select_app`. If the user named an app, select it. Otherwise, if several apps exist, ask which one.
3. Networks default to the five in the table below. If the user gives a `Networks:` line, use that list instead and confirm each id with `list_chains`. Print the network list in the report.
4. Resolve the input (Step 1), then run Steps 2 to 6.
5. Write the report using the template at the end.

## Default networks and native symbols

| Network id | Native symbol | Price symbol |
|------------|---------------|--------------|
| `eth-mainnet` | ETH | ETH |
| `base-mainnet` | ETH | ETH |
| `arb-mainnet` | ETH | ETH |
| `opt-mainnet` | ETH | ETH |
| `matic-mainnet` | POL (the response metadata still says "Matic Token / MATIC") | POL |

If the user adds networks: `zksync-mainnet`, `linea-mainnet`, `scroll-mainnet`, `blast-mainnet`, `zora-mainnet`, `unichain-mainnet`, `ink-mainnet`, `worldchain-mainnet` are ETH-native; `bnb-mainnet` is BNB; `avax-mainnet` is AVAX; `gnosis-mainnet` is xDAI, priced as DAI. Anything else: say the native symbol is unknown to the skill and price it only if the response carries a `tokenPrices` entry for the native row.

## Step 1: resolve the input

An address is used as given. An ENS name is resolved onchain.

**Check the tool list first.** If your connection exposes a dedicated ENS resolution tool, use it and skip the recipe.

Otherwise, `web3Sha3` for the namehash and two `ethCall`s to the ENS registry on `eth-mainnet`. Never guess a hash or an address. The `.eth` parent node is a constant:

```
node_eth = 0x93cdeb708b7545dc668eb9280176169d1c33cfd8ed6f04690a0bcc88a93fc4ae
```

For `label.eth`, lower-cased:

1. `h_label = web3Sha3(data = "0x" + hex(label))`. For `vitalik` the data is `0x766974616c696b`.
2. `node = web3Sha3(data = "0x" + node_eth_without_0x + h_label_without_0x)`, one hex string of 128 characters after the `0x`.
3. `ethCall` to the registry `0x00000000000C2E074eC69A0dFb2997BA6C7d2e1e` with `data` `0x0178b8bf` + node (without `0x`). The resolver is the last 20 bytes of the result.
4. `ethCall` to that resolver with `data` `0x3b3b57de` + node. The address is the last 20 bytes of the result.

For `a.b.eth` apply steps 1 and 2 per label from right to left. Sanity check: `vitalik.eth` has node `0xee6c4522aab0003e8d14cd40a6af439055fd2577951148c14b6cea9a53475835` and resolves to `0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045`. If any step fails, stop and record it under Gaps. Do not continue with an invented address.

## Step 2: account type

`ethGetCode` on `eth-mainnet` only. `0x` is a plain account. 23 bytes starting `0xef0100` is an EIP-7702 delegated account; report the delegate (bytes 3 to 23) in one line and point to contract-inspector for what it is. Anything else is a contract; the brief is still valid, say so. Code on other networks is not checked; the same address can be a contract on one chain and empty on another.

## Step 3: holdings, one call

`getTokensByAddress` with `address`, `networks` (the list from the workflow), and `limit`. The response is one flat `tokens` array:

- **Native rows** come first, one per network, with `tokenAddress` `null`, empty metadata, and a `tokenPrices` entry. Balance is `tokenBalanceDecimal` / 10^18. USD is balance x price. On `matic-mainnet` the native appears twice: the `null` row and a second row at `0x0000000000000000000000000000000000001010` with the same balance. Count it once, from the `null` row.
- **ERC-20 rows** follow, sorted by contract address across all networks, not by value. This is **page 1 only**. The first page of a busy wallet is mostly vanity-address spam. A token missing from page 1 is not evidence the wallet lacks it; use the watchlist in Step 4 for tokens you care about.

Classify every ERC-20 row into exactly one bucket:

- **Priced holding:** `tokenBalanceDecimal` > 0 and `tokenPrices` non-empty. Goes in the holdings table with balance = `tokenBalanceFormatted`, USD = balance x price. Record whether `logo` is present. Alchemy curates logos, so a logo is a mild legitimacy signal, but its absence proves little: the canonical USDC on `base-mainnet` has none. A priced page-1 token without a logo gets a `no logo` note. A priced page-1 token with a well-known symbol (USDC, USDT, WETH, DAI, WBTC) is counted only after its address is checked: on `eth-mainnet` compare with the canonical table in `skills/contract-inspector/SKILL.md`; elsewhere add `verify with contract-inspector` to the note and count it, since the wallet did receive something priced at that address.
- **Zero balance:** `tokenBalanceDecimal` is `0`. The wallet touched it once. Count only.
- **Unpriced:** balance > 0, no price. Could be real and illiquid, or spam. Count, and name the symbol if it looks spam-shaped: a URL, "claim", "visit", "reward", an emoji, or `decimals` 0 with a vanity `0x0000...` address.

If a native row has an empty `tokenPrices`, call `getTokenPricesBySymbol` with that network's price symbol from the table. If `getTokensByAddress` itself fails, fall back to `getTokenBalancesByAddress` with the same parameters (balances, no prices) and price the natives with `getTokenPricesBySymbol`; say so under Gaps.

## Step 4: watchlist (optional)

If the prompt has `Tokens:` lines in the form `network:address`, at most 5, read each one directly. This is how the brief reaches past page 1.

For each entry, in one batch: `ethCall` to the token with `data` `0x70a08231` + the wallet address left-padded to 32 bytes (that is `balanceOf(address)`), and `getTokenMetadata` for symbol and decimals. Then one `getTokenPricesByAddress` with every `network:address` pair. Balance = result / 10^decimals. Add the rows to the holdings table, marked `watchlist`. A zero balance is still reported: it answers the question the user asked.

If the user asks about a token by symbol only, ask for the contract address or point to the tokens table in the wallet's block explorer. Do not guess an address.

## Step 5: 7-day price change

`getHistoricalTokenPrices` with `interval` `"1d"`, `startTime` = today minus 7 days at `00:00:00Z`, `endTime` = today at `00:00:00Z`. The `symbol` form returns 8 daily points; the `network` + `address` form may return 7, without today's point. Change = (last - first) / first, as a signed percent with one decimal, and the table states the two dates it compares.

Run it for:

- each distinct native price symbol with a non-zero balance (`symbol` form: `ETH` once even if held on four networks, `POL` once), and
- up to 3 priced tokens from Steps 3 and 4, largest by USD, using the `network` + `address` form.

Skip stablecoins if the 3-token cap is tight; their change is noise. At most 5 history calls per run. Keep batches to 4.

## Step 6: total and coverage

Total = natives + priced page-1 tokens + watchlist. Show it with a coverage line that says exactly what is in it, for example "5 native balances, 1 priced token from page 1, 3 watchlist tokens. Page 1 held 9 other tokens with no price." Rows under $0.01 show as `<$0.01` and still count. Never present the total as a portfolio value: it is what the Free-tier calls could price.

Two things this brief does not cover: NFTs (Lab 4) and staked or locked positions held by other contracts. Say so under Gaps.

## Report template

Bold labels as bullets, Markdown tables for balances, bare tool names. Omit the watchlist rows if none were given.

```markdown
# Multichain brief
- **Input:** as given, plus the resolved address for ENS
- **Networks:** the ids queried, and any dropped from the response
- **Observed:** date, and the earliest price lastUpdatedAt
- **Account type:** plain account | EIP-7702 delegated -> delegate | contract (checked on eth-mainnet)
- **Native balances** (table: network, balance, price, USD)
- **Token holdings** (table: network, token, address, balance, price, USD, note) with `page 1` or `watchlist` in the note, plus `no logo` where it applies
- **Total:** USD, with the coverage line
- **7-day change** (table: asset, 7 days ago, today, change)
- **Noise:** counts of zero-balance and unpriced page-1 rows, spam-shaped symbols named
- **Tools used (in order):** bare MCP names, counts for repeats
- **Gaps:** networks dropped, tokens beyond page 1, NFTs, staked positions, anything that failed
```

Prices come from Alchemy's price feed and lag the market by minutes. This is not financial advice and not a valuation.
