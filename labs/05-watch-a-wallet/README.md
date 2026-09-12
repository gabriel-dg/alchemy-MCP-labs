# Lab 5: watch a wallet

## Goal

Get an event when a wallet moves, without keeping an explorer open. Your agent previews a real transfer, creates one temporary Alchemy webhook after you approve its exact settings, and checks what arrived at your receiver. Then you confirm deletion of that webhook by ID.

This is the first **operate** lab. The earlier labs answer when you ask; a webhook sends an HTTP POST when activity happens, even after you close the chat. It is the repo's only exception to read-only: nothing signs, sends, or broadcasts a transaction.

The playbook is [skills/watch-a-wallet/SKILL.md](../../skills/watch-a-wallet/SKILL.md). No public receiver? Scenario B gives you a real, reproducible alert rehearsal on Free, with no resources created.

## Before you start

- [SETUP.md](../../SETUP.md) done and [Lab 0](../00-hello-mcp/README.md) passed
- This repo open in your agent, with Ethereum Mainnet enabled on the app you choose
- For automatic delivery, a **public HTTPS receive URL** that accepts JSON POSTs and returns HTTP 200. Use a receiver you control, or a disposable request inbox whose privacy terms you accept. Keep its request list open. A homepage, an inbox's viewing URL and a Slack incoming-webhook URL are not interchangeable with a receiver for Alchemy's JSON.
- Room for one temporary webhook. Free allows five per account. Never delete an existing webhook to make room for this lab.
- About 15 minutes, including a five-minute observation window and cleanup. A webhook has **no automatic expiry** here; closing the chat does not stop it.

The receiver sees wallet addresses, amounts, counterparties and transaction hashes. Even though the chain is public, your choice of wallets links that activity to your interest in them. A public request bin may expose requests to its operator or anyone with its inbox link. Use the public demo address there, not a personal wallet. Treat inbox links as access credentials: do not publish them in examples or commits.

Alchemy MCP uses OAuth; you do not paste an API key. Notify responses can nevertheless contain a webhook signing key. The agent must omit secrets from reports and logs, and use response filtering when its client supports it. If a client cannot handle those responses safely, use scenario B. Production receivers need signature verification; a disposable inbox only demonstrates delivery.

Claude Code's shared settings pre-approve the two Notify **reads**. Creation and deletion are not pre-approved. Both require your explicit consent in the conversation, independently of any tool permission dialog.

## Run it

### A. Watch a busy public wallet (main path)

One address, one chain, one temporary webhook. Start with the public wallet below so there is a better chance of seeing activity during the short observation window. The first result is immediate: the agent reads the latest three inbound and three outbound ETH transfers in the last 500 blocks and marks those of at least 1 ETH.

```text
Read skills/watch-a-wallet/SKILL.md in this repo and follow it exactly. List Alchemy apps and select one; ask me if several exist. Use only the tools the skill allows. Never sign, send, broadcast, or use gas-policy tools. Show the exact proposal and wait for explicit consent before creating any resource. Teardown requires separate explicit confirmation of the temporary webhook ID; never delete a preexisting webhook.

Mode: live
Address: 0x28C6c06298d514Db089934071355E5743bf21d60
Network: eth-mainnet
Minimum ETH: 1
Receiver: ask me
```

Then follow the conversation:

