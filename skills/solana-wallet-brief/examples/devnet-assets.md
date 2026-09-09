> Cold run, Scenario D, 2026-09-09, on skill v0.1.0. An agent was given only the Scenario D prompt from `PROMPTS.md` and followed `SKILL.md`. Report reproduced as written. Devnet state is test data and may be reset; the shape of the report, the two-program token read and the compressed-asset proof are what to compare.

# Solana wallet brief

- **Input:** `86xCnPeV69n6t3DnyGvkKobf9FdN2H9oiVDdaMpo2MMY`
- **Network:** solana-devnet. No prices, no USD, no total, no priority-fee estimate on devnet; the assets section runs here because DAS answers on devnet.
- **Observed:** 2026-09-09, 14:08 UTC. Slot 495,661,298, epoch 1147 (36.4% through).
- **Account type:** wallet. Owned by the System program, no data, not executable.

## SOL balance

| Asset | Balance |
|---|---|
| SOL | 21.133339 |

## Token holdings

Two calls, one per token program. Both responses truncated, so both counts are lower bounds.

| Mint | Program | Balance | Note |
|---|---|---|---|
| `DRTL…r5hK` | Token | 183.378606 | |
| `5D5n…Uqvf` | Token | 7,400,002,097.066660 | |
| `4zMM…ncDU` | Token | 10 | |
| `BDo3…4jaP` | Token | 35 | |
| `j2wd…Syim` | Token | 1 | decimals 0, the "Social Passport" NFT below |
| `BP8E…W2Ts` | Token-2022 | 5,000,000,000 | immutableOwner extension |
| `BPP3…CCw8` | Token-2022 | 500,000,000,000 | immutableOwner extension |
| `BP1o…KA55` | Token-2022 | 500,000,000,000 | immutableOwner extension |

At least 9 accounts under Token and at least 8 under Token-2022. The Token-2022 accounts hold 0.00207408 SOL of rent each, the standard ones 0.00203928 SOL. Querying only the classic Token program would have missed every Token-2022 row.

## Recent activity

| When (UTC) | Signature | Status | Memo |
|---|---|---|---|
| 2026-07-26 22:12:09 | `4nPRSzWE…fyoLh` | success | none |
| 2026-07-14 16:47:45 | `5TbRq2zB…jz3SB` | success | none |
| 2026-07-14 15:12:05 | `5aMj3NKP…wD3Fn` | success | none |
| 2026-07-14 14:58:45 | `V4ADE2JD…y6Sjv` | success | none |
| 2026-07-14 14:57:22 | `NaDVWQAn…LMDz4` | success | none |

Full signature of the newest: `4nPRSzWE6XAQox8NUtc3ExwVyt3ukrJLeCEFnYthRiYy1AYAb2VgRrtCZVYnzQCXuKLnVcgzmBs5hZCbqewfyoLh`.

## Last transaction, decoded

- **Status:** success. Slot 479,125,287, 2026-07-26 22:12:09 UTC. Fee 0.000005 SOL (5,000 lamports). 150 compute units.
- **Signers:** `5fzY…jMc5`, fee payer. **The briefed wallet did not sign.**
- **SOL moves:** `5fzY…jMc5` −0.000006 SOL (0.000001 transferred plus the 0.000005 fee). `86xC…2MMY` +0.000001 SOL (1,000 lamports).
- **Token moves:** none.
- **Programs invoked:** System.
- **In plain words:** `5fzY…jMc5` sent 1,000 lamports to `86xC…2MMY` and paid 0.000005 SOL in fees. The briefed wallet signed nothing. On devnet this is test traffic.

## Assets

At least 9 assets: three pages of 3, every page full, paging stopped at the skill's cap.

