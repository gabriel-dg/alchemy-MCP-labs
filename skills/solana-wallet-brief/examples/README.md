# Reference runs

One report per scenario, produced by an agent that was given only the prompt from `PROMPTS.md` and followed `SKILL.md` cold. Each file starts with a header that states the run date and the skill version. Balances, prices, the newest signature and the decoded transaction move every minute; the account type, the page-1 classification, the watchlist mechanics, the DAS gap on mainnet and the shape of the report should not.

| File | Scenario | What it shows |
|------|----------|---------------|
| [exchange-hot-wallet.md](exchange-hot-wallet.md) | A. an exchange hot wallet on mainnet | $181M of SOL from one call, two counterfeit symbols on page 1, a token withdrawal decoded seconds after it landed, the DAS gate |
| [wallet-watchlist.md](wallet-watchlist.md) | B. a wallet plus two watchlist mints | USDC and JUP read directly by mint and priced, a memo-spam signature, a token-dusting transaction the wallet never signed |
| [tx-exchange-withdrawal.md](tx-exchange-withdrawal.md) | C. one signature | Two SOL transfers to fresh accounts, the fee, the durable-nonce pattern, four calls |
| [devnet-assets.md](devnet-assets.md) | D. the same wallet on devnet | Token accounts under both token programs, nine assets via DAS, one compressed NFT with its Merkle proof |

Use them to compare against your own output, and to see the tool call order without running anything.

**Skill versions move; these files do not.** Each header states the run date and the skill version. A current run may legitimately make *fewer* calls than the example beside it, because the skill has since gained a documented shortcut. Compare account types, classifications and the shape of the report. Do not treat a difference in call count or in dollar figures as a fault in your run.

The four runs were made within fifteen minutes of each other on 2026-09-09, on an app with Solana enabled and no DAS access on mainnet. If your app has DAS access, run A and run B gain an Assets table and lose a Gap.
