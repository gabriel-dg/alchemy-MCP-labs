# Reference runs

One report per scenario, produced by an agent that was given only the prompt from `PROMPTS.md` and followed `SKILL.md` cold. Each file starts with a header that states the run date. Activity counts, balances, and prices change over time; the type, identity, proxy detection, and assessment should not.

| File | Scenario | Assessment |
|------|----------|------------|
| [permit2.md](permit2.md) | A. Uniswap Permit2 | ESTABLISHED |
| [usdc-proxy.md](usdc-proxy.md) | B. USDC, a proxy with a legacy implementation slot | ESTABLISHED |
| [counterfeit-eth-token.md](counterfeit-eth-token.md) | C. an ERC-20 calling itself "ETH" | RED FLAGS |
| [eip7702-delegate.md](eip7702-delegate.md) | D. the delegate behind `vitalik.eth` | UNCERTAIN |
| [not-a-contract.md](not-a-contract.md) | E. `0x1111…1111`, the spender from Lab 1 | NOT A CONTRACT |

Use them to compare against your own output, and to see the tool call order without running anything.