| Name | Symbol | Collection | Compressed | Id | Note |
|---|---|---|---|---|---|
| Social Passport | STP | `DNYf…gNzZ` | no | `j2wd…Syim` | regular NFT, Token program, ATA `6uYw…86SG` |
| Montana Land sDAO Deal Toy | sDAO | `EDjY…ki36` | yes | `2deg…P4tn` | delegated to `7Una…Wpoy` |
| Montana Land sDAO Deal Toy | sDAO | `BCcb…u71Y` | yes | `2hgQ…jSd6` | delegated to `7Una…Wpoy` |
| undefined #50 | SSNC | `2MFD…BNEP` | yes | `3LwB…NQAK` | no image, delegated to `7Una…Wpoy` |
| undefined #53 | SSNC | `2MFD…BNEP` | yes | `3TcH…Qxbb` | no image, delegated to `7Una…Wpoy` |
| Montana Land sDAO Deal Toy | sDAO | `EDjY…ki36` | yes | `3Z3L…sceL` | delegated to `7Una…Wpoy` |
| Montana Land sDAO Deal Toy | sDAO | `BCcb…u71Y` | yes | `415S…u7Dx` | delegated to `7Una…Wpoy` |
| Montana Land sDAO Deal Toy | sDAO | `EDjY…ki36` | yes | `53uj…QKgX` | delegated to `7Una…Wpoy` |
| Montana Land sDAO Deal Toy | sDAO | `BCcb…u71Y` | yes | `54B9…ZVnJ` | delegated to `7Una…Wpoy` |

**Proof for the first compressed asset** `2deg…P4tn`: tree `Fekm…pFED`, node index 16,432, 14 hashes, root `CxeP…4hbs`. The asset is a leaf in that Merkle tree, not a token account; the 14 hashes are what a program verifies before it lets the leaf move.

## Noise

- 1 NFT-shaped token account on the Token list (`j2wd…Syim`), matched to the "Social Passport" asset.
- No memo spam in the last 5.
- No zero-balance accounts among those returned.

## Tools used (in order)

1. `list_apps`
2. `select_app`
3. `solana_getEpochInfo`
4. `solana_getAccountInfo`
5. `solana_getTokenAccountsByOwner` (x2: Token, Token-2022)
6. `solana_getSignaturesForAddress`
7. `solana_getAssetsByOwner` (x3: pages 1, 2, 3)
8. `solana_getTransaction`
9. `solana_getAssetProof`

## Gaps

- Both token-account responses truncated; counts are lower bounds and the table shows the first rows of each.
- Assets capped at 3 pages; the wallet may own more.
- No prices on devnet, so no USD and no total.
- `solana_getPriorityFeeEstimate` is not available on devnet (`-32601`), skipped as the skill says.
- Nothing here says what the SSNC or sDAO assets are worth or whether their creators are who they claim; `creators[].verified` is false on every compressed item.

Nothing in this brief signs, sends, broadcasts, or requests an airdrop.

## Raw call log

1. `list_apps` → 3 apps.
2. `select_app` → Selected "Alchemy MCP", API key cached.
3. `solana_getEpochInfo` `{network: "solana-devnet"}` → absoluteSlot 495661298, epoch 1147, slotIndex 157298 of 432000.
4. `solana_getAccountInfo` `{network, pubkey}` → owner `1111…1111`, executable false, lamports 21133339029.
5. `solana_getTokenAccountsByOwner` `{network, owner, programId: "Tokenkeg…5DA"}` → 9 accounts before the "response too large" cut.
6. `solana_getTokenAccountsByOwner` `{network, owner, programId: "TokenzQd…xuEb"}` → 8 accounts before the cut, all `spl-token-2022` with `immutableOwner`, lamports 2074080 each.
7. `solana_getSignaturesForAddress` `{network, address, limit: 5}` → 5 signatures, all `err: null`, no memos.
8. `solana_getAssetsByOwner` `{network, ownerAddress, limit: 3, page: 1}` → 3 items, `total: 3` (echoes the limit).
9. `solana_getAssetsByOwner` `{…, page: 2}` → 3 items.
10. `solana_getAssetsByOwner` `{…, page: 3}` → 3 items. Cap reached.
11. `solana_getTransaction` `{network, signature: "4nPR…yoLh", maxSupportedTransactionVersion: 0}` → complete: fee 5000, preBalances `[10000000, 21133338029, 1]`, postBalances `[9994000, 21133339029, 1]`, one parsed System `transfer` of 1000 lamports.
12. `solana_getAssetProof` `{network, id: "2deg…P4tn"}` → root `CxeP…4hbs`, 14 proof hashes, node_index 16432, leaf `2RtT…qJsf`, tree_id `Fekm…pFED`.

Twelve calls. Calls 3 to 6 were one batch, 7 and 8 another, then 9, 10, 11 and 12 in two batches.
