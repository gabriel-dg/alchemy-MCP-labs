# before-you-sign: copy-paste prompts

One block per scenario. Each is self-contained. Open this repo in your agent first so it can read `SKILL.md` by path. Every input below is real and harmless to query.

## A. Wallet or ENS briefing (delegated account, expect REVIEW)

```text
Read skills/before-you-sign/SKILL.md in this repo and follow it exactly. Select an Alchemy app first if none is selected. Use only the tools the skill allows. Never sign, send, or broadcast.

Input: vitalik.eth
Network: eth-mainnet
```

## A2. Wallet or ENS briefing (plain account, expect OK)

```text
Read skills/before-you-sign/SKILL.md in this repo and follow it exactly. Select an Alchemy app first if none is selected. Use only the tools the skill allows. Never sign, send, or broadcast.

Input: nick.eth
Network: eth-mainnet
```

Replace the name with any ENS name or `0x` address.

## B. Unsigned calldata (simulate before signing, expect DO NOT SIGN)

Unlimited USDC `approve` to `0x1111…1111`, an address with no code. `From` is a throwaway address with no code and no balance; an approve simulates fine without one.

```text
Read skills/before-you-sign/SKILL.md in this repo and follow it exactly. Select an Alchemy app first if none is selected. Use only the tools the skill allows. Never sign, send, or broadcast. This is unsigned calldata: you must simulate it.

Network: eth-mainnet
From: 0x1234567890abcdef1234567890abcdef12345678
To: 0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48
Value: 0
Calldata: 0x095ea7b30000000000000000000000001111111111111111111111111111111111111111ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff
```

To check something a dapp is asking you to sign, replace `From` with your address and `To`, `Value`, and `Calldata` with what the wallet popup shows (MetaMask: Hex tab; Rabby: View raw).

## C. Mined transaction (expect OK)

A real USDC transfer from `vitalik.eth`.

```text
Read skills/before-you-sign/SKILL.md in this repo and follow it exactly. Select an Alchemy app first if none is selected. Use only the tools the skill allows. Do not call trace or debug tools.

Input: mined transaction 0xcdca6219c1c3f2e34b9c0a20347a6338219663aa9acd7adb1426fdabda0267d7
Network: eth-mainnet
```

Replace the hash with any transaction hash from Etherscan or your wallet history.

## Optional lines

Add any of these to a prompt when they apply:

- `I am on PAYG, traces and spam filters are allowed.`
- `Network: base-mainnet` (or any id from `list_chains`; the app must have it enabled)
- `Keep the report under 200 words.`
