# Reference runs

One report per scenario, produced by an agent that was given only the prompt from `PROMPTS.md` and followed `SKILL.md` cold. Each file starts with a header that states the run date and the skill version. Balances, prices, USD totals and the 7-day change move every minute; the resolved address, the account type, the page-1 classification and the shape of the report should not.

| File | Scenario | What it shows |
|------|----------|---------------|
| [vitalik-eth.md](vitalik-eth.md) | A. `vitalik.eth` on five chains | ENS resolution, five native balances in dollars, a page 1 with nothing priceable |
| [not-a-contract-multichain.md](not-a-contract-multichain.md) | B. `0x1111…1111` on five chains | Mistaken deposits on every chain, one priced dust token, three spam-shaped symbols |
| [vitalik-watchlist.md](vitalik-watchlist.md) | C. same wallet plus four watchlist tokens | `balanceOf` through `ethCall`, prices by address, WETH 7-day change from the address form |

Use them to compare against your own output, and to see the tool call order without running anything.

**Skill versions move; these files do not.** Each header states the run date and the skill version. A current run may legitimately make *fewer* calls than the example beside it, because the skill has since gained a documented shortcut. Compare addresses, classifications and the shape of the report. Do not treat a difference in call count or in dollar figures as a fault in your run.

The three runs were made within two minutes of each other on 2026-09-08. The ETH and POL 7-day history calls were issued once, during run A, and their results reused for B and C; a cold run of B or C alone makes those two calls itself.
