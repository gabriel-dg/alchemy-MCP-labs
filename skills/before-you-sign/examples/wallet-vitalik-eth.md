> Cold run, Branch A, 2026-09-07, on a pre-v0.3.0 skill. An agent was given only the Scenario A prompt from `PROMPTS.md` and followed `SKILL.md`. Report reproduced as written, lightly trimmed. Balance, page-1 lists, and NFT count change over time; the resolved address, the delegation, and the verdict should not while the delegation stays in place.

# Before you sign

- **Input:** `vitalik.eth`, resolved onchain to `0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045` (ENS registry, resolver `0x231b0ee14048e9dccd1d247744d114a4eb5e8e63`, `addr()`; namehash `0xee6c4522aab0003e8d14cd40a6af439055fd2577951148c14b6cea9a53475835`, matching the recipe's sanity check)
- **Network:** `eth-mainnet`
- **One-sentence summary:** The wallet holds about 6.71 ETH and 10,001 NFTs, is an EIP-7702-delegated account (code `0xef0100…` pointing at contract `0x5a7fc11397e9a8ad41bf10bf13f22b0a63f96f6d`), and its page-1 token, transfer, and NFT data is dominated by vanity-address airdrop spam and forged "from vitalik" transfers rather than the wallet's own activity.

- **Asset changes** (Branch A: current holdings, page 1 only)

| Asset | From | To | Amount | Direction |
|---|---|---|---|---|
| ETH (native) | — | `0xd8dA…6045` | 6.712150 ETH (`0x5d2659027b0b8043` wei) | held |
| ERC-20 (defensible) | — | `0xd8dA…6045` | none on page 1; every page-1 entry is a `0x0000…` vanity-prefix contract | held (page 1 only) |
| NFTs | — | `0xd8dA…6045` | 10,001 total; page 1 (5 items) is 4/5 Alchemy-flagged spam | held (page 1 only) |
| ERC-1155 `0xfaaf…043c` id `0x1080…0037` | `0xd8dA…6045` | `0x7083…d321` | 1 | out (most recent outbound) |

- **Risk flags**
  - **EIP-7702 delegation (REVIEW):** `ethGetCode` on the wallet returned 23 bytes `0xef01005a7fc11397e9a8ad41bf10bf13f22b0a63f96f6d`. Signatures from this account are executed by delegate `0x5a7fc11397e9a8ad41bf10bf13f22b0a63f96f6d`, not a plain EOA. `ethGetCode` on the delegate confirms it is a contract (large bytecode; its dispatcher includes `isValidSignature`, `onERC721Received`, and `validateUserOp`-style selectors). Vendor not verified; not claimed.
  - **Vanity-address token spam pattern:** page 1 of `getTokenBalancesByAddress` (address-sorted) is entirely `0x0000…` prefixed contracts with large or zero balances. Treated as airdropped dust, not holdings. No defensible token could be identified from page 1.
  - **Forged "from wallet" transfers:** the 4 most recent outbound transfers include "Vitalik" (`0x6390ee…f685`, metadata name "From Vitalik") to `0x87f5…ca7b`, and two "<3" tokens "sent" to `0x0000…0000`. These are unknown contracts emitting Transfer events with the subject as `from`; forged spam, not the wallet's intent.
  - **Inbound spam:** the 4 most recent inbound transfers are EGP, "w🍖", WMC, and CMD from unknown contracts or minters. Airdrop noise.
  - **NFT spam:** `0x000386E3…59Dc3` is a confirmed spam contract (`isSpamContract` true; wallet holds 27 units). `0x00000000…242EB` is not flagged by `isSpamContract` but carries a `SpammyMetadata` classification.
  - No approval or allowance data was collected (Branch A; no transfer evidence of unlimited approvals on page 1).

- **Gas / fee snapshot:** n/a (account briefing).

- **Verdict: REVIEW.** Sole verdict reason is the EIP-7702 delegation. Spam tokens, forged transfers, and spam NFTs are noise patterns, not evidence against the wallet.

- **Tools used (in order):** `list_apps`, `select_app`, `web3Sha3` ×4 (hex "eth", hex "vitalik", node_eth, namehash), `ethCall` ×2 (registry `resolver`, resolver `addr`), `ethGetBalance`, `ethGetCode` (wallet), `getTokenBalancesByAddress`, `getAssetTransfers` ×2 (inbound, outbound), `getNFTsForOwner`, `ethGetCode` (delegate), `isSpamContract` ×2, `getTokenMetadata`

- **Gaps**
  - `getTokenBalancesByAddress` page 1 was truncated ("response too large") after roughly 24 entries. This run predates the skill's `limit 10` convention.
  - `getAssetTransfers`: 4 inbound + 4 outbound only, page 1 only.
  - `getNFTsForOwner`: 5 of 10,001 NFTs seen; no spam filters (Free tier).
  - Delegate contract bytecode was truncated; confirmed as a contract, vendor not verified.
  - No paid tools used; no paid-plan 400s encountered.

These heuristics are not financial advice and not a security audit.

## Raw call log

1. `list_apps` → 2 apps.
2. `select_app` → Selected "<your app>", API key cached.
3. `web3Sha3` `{data: "0x657468"}` → `0x4f5b8127…d3d7f0`.
4. `web3Sha3` `{data: "0x766974616c696b"}` → `0xaf2caa1c…7103cc`.
5. `web3Sha3` `{data: "0x" + 64 zeros + "4f5b8127…d3d7f0"}` → `0x93cdeb70…3fc4ae`.
6. `web3Sha3` `{data: "0x93cdeb70…3fc4ae" ++ "af2caa1c…7103cc"}` → `0xee6c4522…475835`.
7. `ethCall` registry, `0x0178b8bf` ++ node → resolver `0x231b…8e63`.
8. `ethCall` resolver, `0x3b3b57de` ++ node → `0xd8da…6045`.
9. `ethGetBalance` → `0x5d2659027b0b8043`.
10. `ethGetCode` wallet → `0xef01005a7fc11397e9a8ad41bf10bf13f22b0a63f96f6d`.
11. `getTokenBalancesByAddress` `{address, networks: ["eth-mainnet"]}` → native + ~24 vanity tokens, truncated.
12. `getAssetTransfers` `{category: [external, internal, erc20, erc721, erc1155], toAddress, maxCount: "0x4", order: "desc"}` → 4 inbound spam erc20.
13. `getAssetTransfers` same with `fromAddress` → 1 erc1155 out, 3 forged erc20.
14. `getNFTsForOwner` `{owner, pageSize: 5, withMetadata: false}` → 5 NFTs, totalCount 10001, 4/5 `isSpam`.
15. `ethGetCode` delegate → large bytecode, truncated.
16. `isSpamContract` `0x0000…242EB` → false.
17. `isSpamContract` `0x0003…59Dc3` → true.
18. `getTokenMetadata` `0x6390…f685` → name "From Vitalik", symbol "Vitalik", 9 decimals, no logo.

Eighteen calls, about 160 seconds end to end.
