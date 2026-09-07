SAMPLE OUTPUT — not live

# Before you sign
- Input: unsigned unlimited `approve` calldata to `0x000000000000000000000000000000000000dEaD`
- Network: `eth-mainnet`
- One-sentence summary: Simulation succeeds: unlimited ERC-20 approve to the null/`dEaD` spender.
- Asset changes

| asset | from | to | amount | direction |
| --- | --- | --- | --- | --- |
| SAMPLE (ERC-20) | wallet | `0x…dEaD` | unlimited | approval (sample) |

- Risk flags
  - unlimited `approve` to `0x…dEaD`
  - no compensating known-router match
- Gas / fee snapshot
  - estimate ~45,000 gas (sample)
- Verdict: DO NOT SIGN
- Tools used (exact MCP names, in order)
  1. `select_app`
  2. `ethGetCode`
  3. `simulateAssetChanges`
  4. `simulateExecution`
  5. `ethEstimateGas`
- Gaps
  - none for the simulation path (sample)
