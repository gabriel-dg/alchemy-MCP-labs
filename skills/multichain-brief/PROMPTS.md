# multichain-brief: copy-paste prompts

One block per scenario. Each is self-contained. Open this repo in your agent first so it can read `SKILL.md` by path. Every input below is real and harmless to query.

## A. An ENS name across five chains

`vitalik.eth`. The agent resolves the name onchain, then reads native balances on Ethereum, Base, Arbitrum, OP Mainnet and Polygon in one call. Page 1 of its tokens is vanity spam, which is the point: the brief prices what it can and says what it could not.

```text
Read skills/multichain-brief/SKILL.md in this repo and follow it exactly. Select an Alchemy app first if none is selected. Use only the tools the skill allows. Never sign, send, or broadcast.

Input: vitalik.eth
```

## B. A plain address with mistaken deposits

`0x1111…1111`, the code-less spender from Lab 1. People send it funds by mistake on every chain, and airdrop spam finds it too. Expect a real ETH balance, dust on the L2s, and spam-shaped symbols in the noise line.

```text
Read skills/multichain-brief/SKILL.md in this repo and follow it exactly. Select an Alchemy app first if none is selected. Use only the tools the skill allows. Never sign, send, or broadcast.

Input: 0x1111111111111111111111111111111111111111
```

## C. The same wallet with a watchlist

Scenario A again, by address, plus four tokens the page-1 listing cannot reach: USDC on Ethereum, Base and Arbitrum, and WETH on Ethereum. The agent reads each balance directly with `balanceOf`, prices them by address, and adds a 7-day change for WETH.

```text
Read skills/multichain-brief/SKILL.md in this repo and follow it exactly. Select an Alchemy app first if none is selected. Use only the tools the skill allows. Never sign, send, or broadcast.

Input: 0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045
Tokens:
- eth-mainnet:0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48
- base-mainnet:0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913
- arb-mainnet:0xaf88d065e77c8cC2239327C5EDb3A432268e5831
- eth-mainnet:0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2
```

Replace the input with any `0x` address or `.eth` name. For the watchlist, copy token contract addresses from the wallet's tokens tab on a block explorer; do not let the agent guess them.

## Optional lines

- `Networks: eth-mainnet, base-mainnet, zksync-mainnet` (any ids from `list_chains`; the app must have them enabled; unknown ones are dropped silently and reported under Gaps)
- `Tokens:` up to five `network:address` lines, as in scenario C
- `Keep the report under 200 words.`
