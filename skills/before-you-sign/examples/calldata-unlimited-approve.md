> Cold run, Branch B, 2026-09-07. An agent was given only the Scenario B prompt from `PROMPTS.md` and followed `SKILL.md`. Report reproduced as written, lightly trimmed. Gas prices change; the simulated change, the code checks, and the verdict do not.

# Before you sign
- **Input:** Unsigned call on `eth-mainnet` from `0x1234567890abcdef1234567890abcdef12345678` to `0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48` (USDC), value 0, calldata `0x095ea7b3…` = `approve(spender=0x1111111111111111111111111111111111111111, value=2^256-1)`.
- **Network:** eth-mainnet
- **One-sentence summary:** This transaction grants an unlimited (max uint256) USDC allowance to `0x1111…1111`, an address with no contract code, which would let whoever controls that address move all USDC this wallet ever holds.
- **Asset changes**

| Asset | From | To | Amount | Direction |
|---|---|---|---|---|
| USDC (`0xa0b8…eb48`, 6 decimals) | `0x1234…5678` (owner) | `0x1111…1111` (spender) | 115792089237316195423570985008687907853269984665640564039457584007913129639935 raw (unlimited) | APPROVE (no tokens move now; future outbound allowed) |

- **Risk flags**
  - **Unlimited approval:** allowance value is `2^256-1`, not a bounded amount.
  - **Spender has no code:** `ethGetCode(0x1111…1111)` returned `0x`. The spender is an EOA or nonexistent, not a verified protocol contract. An unlimited approval to a code-less spender is a strong drain setup.
  - **Spender is a vanity/placeholder pattern** (`0x1111…1111`), a common shape for test values or approval-phishing targets.
  - **Current allowance is 0**, so this would be a new grant, not a renewal.
  - Simulation succeeded (returned `true`, emitted `Approval` log), so the risk is not a revert; it is what the successful call authorizes.
  - `to` is a proxy (delegatecalls to implementation `0x43506849d7c04f9138d1a2050bbf3a0c054402dd`); Alchemy decodes it via Etherscan ABI as USDC `approve`. Consistent with the canonical USDC contract.
  - `from` has code `0x`: plain EOA, no EIP-7702 delegation.
- **Gas / fee snapshot**
  - `ethEstimateGas`: `0xdd87` = 56,711 gas (simulation used `0xda62` = 55,906)
  - `ethGasPrice`: `0xa8e359d` = 177,091,997 wei, about 0.177 gwei
  - `ethMaxPriorityFeePerGas`: `0x2dc6c` = 187,500 wei
  - Estimated fee about 56,711 × 177,091,997 wei = **0.00001 ETH**
- **Verdict: DO NOT SIGN**
- **Tools used (in order):** `list_apps`, `select_app`, `simulateAssetChanges`, `simulateExecution`, `ethGetCode` (to), `ethGetCode` (spender), `ethGetCode` (from), `getTokenAllowance`, `ethEstimateGas`, `ethGasPrice`, `ethMaxPriorityFeePerGas`, `getTokenMetadata`
- **Gaps:** None. All required Branch B tools ran successfully. No paid-plan tools were needed or attempted.

These heuristics are not financial advice and not a security audit.

## Raw call log

1. `list_apps` `{}` → 2 apps.
2. `select_app` `{"app_id":"…"}` → Selected "<your app>", API key cached.
3. `simulateAssetChanges` `{"network":"eth-mainnet","from":"0x1234…5678","to":"0xA0b8…eB48","value":"0x0","data":"0x095ea7b3…"}` → 1 change: ERC20 APPROVE USDC to `0x1111…1111`, rawAmount 2^256-1, gasUsed `0xda62`, error null.
4. `simulateExecution` same params → CALL to USDC proxy, DELEGATECALL to `0x4350…02dd`, decoded `approve(spender, value)` returning `true`; 1 `Approval` log.
5. `ethGetCode` USDC → long bytecode (proxy).
6. `ethGetCode` `0x1111…1111` → `"0x"`.
7. `ethGetCode` `0x1234…5678` → `"0x"`.
8. `getTokenAllowance` `{"contract":USDC,"owner":"0x1234…5678","spender":"0x1111…1111"}` → `"0"`.
9. `ethEstimateGas` same tx params → `"0xdd87"`.
10. `ethGasPrice` → `"0xa8e359d"`.
11. `ethMaxPriorityFeePerGas` → `"0x2dc6c"`.
12. `getTokenMetadata` USDC → `{decimals: 6, name: "USDC", symbol: "USDC"}`.

Calls 3 to 12 were issued in one parallel batch after `select_app`. Twelve calls, about 75 seconds end to end.

## Compare: same calldata, known spender

Verified separately on the same day with Uniswap Permit2 `0x000000000022D473030F116dDEE9F6B43aC78BA3` as the spender: `simulateAssetChanges` returns the same unlimited APPROVE (gasUsed `0xda26`), but `ethGetCode` on Permit2 returns contract code. Under the skill's rules that is REVIEW (unlimited approval to a known contract), not DO NOT SIGN. Same shape, different spender, different answer.
