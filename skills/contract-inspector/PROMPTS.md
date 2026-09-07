# contract-inspector: copy-paste prompts

One block per scenario. Each is self-contained. Open this repo in your agent first so it can read `SKILL.md` by path. Every input below is real and harmless to query.

## A. An established protocol contract (expect ESTABLISHED)

Uniswap Permit2, the approval router used by most modern swaps.

```text
Read skills/contract-inspector/SKILL.md in this repo and follow it exactly. Select an Alchemy app first if none is selected. Use only the tools the skill allows. Never sign, send, or broadcast.

Address: 0x000000000022D473030F116dDEE9F6B43aC78BA3
Network: eth-mainnet
```

## B. A proxy token (expect ESTABLISHED, proxy → implementation)

USDC. Its proxy uses the legacy storage slot, not the EIP-1967 one.

```text
Read skills/contract-inspector/SKILL.md in this repo and follow it exactly. Select an Alchemy app first if none is selected. Use only the tools the skill allows. Never sign, send, or broadcast.

Address: 0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48
Network: eth-mainnet
```

## C. A counterfeit token (expect RED FLAGS)

An ERC-20 that calls itself "ETH". It appeared in Lab 1's wallet briefings as forged outbound transfers.

```text
Read skills/contract-inspector/SKILL.md in this repo and follow it exactly. Select an Alchemy app first if none is selected. Use only the tools the skill allows. Never sign, send, or broadcast.

Address: 0xcbb23e2ee87384799c45508c8a5ccaa6c611dd48
Network: eth-mainnet
```

## D. An unknown contract (expect UNCERTAIN)

The EIP-7702 delegate behind `vitalik.eth` in Lab 1. A contract with no token interface and no verified-source match.

```text
Read skills/contract-inspector/SKILL.md in this repo and follow it exactly. Select an Alchemy app first if none is selected. Use only the tools the skill allows. Never sign, send, or broadcast.

Address: 0x5a7fc11397e9a8ad41bf10bf13f22b0a63f96f6d
Network: eth-mainnet
```

## E. Not a contract (expect NOT A CONTRACT)

The spender from Lab 1's unlimited-approve scenario.

```text
Read skills/contract-inspector/SKILL.md in this repo and follow it exactly. Select an Alchemy app first if none is selected. Use only the tools the skill allows. Never sign, send, or broadcast.

Address: 0x1111111111111111111111111111111111111111
Network: eth-mainnet
```

Replace the address with any `0x` address from a wallet popup, a Lab 1 report, or a block explorer.

## Optional lines

- `Network: base-mainnet` (or any id from `list_chains`; the app must have it enabled)
- `Keep the report under 200 words.`
