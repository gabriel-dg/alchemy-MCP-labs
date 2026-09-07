> Cold run, 2026-09-07. An agent was given only the Scenario C prompt from `PROMPTS.md` and followed `SKILL.md`. Report reproduced as written, lightly trimmed. The "days ago" figures move; the identity, burst pattern, and assessment do not.

# Contract inspector
- **Address:** 0xcbb23e2ee87384799c45508c8a5ccaa6c611dd48
- **Network:** eth-mainnet
- **Type:** contract (no proxy detected)
- **Identity:** ERC-20 "ETH" / ETH / 18 decimals

- **Signals**

| Signal | Result |
|--------|--------|
| has code | yes (about 1.1 KB runtime bytecode, not truncated) |
| proxy | no proxy detected (EIP-1967 implementation, legacy ZeppelinOS, and beacon slots all zero) |
| verified source | no ABI match on 3 probes: `name()` returned "ETH" but with no `decoded` block; `owner()` and `DOMAIN_SEPARATOR()` reverted. Unverified |
| token metadata | name "ETH", symbol "ETH", decimals 18 |
| logo | absent |
| price feed | none ("Price not found") |
| NFT spam flags | n/a (ERC-20) |
| first activity | 2026-06-24 07:39:59 UTC (block 0x1835c38), about 75 days ago |
| last activity | 2026-06-24 09:32:11 UTC (block 0x1835e68), about 2 hours after the first |
| events in last 5 blocks | 0 (blocks 0x18b9a62 to 0x18b9a66) |
| ETH balance | 0 |

- **Red flags:**
  - Native-asset symbol: an ERC-20 named and symbolled `ETH`. Ether is not an ERC-20; this imitates the native asset.
  - No price feed, no curated logo, no ABI match, and first activity under 90 days ago (all four together).
  - Burst pattern: all recorded transfers fall within a single 2-hour window on 2026-06-24, and nothing since.
  - Beyond the checklist, heuristic only: the bytecode returned by `ethGetCode` appears to hard-code `totalSupply()` as max-uint256, `balanceOf()` as a constant zero, and `transfer()` emits a `Transfer` event without changing any balance. This is the shape of a fake-event "airdrop" token used to spoof wallet and explorer histories.

- **Assessment:** RED FLAGS. Native-asset symbol `ETH` on an ERC-20 plus a same-day burst of transfers with no price, logo, or verified ABI.

- **What to do next:** Do not approve, buy, or interact with this token; it is not ether and appears designed to fake transfer history. If it appeared in your wallet, ignore or hide it rather than trying to "sell" it, and never sign anything a site presents to "unlock" it.

- **Tools used (in order):** `list_apps`, `select_app`, `ethGetCode`, `ethGetStorageAt` ×3, `simulateExecution` (`name()`), `getTokenMetadata`, `ethBlockNumber`, `ethGetBalance`, `simulateExecution` (`owner()`), `simulateExecution` (`DOMAIN_SEPARATOR()`), `getTokenPricesByAddress`, `getAssetTransfers` (asc), `getAssetTransfers` (desc), `ethGetLogs`

- **Gaps:** Source code was not retrieved (Free tier has no Etherscan source access; only the simulated-call decode signal was used). Deployment date and deployer not determined; first ERC-20 transfer is used as the age proxy. Holder count and total transfer count were not enumerated. `getContractMetadata` and `isSpamContract` were not called because the address resolved as an ERC-20.

## Raw call log

1. `list_apps` → two apps.
2. `select_app` → Selected "<your app>", API key cached.
3. `ethGetCode` → non-empty bytecode, about 1,100 bytes, solc 0.8.x metadata trailer; not `0x`, not `0xef0100`.
4. `ethGetStorageAt` EIP-1967 implementation slot → zero.
5. `ethGetStorageAt` legacy ZeppelinOS slot → zero.
6. `ethGetStorageAt` EIP-1967 beacon slot → zero.
7. `simulateExecution` `{from: 0x1234…5678, value: "0x0", data: "0x06fdde03"}` → one CALL, output is the ABI-encoded string "ETH"; no `decoded` block.
8. `getTokenMetadata` → `{decimals: 18, logo: null, name: "ETH", symbol: "ETH"}`.
9. `ethBlockNumber` → `0x18b9a66` (25,926,246).
10. `ethGetBalance` → `0x0`.
11. `simulateExecution` `data: "0x8da5cb5b"` → execution reverted, no `decoded`.
12. `simulateExecution` `data: "0x3644e515"` → execution reverted, no `decoded`.
13. `getTokenPricesByAddress` `["eth-mainnet:0xcbb2…dd48"]` → "Price not found".
14. `getAssetTransfers` `{category: ["erc20"], contractAddresses: [addr], order: "asc", maxCount: "0x1", withMetadata: true}` → first transfer 2026-06-24T07:39:59Z.
15. `getAssetTransfers` same, `order: "desc"` → last transfer 2026-06-24T09:32:11Z.
16. `ethGetLogs` `{address, fromBlock: "0x18b9a62", toBlock: "0x18b9a66"}` → `[]`.

Calls 4 to 10 in one parallel batch, 11 to 16 in a second. Sixteen calls, about 105 seconds end to end.
