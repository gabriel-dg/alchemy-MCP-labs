> Cold run, 2026-09-07. An agent was given only the Scenario A prompt from `PROMPTS.md` and followed `SKILL.md`. Report reproduced as written, lightly trimmed. Event counts and the last inbound transfer change constantly; the type, verified-source match, and assessment do not. This run hit Alchemy rate limits because five cold runs shared one Free app at the same time.

# Contract inspector
- **Address:** 0x000000000022D473030F116dDEE9F6B43aC78BA3
- **Network:** eth-mainnet
- **Type:** contract (no proxy detected on the three standard slots)
- **Identity:** non-token contract. `getTokenMetadata` rejected it as "not a valid token contract address"; `getContractMetadata` returned `tokenType: UNKNOWN` with no name or symbol. Not claimed by the skill's tools, but worth noting: the bytecode contains the string `PermitBatchWitnessTransferFrom(TokenPermissions[] permitted,address spender,uint256 nonce,uint256 deadline,` and the contract exposes `DOMAIN_SEPARATOR()`; both are consistent with a Permit2-style signature-based token-approval contract, and this address is the well-known canonical Permit2 address. Treat that as a label, not a verified identity.

- **Signals**

| Signal | Result |
|---|---|
| has code | yes (large bytecode; server truncated the response) |
| proxy | none detected (EIP-1967 implementation, legacy ZeppelinOS, and beacon slots all zero) |
| verified source | verified (ABI matched on `DOMAIN_SEPARATOR`, authority ETHERSCAN); `name()` and `owner()` reverted with no decode |
| token metadata | not an ERC-20 |
| NFT spam flags | `isSpam: false`, `tokenType: UNKNOWN` (not an NFT; `isSpamContract` not called) |
| first activity | 2023-03-23T13:52:59Z, block 0x101bb20 (first inbound ERC-20 transfer, 297.18 ETHDYDX). Lower bound on age, not a deployment date; over 3 years ago |
| last activity | 2026-08-31T16:00:23Z, block 0x18ad75e (inbound 1 SOS, dust). About 7 days before inspection |
| events in last 5 blocks | 9 events in blocks 0x18b9a61 to 0x18b9a65 (mostly topic `0xda9fa7c1…`, consistent with a Permit2 `Permit` event, referencing USDT, USDC and other tokens) |
| ETH balance | 0 |

- **Red flags:** none observed
- **Assessment:** ESTABLISHED. Etherscan ABI matched on `DOMAIN_SEPARATOR`, first activity over three years ago, and 9 events in the last 5 blocks show it is actively used right now.
- **What to do next:** If you are approving this contract as an ERC-20 spender, remember that "established" means widely used and verifiable, not safe: a Permit2-style contract lets any dApp you later sign a permit for move tokens up to the approved amount, so keep approvals bounded and check who the downstream spender is with `before-you-sign`. Confirm the address byte-for-byte against the dApp's published address before signing.
- **Tools used (in order):** `list_apps`, `select_app`, `ethGetCode`, `ethGetStorageAt` ×3, `simulateExecution` (`name()`), `getTokenMetadata` (429, retried), `ethBlockNumber`, `ethGetBalance`, `simulateExecution` (`owner()`, 429 twice, retried), `getTokenMetadata`, `ethGetLogs`, `getContractMetadata`, `simulateExecution` (`owner()`), `simulateExecution` (`DOMAIN_SEPARATOR()`), `getAssetTransfers` (asc), `getAssetTransfers` (desc)
- **Gaps:** Bytecode was truncated by the server, so no full-code analysis. Proxy check only covers the three listed slots. Age is a lower bound from the first inbound ERC-20 transfer, not the deployment block (`contractDeployer` and `deployedBlockNumber` came back null). The Permit2 label above is inferred from bytecode strings and the matched selector, not from any tool output naming the contract. `getTokenPricesByAddress` and `isSpamContract` were skipped because the address is neither an ERC-20 nor an NFT. Three calls hit Alchemy 429 rate limits and were retried.

These heuristics are not financial advice and not a security audit. "Established" means widely used and verifiable, not safe.

## Raw call log

1. `list_apps` → two apps.
2. `select_app` → Selected "<your app>", API key cached.
3. `ethGetCode` → large non-empty bytecode, truncated by the server.
4. `ethGetStorageAt` EIP-1967 implementation slot → zero.
5. `ethGetStorageAt` legacy ZeppelinOS slot → zero.
6. `ethGetStorageAt` EIP-1967 beacon slot → zero.
7. `simulateExecution` `{from: 0x1234…5678, value: "0x0", data: "0x06fdde03"}` → execution reverted, no `decoded`.
8. `getTokenMetadata` → RPC error 429 (compute units per second exceeded).
9. `ethBlockNumber` → `0x18b9a65`.
10. `ethGetBalance` → `0x0`.
11. `simulateExecution` `data: "0x8da5cb5b"` → RPC error 429.
12. `getTokenMetadata` (retry) → RPC error -32602 "expected a valid token contract address" (not an ERC-20).
13. `ethGetLogs` `{address, fromBlock: "0x18b9a61", toBlock: "0x18b9a65"}` → 9 logs, not truncated.
14. `simulateExecution` `data: "0x8da5cb5b"` (retry) → RPC error 429.
15. `getContractMetadata` → name/symbol null, `tokenType: "UNKNOWN"`, `isSpam: false`, deployer null.
16. `simulateExecution` `data: "0x8da5cb5b"` (retry) → execution reverted, no `decoded`.
17. `simulateExecution` `data: "0x3644e515"` → `decoded.authority: "ETHERSCAN"`, `methodName: "DOMAIN_SEPARATOR"`.
18. `getAssetTransfers` `{toAddress: addr, category: ["external","erc20"], order: "asc", maxCount: "0x1", withMetadata: true}` → 2023-03-23, 297.18 ETHDYDX.
19. `getAssetTransfers` same, `order: "desc"` → 2026-08-31, 1 SOS.

Nineteen calls including three rate-limit retries, about 120 seconds end to end.