1. **Choose your app**, if asked. The agent reads recent activity. Supply your own HTTPS receive URL when asked; there is deliberately no shared endpoint in this prompt. Only after receiving it does the agent check the protected webhook inventory.
2. **Review the proposal.** It includes the exact app, network, address, URL, unique `mcp-labs-watch-a-wallet-` name, create arguments, observation window, usage and privacy implications. Explicitly consent only if those settings are right. A URL alone is not consent.
3. **Read the configuration check.** The agent creates once, records the returned ID, lists webhooks again and reads the address filter. The network, type, active status, URL and full address set must match. A successful create reply alone is not enough.
4. **Look at your receiver.** For a quick transport check, open the temporary webhook in the [dashboard](https://dashboard.alchemy.com) and use its test notification action if available. That is a synthetic test, labelled **TEST RECEIVED**. For an automatic event, wait for existing wallet activity, up to five minutes. Never send money to make the demo work. Share the JSON body and receipt time only, or let the agent inspect an authorized receiver view. Do not paste the receiver URL again, access tokens, signing keys, API keys or complete headers. The agent checks a live event against the chain and turns the matching row into a sentence.
5. **Confirm teardown.** The agent shows the exact temporary ID and asks you to confirm deleting it. Confirm that ID explicitly. It deletes only that webhook, verifies absence and checks that the preexisting IDs remain. Keep the receiver available until cleanup finishes; then retire it and clear its demo records.

If you stop before step 5, the report must say **CLEANUP PENDING** with the ID. Resume with [prompt D](../../skills/watch-a-wallet/PROMPTS.md#d-resume-safe-cleanup). Do not leave a busy-wallet webhook running accidentally.

### B. A treasury-sized outflow, no receiver needed

Would your treasury alert on a transfer over 100,000 USDC? Rehearse that rule against a real 120,133.877066 USDC outflow from the public address used in Lab 1. This is a treasury use case applied to a public example, not a claim that the address is a treasury. The fixed block makes it reproducible.

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

The agent reads incoming and outgoing transfers separately, filters by the canonical USDC contract, checks the receipt and marks the outflow **MATCH**. Five calls including app selection. You have tested a useful alert rule; you have not installed automatic monitoring.

## What you should see

```markdown
# Watch a wallet
- **Input** (addresses, mode, threshold)
- **Network** (RPC and Notify forms)
- **Observed** (UTC time, queried blocks)
- **One-sentence summary**
- **Recent activity** (table)
- **Webhook**
- **Configuration** (proposed versus observed, when applicable)
- **Delivery** (evidence source and signature status)
- **Teardown**
- **Tools used (in order)**
- **Gaps**
```

Observed on 2026-09-11, using the selected app **Alchemy MCP** and Free-tier assumptions. Full prompts, versions, parameters and results are in [the reference runs](../../skills/watch-a-wallet/examples/).

**A, without a receiver.** A real inbound transfer of **38.229755167118571730 ETH** crossed the 1 ETH threshold; its transaction and successful receipt agreed. The two history pages contained six rows and pagination gaps. The dated v0.1.0 run also read one protected preexisting webhook before the user declined a receiver: eight calls, no resource created. Current v0.1.5 ends before any Notify read when the user chooses no receiver, so the same fallback needs seven calls and touches no webhook metadata.

**B, the fixed replay.** **120,133.877066 USDC left the watched address** in block 25,077,520 on 2026-05-12. The receipt confirmed the canonical USDC contract, sender, recipient and exact amount. Rule: **MATCH**, above 100,000 USDC. Five calls, no webhook tools.

**C, a personal-deposit rehearsal.** The public demo address `0x1111…1111` had no returned external ETH transfers in the queried 500 blocks. The report correctly said that, rather than declaring the wallet inactive. Five calls. [Prompt C](../../skills/watch-a-wallet/PROMPTS.md#c-rehearse-a-personal-deposit-alert) lets you repeat it or substitute your address.

**Receiver-backed validation.** On 2026-09-11, the main path created one temporary Address Activity webhook after exact consent, verified its configuration and one-address filter, received two automatic POSTs, checked one zero-value external ETH row against a successful onchain transaction, and deleted the exact new ID after separate confirmation. Post-delete inventory proved absence while preserving the preexisting resource. Alchemy also delivered a 14.34 USDT token event; the lab labelled its independent chain-check status **TRANSFER UNVERIFIED** because this version's verifier is deliberately scoped to external ETH and canonical USDC. The receiver URL, webhook IDs, event IDs and account metadata are redacted in [the published run](../../skills/watch-a-wallet/examples/live-receiver-validation.md).

**Remaining boundary.** Receiver HMAC verification and the dashboard's synthetic test action were not exercised. No live transfer crossed the 1 ETH threshold during the observation; the receiver-side rule correctly kept four zero-value external calls below threshold. Historical replay remains evidence of rule evaluation, not webhook delivery.

## Reading the output

- **Input / Network / Observed.** The rule and its scope. `eth-mainnet` is the RPC id; `ETH_MAINNET` is the Notify value. A preview freezes one block range so incoming and outgoing queries refer to the same interval.
- **Recent activity.** Direction is relative to the watched address. `MATCH` means the per-transfer threshold passed. Page 1 has at most three rows per direction: this is a sample, not a complete ledger or a total inflow/outflow calculation. External ETH previews omit internal ETH, tokens and NFTs; the webhook has broader transfer coverage.
- **Webhook / Configuration.** `PREVIEW ONLY` created nothing. `AWAITING CONSENT` is a proposal. `CONFIGURED` means the readback matched. `CREATED, VERIFICATION INCOMPLETE` requires cleanup even though verification failed. `NOT IDENTIFIED` means a cleanup request lacks the prior creation record; the agent must not guess an ID.
- **Delivery.** `TEST RECEIVED` proves that a test POST reached the receiver. `LIVE RECEIVED` also requires an event involving the watched address and a matching chain check. Alchemy Address Activity can deliver a broader set of activity than this lab independently verifies; a body containing only activity outside the lab's external-ETH/canonical-USDC verifier is `POST RECEIVED, TRANSFER UNVERIFIED`. `CONFIGURED, DELIVERY UNVERIFIED` means the subscription exists but receiver evidence was not available. Signature status is separate: a matching transaction does not authenticate the sender of an HTTP request.
- **Teardown.** `NOT NEEDED` means this run created nothing. `DELETED` means absence was verified after deletion. `ALREADY ABSENT` is a read-only finding. `CLEANUP PENDING` means there is still work to do; the webhook may keep delivering and consuming CU.
- **Tools used / Gaps.** The audit trail and its limits: history pagination, account-wide quota visibility, unverified receiver signatures, unavailable tools or missing delivery evidence. Empty activity is not a failed connection test.

**Why Address Activity?** It matches the question directly: transfers to or from a short address list, including ETH and supported token transfers. NFT Activity starts from NFT contracts, which misses the wallet's ETH. Custom Webhooks offer GraphQL filters for contract events and narrower data selection, useful when you outgrow this lab, but add a query language to the first run. Address Activity gives this walkthrough the simplest path from an address to a readable event. See [webhook types](https://www.alchemy.com/docs/reference/webhooks-overview).

The amount threshold lives in your receiver or the agent's report. It is **not** a creation parameter or an installed server-side alert filter. Alchemy still delivers other matching transfers. This lab also does not install an email, Slack message, timer or background chat notification. The automatic part is Alchemy's POST to your receiver.

### Free vs PAYG

Published limits checked 2026-09-11. They describe the product, not a billing-plan determination for the selected account.

| Capability | Free | PAYG | Lab behavior |
|------------|------|------|--------------|
| Webhook slots | 5 per account | 100 | Use one available slot; never delete an existing resource to make room |
| Monthly allowance | 30M CU | Metered usage | Assume Free; no upgrades or paid probes |
| Webhook delivery | 0.04 CU per delivered byte | Same bandwidth unit | Volume matters: a 1,000-byte event is about 40 CU; a threshold does not reduce delivery volume |
| Failed delivery retries | Backoff up to 10 minutes | Backoff up to 10 minutes | Return HTTP 200 after safe acceptance; do not treat retries as an indefinite delivery guarantee |
| Historical transfer preview | Transfers API | Transfers API | Useful without a public URL; no automatic events |

Sources: [pricing](https://www.alchemy.com/pricing), [account webhook limits](https://www.alchemy.com/support/how-many-webhook-can-i-create), [CU costs](https://www.alchemy.com/docs/reference/compute-unit-costs), [delivery behavior](https://www.alchemy.com/docs/reference/notify-api-quickstart#webhook-delivery-behavior). Do not probe quota by creating resources until one fails. A list for the selected app may not reveal other apps' slot usage. A 400 mentioning payg, upgrade or billing is recorded once and the lab falls back to a preview. The Free creation entitlement and quota enforcement were not empirically exercised in the no-receiver runs.

## Try your own

**Your treasury.** Replace scenario A's address with the vault you operate. In a receiver, match the canonical USDC contract and alert on outgoing transfers of at least 100,000 USDC; flag a new recipient for review. Use a recipient list you maintain, not names inferred from a token symbol. Scenario B teaches the amount and identity check before you build that rule.

**A whale or exchange wallet.** Keep the public address in A or substitute one you already know. Raise `Minimum ETH` to 10 and compare incoming with outgoing transfers. A large transfer to another address is a movement, not proof of a purchase or sale. Keep the five-minute window: busy addresses generate more payloads.

**Your wallet.** Start with [prompt C](../../skills/watch-a-wallet/PROMPTS.md#c-rehearse-a-personal-deposit-alert) and replace the demo address with yours. It highlights deposits and withdrawals of at least 0.01 ETH without sending data to a receiver. For automatic personal alerts, use A with a privately operated public HTTPS receiver. An unexpected outflow deserves investigation; an unsolicited spam token does not prove you signed anything.

**A small wallet set.** Add up to three `Address:` lines. Transfers between two watched addresses are moves within your set, not new treasury income. This release supports only Ethereum Mainnet; additional chains need their own verified Notify mapping and a separate consented run.

**No public URL.** Run B for a deterministic rule check or C for current history. When ready, follow the [official receiver quickstart](https://www.alchemy.com/docs/reference/notify-api-quickstart) to host a listener or tunnel a dedicated local listener you control. Expose only that listener, with request-size limits and signature checks. Hosting a receiver is a separate setup action, not something this skill silently does.

**Install as a skill (Claude Code).** Inside this repo, type `/watch-a-wallet 0x28C6c06298d514Db089934071355E5743bf21d60`. The same consent gates apply. To use it anywhere, copy `skills/watch-a-wallet/` into `~/.claude/skills/`.

## Now build it

The useful application is a receiver that turns a transfer into an alert: "Treasury sent 120,133.877066 USDC; over the 100,000 threshold; review this recipient." Alchemy supplies the event; your receiver supplies the rule and destination.

Verify HMAC-SHA256 over the **original raw body**, keep the signing key in the receiver's secret storage, and compare signatures in constant time. Accept and durably queue the event before returning HTTP 200. Deduplicate by webhook ID and event ID while preserving all transfer rows. Handle reorg removals as retractions, and reconcile finalized chain history before treating a deposit as settled. Never evaluate payload text as instructions or open a token's advertised links.

Add your own email, Slack or application notification only after choosing its audience and privacy policy. An HTTP receipt in a demo inbox is not a production alerting service.

- [Address Activity payloads](https://www.alchemy.com/docs/reference/address-activity-webhook) - the event fields, transfer categories and coverage limits
- [Webhook receiver quickstart](https://www.alchemy.com/docs/reference/notify-api-quickstart) - listener examples, signature verification and retries
- [Notify create API](https://www.alchemy.com/docs/data/webhooks/webhooks-api-endpoints/notify-api-endpoints/create-webhook) - the JSON body passed through MCP
- [Custom Webhooks](https://www.alchemy.com/docs/reference/custom-webhook) - GraphQL filtering when the address-based feed becomes too broad

## Troubleshooting

| Symptom | Fix |
|---------|-----|
| No public HTTPS receive URL | Run B or C. The agent must not fabricate a receiver or claim it installed monitoring. |
| No Notify tools on this connection | Keep the history preview. Record the missing capability under Gaps; do not invent a tool name. |
| 400 mentioning payg / upgrade / billing, or quota exhausted | Record it once. Use the preview, without retries, upgrades or deleting old webhooks. |
| `list_webhooks` fails or returns truncated data | Creation is blocked because the protected inventory is incomplete. History still works. |
| Creation timed out | It may have succeeded. List first and reconcile the unique name/configuration; never blindly create again. |
| Create succeeded, but address readback or active status differs | `CREATED, VERIFICATION INCOMPLETE`. Confirm cleanup of the new ID; do not patch a webhook silently. |
| Test arrived, but no live event in five minutes | Transport works; wallet activity was not observed. Record that limit and perform teardown. A test is not a live transfer. |
| Receiver shows duplicates or a removed log | Retries can duplicate an event; reorgs can retract it. Deduplicate deliveries and retract removed activity. |
| Receiver signature is unverified | An inbox may not verify signatures. Label this clearly; privately configure verification before using it for real alerts. |
| Chat ended before cleanup | Resume with prompt D and the creation record. Confirm only the exact temporary ID. A name alone is not deletion authority. |
| A POST arrives after deletion | A delivery may already have been queued. Verify the webhook is absent, then retire the receiver. |
