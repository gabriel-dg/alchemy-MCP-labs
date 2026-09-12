# watch-a-wallet: copy-paste prompts

One block per scenario. Each is self-contained. Open this repo in your agent first so it can read `SKILL.md` by path. Every address below is public and harmless to query. None of these prompts authorizes resource creation or deletion.

## A. Watch a busy public wallet (main path)

The agent previews recent ETH transfers and asks for your receiver. Supply your own public HTTPS receive URL in the conversation; there is no shared endpoint to paste. It then checks webhook availability. Review the exact proposal and explicitly consent if you want to create it. Keep the receiver open for a test payload and the first automatic event, then confirm deletion of the temporary ID.

```text
Read skills/watch-a-wallet/SKILL.md in this repo and follow it exactly. List Alchemy apps and select one; ask me if several exist. Use only the tools the skill allows. Never sign, send, broadcast, or use gas-policy tools. Show the exact proposal and wait for explicit consent before creating any resource. Teardown requires separate explicit confirmation of the temporary webhook ID; never delete a preexisting webhook.

Mode: live
Address: 0x28C6c06298d514Db089934071355E5743bf21d60
Network: eth-mainnet
Minimum ETH: 1
Receiver: ask me
```

## B. A treasury-sized USDC outflow, without a receiver

A fixed historical block from Lab 1 contains a 120,133.877066 USDC outflow from its public demo address. Use it as a treasury-rule rehearsal: would an outflow of at least 100,000 USDC raise an alert? It is not a claim that this wallet is a treasury. The rule checks the canonical contract, not a token calling itself USDC.

```text
Read skills/watch-a-wallet/SKILL.md in this repo and follow it exactly. List Alchemy apps and select one; ask me if several exist. Use only the tools the skill allows. Never sign, send, broadcast, or use gas-policy tools. Do not create, update, or delete any resource. Label this historical replay, not webhook delivery.

Mode: replay
Address: 0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045
Network: eth-mainnet
Token: USDC
Minimum USDC: 100000
From block: 0x17ea710
To block: 0x17ea710
Receiver: none
```

## C. Rehearse a personal deposit alert

The code-less address from Labs 1 to 3 receives mistaken deposits. It is a public demo input, not your wallet. The rule highlights both deposits and withdrawals of at least 0.01 ETH. A quiet window is useful too: the agent must distinguish no returned transfer from proof that a wallet is inactive. Replace the address with yours when you are ready; preview mode sends nothing to a receiver.

```text
Read skills/watch-a-wallet/SKILL.md in this repo and follow it exactly. List Alchemy apps and select one; ask me if several exist. Use only the tools the skill allows. Never sign, send, broadcast, or use gas-policy tools. Do not create, update, or delete any resource. Label the output as a transfer preview, not automatic monitoring.

Mode: preview
Address: 0x1111111111111111111111111111111111111111
Network: eth-mainnet
Minimum ETH: 0.01
Receiver: none
```

## D. Resume safe cleanup

Paste this in the conversation that created the temporary webhook. In a fresh conversation the agent must ask for the creation record and exact ID before it can establish a safe target. There is deliberately no example ID to accidentally delete.

```text
Read skills/watch-a-wallet/SKILL.md in this repo and follow it exactly. List Alchemy apps and select one; ask me if several exist. Use only the tools the skill allows. Never sign, send, broadcast, or use gas-policy tools. Find only the temporary webhook created by this lab using its creation record. If that record or its exact ID is missing, ask me for it and do not delete anything. Show the exact target and require my explicit confirmation of its ID before deletion. Verify absence afterward and preserve every preexisting webhook.

Mode: teardown
Network: eth-mainnet
```

## Optional lines

- `App:` followed by the exact app name or ID you chose from `list_apps`.
- `Minimum ETH: 10` to rehearse a larger per-transfer threshold; this does not filter what Alchemy delivers.
- Up to three `Address:` lines for wallets you know. One network and one temporary webhook per run.
- `Then call get_usage_summary.` This is account-wide usage, not an isolated measurement of the lab.

For a live personal alert, use scenario A with your address and a privately operated public HTTPS receiver. Do not paste a production webhook secret, signing key or API key. No prompt here installs an email, Slack or background chat notification.
