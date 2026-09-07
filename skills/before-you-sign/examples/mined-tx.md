SAMPLE OUTPUT — not live

# Before you sign
- Input: mined tx hash `0xSampleMinedTxHash000000000000000000000000000000000000000000000001`
- Network: `eth-mainnet`
- One-sentence summary: After-the-fact read of a successful swap/transfer: wallet sold SAMPLE for ETH via a known router pattern (illustrative).
- Asset changes

| asset | from | to | amount | direction |
| --- | --- | --- | --- | --- |
| SAMPLE | wallet | pool/router | 100.0 SAMPLE | out (sample) |
| ETH | pool/router | wallet | 0.050 ETH | in (sample) |

- Risk flags
  - none observed (post-mine explanation only)
- Gas / fee snapshot
  - gas used: 150,000 (sample)
  - effective gas price: 20 gwei (sample)
  - fee paid: ~0.003 ETH (sample)
- Verdict: OK
- Tools used (exact MCP names, in order)
  1. `select_app`
  2. `ethGetTransactionByHash`
  3. `ethGetTransactionReceipt`
  4. `getAssetTransfers`
- Gaps
  - `traceTransaction` / `debugTraceTransaction` not needed for this sample summary
