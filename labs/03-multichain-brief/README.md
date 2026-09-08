# Lab 3: multichain brief

## Goal

One call, every chain, in dollars. You give the agent an address or an ENS name and get back its native balance on Ethereum, Base, Arbitrum, OP Mainnet and Polygon, the tokens it could price, a USD total with an honest coverage line, and the 7-day price change of what it holds.

Labs 1 and 2 were about safety. This one is about reach: the same address lives on every EVM chain, and Alchemy's multi-chain token API reads all of them in a single request. The lab also introduces the price feeds, current and historical.

The playbook the agent follows is [skills/multichain-brief/SKILL.md](../../skills/multichain-brief/SKILL.md). Everything runs on the Free tier. Nothing is signed or sent.

## What the agent reads

| Signal | How | Why it matters |
|--------|-----|----------------|
| Resolved address | `web3Sha3` plus two `ethCall`s to the ENS registry | The same recipe as Lab 1. The agent never guesses an address. |
| Account type | `ethGetCode` on eth-mainnet | Plain account, EIP-7702 delegated, or contract. One line, then on with the brief. |
| Balances on five chains | `getTokensByAddress` with a `networks` list | One request returns native balances with prices, then page 1 of ERC-20s with metadata and prices. |
| Watchlist | `ethCall` `balanceOf`, `getTokenMetadata`, `getTokenPricesByAddress` | Page 1 is sorted by address, not by value. A token you know you hold is read directly. |
| 7-day change | `getHistoricalTokenPrices`, daily interval | Eight daily points per asset; the report shows first, last, and the percent between them. |

## Before you start

- [SETUP.md](../../SETUP.md) done and [Lab 0](../00-hello-mcp/README.md) passed
- This repo open in your agent
- Your app must have Ethereum, Base, Arbitrum, OP Mainnet and Polygon enabled. Apps created in the dashboard usually have all of them. A network the app lacks is dropped from the response silently; the agent lists it under Gaps.
- Claude Code users: the repo's `.claude/settings.json` pre-approves every tool this lab uses. Other agents may ask once per tool; say yes.

## Run it

Pick one. Paste the block. The agent selects an app if needed, makes the tool calls, and prints the report. An ENS input takes about 10 calls, a bare address about 6, and a watchlist adds 2 per token plus one price call.

### A. `vitalik.eth` on five chains

```text
Read skills/multichain-brief/SKILL.md in this repo and follow it exactly. Select an Alchemy app first if none is selected. Use only the tools the skill allows. Never sign, send, or broadcast.

Input: vitalik.eth
```

### B. A plain address with mistaken deposits

The spender from Lab 1 and Lab 2, `0x1111…1111`. It has never sent a transaction, yet it holds ETH on every chain because people send it funds by mistake.

```text
Read skills/multichain-brief/SKILL.md in this repo and follow it exactly. Select an Alchemy app first if none is selected. Use only the tools the skill allows. Never sign, send, or broadcast.

Input: 0x1111111111111111111111111111111111111111
```

### C. The same wallet with a watchlist

Scenario A by address, plus four tokens the page-1 listing cannot reach.

```text
Read skills/multichain-brief/SKILL.md in this repo and follow it exactly. Select an Alchemy app first if none is selected. Use only the tools the skill allows. Never sign, send, or broadcast.

Input: 0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045
Tokens:
- eth-mainnet:0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48
- base-mainnet:0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913
- arb-mainnet:0xaf88d065e77c8cC2239327C5EDb3A432268e5831
- eth-mainnet:0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2
```

## What you should see

Every run produces the same shape:

```markdown
# Multichain brief
- **Input**
- **Networks**
- **Observed**
- **Account type**
- **Native balances** (table)
- **Token holdings** (table, page 1 and watchlist)
- **Total** with a coverage line
- **7-day change** (table)
- **Noise**
- **Tools used (in order)**
- **Gaps**
```

Values observed on 2026-09-08. Full cold-run reports with every tool call are in [skills/multichain-brief/examples/](../../skills/multichain-brief/examples/).

**A, `vitalik.eth`.** Resolves to `0xd8da…6045`, an EIP-7702 delegated account. ETH on all four ETH-native chains and about 593 POL on Polygon, roughly $25,400 in natives. Page 1 of its tokens is six vanity-address rows, none with both a balance and a price, so the token table is empty and the coverage line says so. ETH up about 1% on the week, POL up about 5%.

