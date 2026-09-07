> Cold run, Branch A, 2026-09-07. An agent was given only the Scenario A2 prompt from `PROMPTS.md` and followed `SKILL.md`. Report reproduced as written, lightly trimmed. Balance, page-1 lists, and NFT count change over time; the resolved address, the code check, and the verdict should not.

# Before you sign

- **Input:** `nick.eth`, resolved onchain to `0xb8c2c29ee19d8307cb7255e1cd9cbde883a267d5` (namehash `0x05a67c0ee82964c4f7394cdd47fee7f4d9503a23c09c38341779ea012afe6e00`, resolver `0x4976fb03c32e5b8cfe2b6ccb31c09ba78ebaba41`)
- **Network:** eth-mainnet
- **One-sentence summary:** `nick.eth` is a plain EOA (code `0x`, no EIP-7702 delegation) holding about 9.17 ETH plus ENS tokens, with page-1 token and transfer data dominated by airdropped vanity spam and forged lookalike-"ETH" transfers rather than any wallet-initiated activity.

## Asset changes (page 1 only)

| Asset | From | To | Amount | Direction |
|---|---|---|---|---|
| ETH (native) | — | nick.eth | 9.1675 ETH | holding |
| ENS (`0xc183…9d72`, verified metadata "Ethereum Name Service") | `0x91c3…d39b` | nick.eth | 65.42 ENS | inbound |
| wstGBP (`0x57c3…b7ae`) | `0xabca…99d5` | nick.eth | 1.0 | inbound (unverified token) |
| PIPECAT (`0xa4d0…4e7d`) | `0xfc61…ee88` | nick.eth | 12,000,000 | inbound (unverified token, likely airdrop spam) |
| YRISE (`0x6051…68b8`) | `0x2767…f482` | nick.eth | 0.00009 | inbound (dust, likely spam) |
| NFTs | — | nick.eth | 1,033 total (5 sampled) | holding |

No wallet-initiated outbound transfers appear on page 1; all four outbound rows are forged (see Risk flags).

## Risk flags

- **Forged outbound "ETH" transfers (address-poisoning pattern).** All 4 outbound page-1 rows are 150-unit ERC-20 transfers of tokens named `ETH`, `E឵Τ឵H`, `ĖTḨ` (Unicode lookalikes) from contracts `0xcbb2…dd48`, `0xddc2…e1c0`, `0x2b09…8dd6`, `0x0b7b…2a94`, sent to lookalike recipients `0x983…4689`. `getTokenMetadata` on `0xcbb2…dd48` confirms a counterfeit token calling itself "ETH". These are spam contracts emitting events in the wallet's name, not the wallet's intent.
- **Vanity `0x0000…` dust in token balances.** Page 1 of token balances is address-sorted and consists of vanity contracts, one with a `uint256.max` balance. Pattern of airdropped spam, not real holdings.
- **Inbound spam airdrops.** PIPECAT and YRISE fit the airdrop-spam pattern.
- One sampled NFT carries Alchemy classification `SpammyMetadata`, though `isSpamContract` returns false for it.
- No EIP-7702 delegation: `ethGetCode` returned `0x`.
- No unlimited-approval evidence in the transfer data reviewed.

## Gas / fee snapshot

Not applicable (Branch A, no transaction to estimate).

## Verdict: OK

Plain EOA, real ENS holdings, spam noise only. Nothing observed that counts against the wallet itself.

## Tools used (exact MCP names, in order)

1. `list_apps`
2. `select_app`
3. `web3Sha3` (x4)
4. `ethCall` (x2)
5. `ethGetBalance`
6. `ethGetCode`
7. `getTokenBalancesByAddress` (x2, second with `limit` after truncation)
8. `getAssetTransfers` (x2: inbound, outbound)
9. `getNFTsForOwner`
10. `getTokenMetadata` (x2)
11. `isSpamContract` (x2)

## Gaps

- Token balances, transfers, and NFTs are **page 1 only**. Token balance page 1 is address-sorted, so real holdings beyond the ones seen in transfers are not confirmed and their absence is not evidence.
- First `getTokenBalancesByAddress` response was truncated by the MCP output limit; retried with `limit: 10`.
- No `isSpamContract` check on the ERC-20 spam contracts (skill restricts it to NFT contracts).
- No traces (Free profile).

These heuristics are not financial advice and not a security audit.

## Raw call log

1. `list_apps` → 2 apps.
2. `select_app` → Selected "<your app>", API key cached.
3. `web3Sha3` `{data: "0x657468"}` → `0x4f5b8127…d3d7f0` (matches skill constant).
4. `web3Sha3` `{data: "0x" + 64 zeros + "4f5b8127…d3d7f0"}` → `0x93cdeb70…3fc4ae` (matches skill constant).
5. `web3Sha3` `{data: "0x6e69636b"}` → `0x5d5727cb…84f68f`.
6. `web3Sha3` `{data: "0x93cdeb70…3fc4ae" ++ "5d5727cb…84f68f"}` → namehash `0x05a67c0e…fe6e00`.
7. `ethCall` registry, `0x0178b8bf` ++ namehash → resolver `0x4976…ba41`.
8. `ethCall` resolver, `0x3b3b57de` ++ namehash → address `0xb8c2…67d5`.
9. `ethGetBalance` → `0x7f397d1699c3dbc5` (9.1675 ETH).
10. `ethGetCode` → `0x`.
11. `getTokenBalancesByAddress` `{address, networks: ["eth-mainnet"]}` → truncated ("response too large").
12. `getAssetTransfers` `{category: ["external","erc20","erc721","erc1155"], toAddress, maxCount: "0x4", order: "desc"}` → 4 inbound erc20.
13. `getAssetTransfers` same with `fromAddress` → 4 outbound forged "ETH" tokens.
14. `getNFTsForOwner` `{owner, pageSize: 5, withMetadata: false}` → 5 NFTs, totalCount 1033.
15. `getTokenBalancesByAddress` `{address, networks: ["eth-mainnet"], limit: 10}` → native + 9 vanity tokens.
16. `getTokenMetadata` `0xcbb2…dd48` → name "ETH", symbol "ETH", no logo (counterfeit).
17. `getTokenMetadata` `0xc183…9d72` → "Ethereum Name Service" / ENS, logo present.
18. `isSpamContract` (NFT contract 1) → false.
19. `isSpamContract` (NFT contract 2) → false.

Nineteen calls, about 110 seconds end to end.
