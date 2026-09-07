> Cold run, 2026-09-07. An agent was given only the Scenario E prompt from `PROMPTS.md` and followed `SKILL.md`. Report reproduced as written, lightly trimmed. The balance changes as people keep sending to this address; the type and assessment do not.

# Contract inspector
- **Address:** 0x1111111111111111111111111111111111111111
- **Network:** eth-mainnet
- **Type:** not a contract
- **Identity:** none. This is a plain externally owned address (or an unused vanity/burn-style address); nothing but a private key stands behind it.
- **Signals**

| Signal | Result |
|--------|--------|
| has code | no (`ethGetCode` returned `0x`) |
| ETH balance | 5,717,180,557,992,809,825 wei, about 5.717 ETH |
| nonce | 0 (has never sent a transaction) |
| proxy, verified source, token metadata, price, activity | not applicable; Step 1 stops for non-contracts |

- **Red flags:** none observed within the scope of this check. Note, however: the address has never sent a transaction yet holds about 5.7 ETH, which is consistent with a burn or vanity address that people send to by mistake (`0x1111…1111` is a well-known pattern of that kind) or an unused key. Funds sent here are likely unrecoverable if no one holds the key.
- **Assessment:** NOT A CONTRACT. Code is `0x`; no contract logic constrains what the holder of the key (if any) can do. If you were about to approve or send to this address, an approval would give whoever controls the key unrestricted power to spend the approved tokens, and a transfer would be irreversible.
- **What to do next:** Confirm with the counterparty that this is the intended recipient and that they actually control it (nonce 0 and a repeating-digit pattern suggest a placeholder or burn address). Do not approve a spender that is an EOA unless you fully trust the key holder.
- **Tools used (in order):** `list_apps`, `select_app`, `ethGetCode`, `ethGetTransactionCount`, `ethGetBalance`
- **Gaps:** Steps 2 to 5 skipped as required for a `0x` code result. Inbound transfer history was not queried in this run; skill v0.1.1 adds one inbound lookup for funded non-contracts.

These heuristics are not financial advice and not a security audit.

## Raw call log

1. `list_apps` → two apps.
2. `select_app` `{"app_id": "…"}` → Selected "<your app>", API key cached.
3. `ethGetCode` `{"network": "eth-mainnet", "address": "0x1111…1111"}` → `"0x"`.
4. `ethGetTransactionCount` same address → `"0x0"`. (Parallel with 5.)
5. `ethGetBalance` same address → `"0x4f57816d33ee5561"`, about 5.717 ETH.

Five calls, about 60 seconds end to end. A local shell command converted wei to ETH.