**B, `0x1111…1111`.** Plain account, code `0x`. About 5.7 ETH on mainnet, a third of an ETH on Base, dust on Arbitrum, OP Mainnet and Polygon, roughly $15,100. One priced page-1 token, 0.0001 TUSD, worth less than a cent. The noise line names three spam-shaped symbols with URLs and "claim" in them.

**C, the watchlist.** Same natives as A, plus four rows read directly: about 1.46 WETH on mainnet and USDC on Ethereum, Base and Arbitrum. The watchlist adds about $3,900 that page 1 could not see, which is the point. WETH gets its own 7-day row; the three USDC rows are skipped as stablecoins. Base USDC shows `no logo` even though it is the canonical contract, a reminder that logo coverage on L2s is patchy.

## Reading the output

- **Native balances** are complete. One row per network, straight from the chain, priced by the same feed. If a network is missing here, the app does not have it enabled or the tool does not support it; check Gaps.
- **Token holdings** are partial. "page 1" rows come from an address-sorted first page and are usually spam or dust; "watchlist" rows are the ones you asked for. A token missing from the table is not evidence the wallet lacks it.
- **Total** is what the calls could price, with a coverage line that says what went in. Read the coverage line before the number.
- **7-day change** is one asset per row, not a portfolio return. The `symbol` form of the history call returns eight points; the `address` form may return seven, so the dates in the row can differ by a day.
- **Noise** is the rest of page 1: zero-balance rows and unpriced rows, with spam-shaped symbols named. It is there so you can see what the agent excluded and why.
- **Account type** is one line. If it says EIP-7702, run Lab 2 on the delegate.

## Try your own

**Your wallet.** Paste your address or ENS name as `Input:`. If you know you hold a token that does not appear, add a `Tokens:` line with its `network:address` from a block explorer. Do not let the agent guess token addresses.

**More networks.** Add `Networks: eth-mainnet, base-mainnet, zksync-mainnet, linea-mainnet`. The skill knows the native symbol for the common ETH-native L2s, BNB Chain, Avalanche and Gnosis. Each network costs one row of the page-1 budget, so the agent drops the `limit` accordingly.

**A treasury or a contract.** The brief works on any address. A contract that holds funds gets "contract" on the account-type line and the same tables.

**Cost check.** Add "then call `get_usage_summary`" to see the compute units the run used. A five-network brief is a few hundred CU.

**Install as a skill (Claude Code).** Inside this repo, type `/multichain-brief vitalik.eth`. To use it anywhere, copy `skills/multichain-brief/` into `~/.claude/skills/`.

## Now build it

The five-chain call and the price rows in this brief are two Alchemy APIs you can hit directly. A portfolio view, a treasury dashboard, or a daily exposure report is the same two calls in a loop:

- [Portfolio APIs](https://www.alchemy.com/docs/reference/portfolio-apis) - `getTokensByAddress` and friends: balances, metadata and prices across chains in one request
- [Prices API quickstart](https://www.alchemy.com/docs/reference/prices-api-quickstart) - current and historical prices, by symbol or by address
- [Free tier](https://www.alchemy.com/pricing) - 30M compute units a month. A five-network brief is a few hundred CU.

## Troubleshooting

| Symptom | Fix |
|---------|-----|
| `getTokensByAddress` comes back with "response too large" | The `limit` was too high. Five networks fit in `limit` 12; 15 was already truncated. The skill retries once with a smaller limit. |
| A network you asked for is not in the response | The tool drops unsupported or unenabled networks without an error. Enable it for the app in the dashboard, or check the id with `list_chains`. `polygon-mainnet` works but is echoed as `matic-mainnet`. |
| Polygon native appears twice | Expected. The response lists it as the `null` row and again at `0x…1010` "Matic Token". The skill counts it once. |
| A native row has no price | Rare on the default networks. The skill falls back to `getTokenPricesBySymbol` with the network's price symbol. |
| A token you hold is missing | Page 1 is address-sorted. Add it as a `Tokens:` line. |
| The 7-day table has different dates per row | The `address` form of `getHistoricalTokenPrices` may omit today's point. The row states both dates. |
| 429 rate limit | Free apps throttle bursts. The skill keeps batches to four calls and retries once. |
