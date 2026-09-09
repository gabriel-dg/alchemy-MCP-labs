# solana-wallet-brief: copy-paste prompts

One block per scenario. Each is self-contained. Open this repo in your agent first so it can read `SKILL.md` by path. Every input below is public chain data and harmless to query. Your app needs Solana enabled; the first call tells you if it is not, with a link to the dashboard.

## A. An exchange hot wallet on mainnet

`5tzFkiKscXHK5ZXCGbXZxdw7gTjjD1mBwuoFbhUvuAi9`, labelled as a Binance hot wallet on public explorers. Over a million SOL, a new transaction every second, and a page 1 of tokens that includes a counterfeit "USD Coin" and a token whose symbol is "SOL". The agent reads the balance in dollars, decodes the newest withdrawal, and flags the fakes.

```text
Read skills/solana-wallet-brief/SKILL.md in this repo and follow it exactly. Select an Alchemy app first if none is selected. Use only the tools the skill allows. Never sign, send, broadcast, or request an airdrop.

Input: 5tzFkiKscXHK5ZXCGbXZxdw7gTjjD1mBwuoFbhUvuAi9
```

## B. A wallet with a watchlist

`86xCnPeV69n6t3DnyGvkKobf9FdN2H9oiVDdaMpo2MMY`, the example wallet in the official DAS API documentation, which is why it is loaded with airdrops on mainnet and owns test assets on devnet. It holds real USDC and a dust amount of JUP, neither of which reaches page 1. The `Tokens:` lines make the agent read both balances directly by mint and price them. Its newest transaction, at the time of writing, was a token dusting: someone else paid the rent to create a token account in this wallet and dropped twenty million spam tokens into it.

```text
Read skills/solana-wallet-brief/SKILL.md in this repo and follow it exactly. Select an Alchemy app first if none is selected. Use only the tools the skill allows. Never sign, send, broadcast, or request an airdrop.

Input: 86xCnPeV69n6t3DnyGvkKobf9FdN2H9oiVDdaMpo2MMY
Tokens:
- EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v
- JUPyiwrYJFskUPiHa7hkeR8VUtAeFoSYbKedZNsDvCN
```

## C. Decode one transaction

A signature instead of an address. This one is a batch withdrawal from the scenario A wallet: two SOL transfers to two fresh accounts, signed with a durable nonce, fee 0.000011 SOL. The agent reports who signed, every balance that moved, and the programs invoked, in one sentence you can read without a block explorer.

```text
Read skills/solana-wallet-brief/SKILL.md in this repo and follow it exactly. Select an Alchemy app first if none is selected. Use only the tools the skill allows. Never sign, send, broadcast, or request an airdrop.

Input: 39wp7WQAzsd1aGLB99dz4hYYQaf6PAVjciNnVAEzeUrWktJZsrgoT7iLwiNV6f2yc9k6MwBobYqNECph3DpLLxfp
```

## D. NFTs and compressed NFTs on devnet

The scenario B wallet again, on `solana-devnet`, where the DAS API answers. It owns a regular NFT and several compressed ones. The agent lists them, fetches the Merkle proof for a compressed one, and reads token accounts under both token programs. No prices on devnet.

```text
Read skills/solana-wallet-brief/SKILL.md in this repo and follow it exactly. Select an Alchemy app first if none is selected. Use only the tools the skill allows. Never sign, send, broadcast, or request an airdrop.

Input: 86xCnPeV69n6t3DnyGvkKobf9FdN2H9oiVDdaMpo2MMY
Network: solana-devnet
```

Replace the input with any Solana address or signature. For the watchlist, copy mint addresses from the wallet's tokens tab on a block explorer; do not let the agent guess them.

## Optional lines

- `Network: solana-devnet` (default is `solana-mainnet`; these two are the only valid ids)
- `Tokens:` up to five mint addresses, one per line, as in scenario B
- `Keep the report under 200 words.`
