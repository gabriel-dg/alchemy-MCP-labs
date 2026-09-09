> Cold run, Scenario A, 2026-09-09, on skill v0.1.0. An agent was given only the Scenario A prompt from `PROMPTS.md` and followed `SKILL.md`. Report reproduced as written. This wallet moves every second: the balance, the signatures and the decoded transaction will differ on your run. The account type, the page-1 classification and the DAS gap should not.

# Solana wallet brief

- **Input:** `5tzFkiKscXHK5ZXCGbXZxdw7gTjjD1mBwuoFbhUvuAi9`
- **Network:** solana-mainnet
- **Observed:** 2026-09-09, 14:13 UTC. Slot 445,630,655, epoch 1031 (55.2% through). SOL price as of 14:12:50 UTC.
- **Account type:** wallet. Owned by the System program, no data, not executable.

## SOL balance

| Asset | Balance | Price (USD) | Value (USD) |
|---|---|---|---|
| SOL | 1,748,525.649516 | 103.49 | 180,954,919.47 |

## Token holdings

No priced token on page 1. See Noise.

## Total

**$180,954,919.47.** Coverage: SOL only. Page 1 held 9 token rows: 0 priced, 2 counterfeit symbols, 1 NFT-shaped, 6 unpriced. This is what the Free-tier calls could price, not a portfolio value. An exchange wallet holds most of its tokens beyond page 1; add `Tokens:` lines to read specific mints.

## Recent activity

| When (UTC) | Signature | Status | Memo |
|---|---|---|---|
| 2026-09-09 14:12:23 | `Ei1s2HoX…LsMrR` | success | none |
| 2026-09-09 14:12:23 | `5yW57tP8…aYRMs` | success | none |
| 2026-09-09 14:12:23 | `4UU4u3pX…x8EQw` | success | none |
| 2026-09-09 14:12:05 | `ec8Asq24…k5fRL` | success | none |
| 2026-09-09 14:12:05 | `4e3yGFWZ…EZjdo` | success | none |

Five transactions in eighteen seconds. Full signature of the newest: `Ei1s2HoXM6zaVhtjGALNkwdYaQcMjrQmKWBZW5aohZGj1XWpqhNgJxNL1EyD4vHehQuqb4u91ALSY4758TLsMrR`.

## Last transaction, decoded

- **Status:** success. Slot 445,630,689, 2026-09-09 14:12:23 UTC. Fee 0.000015 SOL (15,000 lamports). 555 compute units.
- **Signers:** `5tzF…uAi9`, the briefed wallet, fee payer. **The briefed wallet signed this.**
- **SOL moves:** `5tzF…uAi9` −0.000015 SOL (the fee, about $0.0016). Nothing else moved in SOL.
- **Token moves:** mint `2zMM…uauv` (not in the known-mints table, no symbol assigned): `5tzF…uAi9` −299,492.598831; `BPiu…RJV3` +299,492.598831 (that account held 0 before).
- **Programs invoked:** System, Compute Budget, Token.
- **In plain words:** `5tzF…uAi9` sent 299,492.598831 units of `mint 2zMM…uauv` to `BPiu…RJV3` through the Token program and paid 0.000015 SOL in fees. The first instruction is a parsed `advanceNonce` on `6ste…WyAi`, the durable-nonce pattern exchanges use to sign withdrawals offline.

## Assets

DAS unavailable on `solana-mainnet` for this app (-32001); run the same input with `Network: solana-devnet` to see this section, or against mainnet once the app has DAS access.

## Fees now

Base 0.000005 SOL per signature. Priority: medium 5,000, high 146,902 microlamports per compute unit.

## Noise

- **Counterfeit symbol, 2:** `SOL` "Claude's Tomato" at `21vy…pump`, 172,711.67 units; `$USDC` "USD Coin·" at `2De3…neSc`, 4,058,283,696,216.10 units. Neither mint is the real asset. Not counted.
- **NFT-shaped, 1:** `ECLESTIA`, no name, decimals 0, balance 1. See Assets.
- **Unpriced, 6:** VIRUS 4,500; `$ROGERS` "MISTER ROGERS" 3,700; `0xm0nk` "I Am Rich" 36,969,696; ZFX "ZOOOM" 63,820; ZEROCOIN 6,972,087.96; `Polymarket` "OFFICIAL POLYMARKET " 400,000,000. Five have a `pump` mint suffix or a brand name in the symbol; spam-shaped.
- **Zero balance, 0.** Nothing to reclaim on page 1. Each empty token account would hold 0.00203928 SOL of rent.
- **Memo spam:** none in the last 5.

## Tools used (in order)

1. `list_apps`
2. `select_app`
3. `solana_getEpochInfo`
4. `solana_getPriorityFeeEstimate`
5. `solana_getAccountInfo`
6. `getTokensByAddress`
7. `solana_getSignaturesForAddress`
8. `solana_getAssetsByOwner`
9. `solana_getTransaction`

## Gaps

- Tokens are **page 1 only**, sorted by mint address. The wallet holds USDC, USDT and much else beyond page 1; nothing here says otherwise.
- DAS is closed on mainnet for this app (-32001). No NFT list.
- SOL in stake accounts and positions inside DeFi programs are not read.
- The mint in the decoded transaction is not in the skill's table, so it is reported by address only.

Prices come from Alchemy's price feed and lag the market by minutes. This is not financial advice and not a valuation.

## Raw call log

1. `list_apps` → 3 apps.
2. `select_app` → Selected "Alchemy MCP", API key cached.
3. `solana_getEpochInfo` `{network: "solana-mainnet"}` → absoluteSlot 445630655, epoch 1031, slotIndex 238655 of 432000.
4. `solana_getPriorityFeeEstimate` `{network, accountKeys: ["5tzF…uAi9"], options: {includeAllPriorityFeeLevels: true}}` → min 0, low 0, medium 5000, high 146902, veryHigh 1500000.
5. `solana_getAccountInfo` `{network, pubkey}` → owner `1111…1111`, executable false, lamports 1748525649515827, space 0.
6. `getTokensByAddress` `{address, networks: ["solana-mainnet"], limit: 10}` → 10 rows: native with price 103.49, then 9 token rows sorted by mint, none priced. `pageKey` present but not exposed as a parameter.
7. `solana_getSignaturesForAddress` `{network, address, limit: 5}` → 5 signatures, all `err: null`, all `memo: null`.
8. `solana_getAssetsByOwner` `{network: "solana-mainnet", ownerAddress, limit: 3, page: 1}` → RPC error -32001 Unable to complete request at this time. Not retried.
9. `solana_getTransaction` `{network, signature: "Ei1s…MrR", maxSupportedTransactionVersion: 0}` → fee 15000, err null, 9 account keys, one signer, preTokenBalances/postTokenBalances for two accounts on mint `2zMM…uauv`, log lines for System, Compute Budget x2, Token.

Nine calls. Calls 3 to 6 were one batch, 7 and 8 another, 9 alone.
