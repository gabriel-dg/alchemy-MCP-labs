# Reference runs

One report per scenario, produced by an agent that was given only the prompt from `PROMPTS.md` and followed `SKILL.md` cold. Each file starts with a header that states the run date. Chain state changes, so balances, page-1 lists, and gas prices will differ when you run it. Addresses, hashes, calldata, verdicts, and the shape of the report should not.

| File | Scenario | Verdict |
|------|----------|---------|
| [wallet-vitalik-eth.md](wallet-vitalik-eth.md) | A. wallet briefing for `vitalik.eth` | REVIEW (EIP-7702 delegation) |
| [wallet-nick-eth.md](wallet-nick-eth.md) | A2. wallet briefing for `nick.eth` | OK (spam pattern flagged, plain account) |
| [calldata-unlimited-approve.md](calldata-unlimited-approve.md) | B. unlimited USDC approve to a code-less address | DO NOT SIGN |
| [mined-tx-usdc-transfer.md](mined-tx-usdc-transfer.md) | C. a real 120k USDC transfer | OK |

Use them to compare against your own output, and to see the tool call order without running anything.
