# Lab 4: Solana wallet brief

## Goal

Same question as Lab 3, different virtual machine. You give the agent a Solana address and get back its SOL in dollars, the tokens it could price, the last five things that happened to it, the newest one decoded into a sentence, and the NFTs it owns, compressed ones included. Give it a transaction signature instead and it tells you who signed, what moved, and which programs ran.

No Solana SDK, no RPC URL, no explorer tabs. The same MCP connection you used for Ethereum reads Solana; only the tool names change. This lab also has the repo's first devnet section, because one API family is not yet open on mainnet for Free apps. The skill says exactly which, and what to expect when it opens.

The playbook the agent follows is [skills/solana-wallet-brief/SKILL.md](../../skills/solana-wallet-brief/SKILL.md). Everything runs on the Free tier. Nothing is signed, sent, or airdropped.

## What the agent reads

| Signal | How | Why it matters |
|--------|-----|----------------|
| Cluster pulse | `solana_getEpochInfo`, `solana_getPriorityFeeEstimate` | Slot and epoch are Solana's block number. The fee estimate says what a transaction costs to land right now. |
| Account type | `solana_getAccountInfo` | Solana has no `ethGetCode`. Every account has an owner program, and the owner says whether this is a wallet, a program, a token account, or a PDA. |
| Holdings in dollars | `getTokensByAddress` with `networks: ["solana-mainnet"]` | The same Portfolio API as Lab 3. Names, logos and prices for SOL and page 1 of the tokens, both token programs covered. |
| Watchlist | `solana_getTokenAccountsByOwner` by mint, `getTokenPricesByAddress` | Page 1 is sorted by mint, not by value. A token you know you hold is read directly. |
| Recent activity | `solana_getSignaturesForAddress` | The last five signatures, with status and memo. Memos are where Solana spam lives. |
| One transaction, decoded | `solana_getTransaction` | Balance before and after for every account, in SOL and in tokens. Who signed. Which programs ran. |
| Assets | `solana_getAssetsByOwner`, `solana_getAssetProof` | The Digital Asset Standard: NFTs, compressed NFTs, and the Merkle proof that makes a compressed one real. Devnet for now. |

## Before you start

