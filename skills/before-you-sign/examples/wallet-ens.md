SAMPLE OUTPUT — not live

# Before you sign
- Input: `vitalik.eth` (resolved sample address `0xSampleResolvedAddress000000000000000001`)
- Network: `eth-mainnet`
- One-sentence summary: Page 1 of a famous-wallet ENS briefing is vanity-spam first, not a clean token bag; native ETH is present.
- Asset changes (page 1 only)

| asset | from | to | amount | direction |
| --- | --- | --- | --- | --- |
| ETH | — | wallet | 1.234 ETH | balance (sample) |

- Risk flags
  - page 1 only: vanity `0x0000…` dust tokens listed first (pattern, not holdings)
  - NFT page 1 may include spam; no paid spam filters on Free
- Gas / fee snapshot
  - n/a (read-only account briefing; sample gas price ~12 gwei)
- Verdict: OK
- Tools used (exact MCP names, in order)
  1. `select_app`
  2. `ethCall` (ENS Registry resolver + addr)
  3. `ethGetCode`
  4. `ethGetBalance`
  5. `getTokenBalancesByAddress` (page 1 only)
  6. `getAssetTransfers` (page 1 only)
  7. `getNFTsForOwner` (page 1 only)
  8. `isSpamContract`
- Gaps
  - famous-wallet page 1 is not the portfolio
  - missing USDC ≠ no USDC
  - NFT and transfer windows truncated for brevity (sample)