- [SETUP.md](../../SETUP.md) done and [Lab 0](../00-hello-mcp/README.md) passed
- This repo open in your agent
- **Solana enabled on your app.** Open the app in the [dashboard](https://dashboard.alchemy.com), go to Networks, and switch on Solana Mainnet and Solana Devnet. If you skip this, the first call answers a 403 with a link to exactly that page. The agent shows the link and stops.
- Claude Code users: the repo's `.claude/settings.json` pre-approves every tool this lab uses. Other agents may ask once per tool; say yes.

## Run it

Pick one. Paste the block. The agent selects an app if needed, makes the calls, and prints the report. A bare address takes about 9 calls, a watchlist adds one per mint plus one price call, a signature takes 4, and the devnet run about 12.

### A. An exchange hot wallet

`5tzFkiKscXHK5ZXCGbXZxdw7gTjjD1mBwuoFbhUvuAi9`, labelled on public explorers as a Binance hot wallet. Over a million SOL, a new transaction every second, and a page 1 that includes a token called "USD Coin" that is not USDC and a token whose symbol is "SOL".

```text
Read skills/solana-wallet-brief/SKILL.md in this repo and follow it exactly. Select an Alchemy app first if none is selected. Use only the tools the skill allows. Never sign, send, broadcast, or request an airdrop.

Input: 5tzFkiKscXHK5ZXCGbXZxdw7gTjjD1mBwuoFbhUvuAi9
```

### B. A wallet with a watchlist

`86xCnPeV69n6t3DnyGvkKobf9FdN2H9oiVDdaMpo2MMY`, the example wallet in the official DAS API documentation. It holds USDC and a dust amount of JUP, neither of which reaches page 1. The `Tokens:` lines make the agent read them by mint.

```text
Read skills/solana-wallet-brief/SKILL.md in this repo and follow it exactly. Select an Alchemy app first if none is selected. Use only the tools the skill allows. Never sign, send, broadcast, or request an airdrop.

Input: 86xCnPeV69n6t3DnyGvkKobf9FdN2H9oiVDdaMpo2MMY
Tokens:
- EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v
- JUPyiwrYJFskUPiHa7hkeR8VUtAeFoSYbKedZNsDvCN
```

### C. Decode one transaction

A signature from the scenario A wallet: a batch withdrawal of two SOL transfers.

```text
Read skills/solana-wallet-brief/SKILL.md in this repo and follow it exactly. Select an Alchemy app first if none is selected. Use only the tools the skill allows. Never sign, send, broadcast, or request an airdrop.

Input: 39wp7WQAzsd1aGLB99dz4hYYQaf6PAVjciNnVAEzeUrWktJZsrgoT7iLwiNV6f2yc9k6MwBobYqNECph3DpLLxfp
```

### D. NFTs and compressed NFTs on devnet

The scenario B wallet on `solana-devnet`, where the asset API answers.

```text
Read skills/solana-wallet-brief/SKILL.md in this repo and follow it exactly. Select an Alchemy app first if none is selected. Use only the tools the skill allows. Never sign, send, broadcast, or request an airdrop.

Input: 86xCnPeV69n6t3DnyGvkKobf9FdN2H9oiVDdaMpo2MMY
Network: solana-devnet
```

## What you should see

An address produces this shape:

```markdown
# Solana wallet brief
- **Input**
- **Network**
- **Observed** (slot, epoch)
- **Account type**
- **SOL balance** (table)
- **Token holdings** (table, page 1 and watchlist)
- **Total** with a coverage line
- **Recent activity** (table, last 5)
- **Last transaction, decoded**
- **Assets** (table, or the one-line gap on mainnet)
- **Fees now**
- **Noise**
- **Tools used (in order)**
- **Gaps**
```

A signature produces a shorter **Solana transaction brief**: status, signers, SOL moves, token moves, programs invoked, one sentence in plain words.

Values observed on 2026-09-09. Full cold-run reports with every tool call are in [skills/solana-wallet-brief/examples/](../../skills/solana-wallet-brief/examples/).

**A, the exchange wallet.** About 1.75 million SOL, roughly $181 million from one call. Page 1 of its tokens has nothing priceable: two counterfeit symbols (a "SOL" that is not SOL, a "$USDC" that is not USDC), one NFT-shaped row, six unpriced. Five signatures in eighteen seconds. The newest decoded as a 299,492-unit token withdrawal to a customer, fee 0.000015 SOL, signed with a durable nonce. The Assets line says DAS is closed on mainnet for this app.

**B, the watchlist.** About 1,328 SOL plus 561 USDC and a millionth of a JUP, read by mint. One of the five recent signatures carries a memo advertising a token with four links. The newest transaction was signed by someone else: they created a token account inside this wallet, paid its rent, and dropped 20 million spam tokens into it. The report says so in one sentence.

**C, the signature.** Success, 750 compute units, fee 0.000011 SOL. Two fresh accounts received 0.047 and 0.099 SOL. No token moves. The first instruction advanced a durable nonce, the pattern exchanges use to sign offline. Four calls.

**D, devnet.** About 21 SOL of test money. Token accounts under both token programs, both lists truncated, so both counts are lower bounds. Nine assets across three pages: one regular NFT and eight compressed ones, most delegated to the same address. The proof for the first compressed asset has 14 hashes against a named tree and root.

## Reading the output

- **Account type** comes from the owner program, not from code. A wallet is owned by the System program. If the line says "token account of …", you pasted a token account, not a wallet; brief the owner it names.
- **SOL balance** is exact. Lamports divided by a billion. The price is Alchemy's feed, timestamped on the Observed line.
- **Token holdings** are partial. "page 1" rows come from a mint-sorted first page and are mostly spam; "watchlist" rows are what you asked for. Symbols on Solana are free text, so the skill only names a mint it can verify, and it flags a row whose symbol claims a well-known asset at the wrong mint as **counterfeit**.
- **Total** is what the calls could price. Read the coverage line before the number.
- **Recent activity** is the last five signatures. A memo with links is spam attached to a dust transfer; the wallet did not write it.
- **Last transaction, decoded** is built from balances before and after, not from instruction bytes. The line that matters most is whether the briefed wallet signed. If it did not, something was done *to* the wallet: a deposit, a dusting, an airdrop.
- **Assets** is the DAS list. On devnet it works today. On mainnet it answers `-32001` for Free apps at the time of writing; the skill makes one call, reports the gap, and moves on. When it opens, the same run fills the table with no change to the skill.
- **Fees now** is the base fee plus the priority estimate. Medium is what lands in a normal block; high is what you pay when the network is busy.
- **Noise** counts what the agent excluded and why: NFT-shaped rows, empty token accounts and the rent each one holds, unpriced and counterfeit rows, memo spam.

## Try your own

**Your wallet.** Paste your address as `Input:`. If you hold a token that does not show, add its mint under `Tokens:`. Copy mints from the tokens tab of a block explorer; do not let the agent guess.

**Any signature.** Paste one from your wallet's history to get the plain-words decode. Failed transactions decode too; the status line says so and the fee still shows.

**Your devnet wallet.** Add `Network: solana-devnet`. If you build on Solana you already have one. If not, the server exposes `solana_requestAirdrop`, which funds a devnet address with test SOL; the labs stay read-only, so the skill never calls it, but you can ask your agent to do it outside the lab and then brief the result.

**A program or a PDA.** The brief works on any address. A program gets "program" on the account-type line; a vault or stake account gets "program-owned account" with the owner named. Holdings for those usually sit elsewhere; the report says so.

**Cost check.** Add "then call `get_usage_summary`" to see the compute units the run used. A mainnet brief is a few hundred CU; the devnet run with three asset pages is a little more.

**Install as a skill (Claude Code).** Inside this repo, type `/solana-wallet-brief 5tzFkiKscXHK5ZXCGbXZxdw7gTjjD1mBwuoFbhUvuAi9`. To use it anywhere, copy `skills/solana-wallet-brief/` into `~/.claude/skills/`.

## Now build it

Every call in this brief is one Alchemy endpoint away. A Solana portfolio page, a deposit watcher, or a "what did this transaction do" button is the same handful of calls in your own code:

- [Solana API quickstart](https://www.alchemy.com/docs/reference/solana-api-quickstart) - the standard RPC methods the skill uses: account info, token accounts, signatures, transactions
- [Portfolio APIs](https://www.alchemy.com/docs/reference/portfolio-apis) - `getTokensByAddress` with `solana-mainnet` in the networks list, the same call as Lab 3
- [DAS APIs for Solana](https://www.alchemy.com/docs/reference/alchemy-das-apis-for-solana) - `getAssetsByOwner`, `getAssetProof` and the rest of the DAS family
- [Prices API quickstart](https://www.alchemy.com/docs/reference/prices-api-quickstart) - prices by mint with the `solana-mainnet:` prefix
- [Free tier](https://www.alchemy.com/pricing) - 30M compute units a month

## Troubleshooting

| Symptom | Fix |
|---------|-----|
| 403, "app does not support network" | Solana is not enabled on the selected app. The error carries the dashboard link. Enable Solana Mainnet and Solana Devnet, or select another app. |
| `-32001 Unable to complete request` on an asset call | The DAS API is closed on mainnet for this app. Expected. The skill reports it once and continues; run scenario D for the assets section. |
| `400 Unsupported network: solana-devnet` from `getTokensByAddress` | Expected. The Portfolio API is mainnet only. The skill reads token accounts by program on devnet instead, without prices. |
| `-32601 Method not found` from `solana_getPriorityFeeEstimate` | Expected on devnet. Skipped. |
| A response ends with "response too large" | Solana JSON is verbose and the server truncates around 8 KB. The skill uses small limits and treats a truncated list as a lower bound. Do not raise the limits. |
| `getTokensByAddress` answers "Internal server error" | The address is probably a program, not a wallet. The account-type line says which. The skill falls back to token accounts by program. |
| A token you hold is missing | Page 1 is mint-sorted and there is no page 2 through MCP. Add it as a `Tokens:` line. |
| The decoded transaction is missing balances | Very large transactions truncate before `preBalances`. The skill retries once with the compact `json` encoding, then reports fee and status only. |
| 429 rate limit | Free apps throttle bursts. The skill keeps batches to four calls and retries once. |
