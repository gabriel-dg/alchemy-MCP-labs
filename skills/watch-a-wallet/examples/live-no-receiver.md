Cold run, 2026-09-11 UTC, on skill v0.1.0

# Scenario A: live mode without a receiver

Live Alchemy MCP cold run; no fixture or scripted RPC responses. Run observation interval: 2026-09-11 14:35:33–14:36:09 UTC. The supplied interaction answers below were available before execution. All exposed schemas for the eight Alchemy calls were inspected before use.

## Verbatim prompt

```text
Read skills/watch-a-wallet/SKILL.md in this repo and follow it exactly. List Alchemy apps and select one; ask me if several exist. Use only the tools the skill allows. Never sign, send, broadcast, or use gas-policy tools. Show the exact proposal and wait for explicit consent before creating any resource. Teardown requires separate explicit confirmation of the temporary webhook ID; never delete a preexisting webhook.

Mode: live
Address: 0x28C6c06298d514Db089934071355E5743bf21d60
Network: eth-mainnet
Minimum ETH: 1
Receiver: ask me
```

## Interaction answers and consent decisions

- **App:** Alchemy MCP (`dhp14gyqfwvwa12q`).
- **Receiver:** “No public receiver — validate the fallback and consent gates.”
- **Authorization:** No resource creation authorized. No creation proposal or creation-consent request was presented because a receiver was unavailable. No additional receiver question was necessary after the supplied answer.
- **Deletion consent:** Not requested; this run created no resource. The preexisting webhook was protected and untouched.

# Watch a wallet

- **Input:** `0x28C6c06298d514Db089934071355E5743bf21d60`; mode `live`; threshold at least 1 ETH per external transfer.
- **Network:** eth-mainnet (Notify: ETH_MAINNET).
- **Observed:** 2026-09-11 14:35:33–14:36:09 UTC; frozen range `0x18c07f9`–`0x18c09ec` (25,954,297–25,954,796), 500 blocks inclusive.
- **One-sentence summary:** An inbound 38.22975516711857173 ETH transfer at 14:32:23 UTC matched the 1 ETH rule and was verified against its successful receipt and transaction; this is transfer history, not a delivered alert.
- **Recent activity:**

| When UTC | Direction | Asset | Amount | Hash | Rule |
|---|---|---|---:|---|---|
| 2026-09-11T14:32:23.000Z | inbound | ETH | 38.22975516711857173 | `0xeb93899c763935252fd96af18aa763901dc1d457a7567709d051c3bd24800811` | MATCH |
| 2026-09-11T14:32:23.000Z | inbound | ETH | 16.272238630735622501 | `0xd6adc46bcd4a881e0db76e57058b0fe69ea903706b9c14b3559208ed422b284a` | MATCH |
| 2026-09-11T14:32:23.000Z | inbound | ETH | 13.704047157289530302 | `0xb48ecd49d55dcb415be58eb759dea8a591b1b44713447413c64d0700a1fc70b3` | MATCH |
| 2026-09-11T14:35:35.000Z | outbound | ETH | 0.01103283 | `0xea93ef092c8083094d0a285fed2508d993520d02bcfe110d9bc4e1a71ecc10b0` | below threshold |
| 2026-09-11T14:34:35.000Z | outbound | ETH | 0.18978731 | `0xc2ea4e6711a2879208da7f26232502473e758113bdd6aa140cf314e0366f0f86` | below threshold |
| 2026-09-11T14:34:35.000Z | outbound | ETH | 0.00445395 | `0x9d0e51d3df3b1bb8fe3033fd0f7740c53ea2c4c33cb8e7842e5c1ea03eee55a5` | below threshold |

Amounts use exact integer raw values divided by 10^18, rather than rounded floating-point display values. All six uniqueIds were distinct; none involved a self-transfer or another watched address. The first matching row in the inbound response was selected for verification. Its receipt had `status: "0x1"`, and transaction sender, recipient, block and raw value agreed with the transfer response. This proves mined success, not finality.

- **Webhook:** PREVIEW ONLY. A receiver must be a public HTTPS endpoint the user controls or accepts that receives Alchemy Address Activity POSTs. No receiver was supplied, so no exact creation arguments or configuration proposal exists. The receiver applies the 1 ETH threshold after delivery; it is not a webhook creation filter.
- **Delivery:** NOT TESTED. No receiver evidence; signature status `not verified`. No automatic chat, email or Slack monitoring was installed.
- **Teardown:** NOT NEEDED. No webhook created or deleted.
- **Tools used (in order):** list_apps → select_app → ethBlockNumber → getAssetTransfers (2, parallel direction reads) → ethGetTransactionReceipt and ethGetTransactionByHash (parallel verification reads) → list_webhooks.
- **Gaps:** Both transfer responses returned pageKey, so these six rows are incomplete history; no pagination was attempted. Only external ETH was queried. One preexisting webhook ID was returned; the retained projection does not independently establish inventory completeness or account-wide capacity. The Free plan is assumed, not verified. No receiver, signature or delivery evidence exists.

The protected preexisting set observed in this run contained one redacted ID. The returned record was active, type ADDRESS_ACTIVITY, network SOLANA_DEVNET; it was not a resource created by this eth-mainnet run. No claim about remaining account-wide slots follows from this app-scoped inventory.

## Call log

Parameters below are complete for every Alchemy MCP call. Results are summarized; receiver URLs, signing keys and unrelated private account metadata are omitted. list_webhooks was parsed inside the JavaScript orchestration call and only an allowlisted projection was displayed or retained; its raw response was never printed or saved. Local file reads, schema discovery and UTC clock reads are orchestration, not Alchemy calls.

### 1. list_apps

```json
{}
```

Result: success; three apps returned, including Alchemy MCP (`dhp14gyqfwvwa12q`). Other app names and IDs are redacted here as unrelated account metadata. No pagination cursor was shown. The supplied user choice selected Alchemy MCP.

### 2. select_app

```json
{"app_id":"dhp14gyqfwvwa12q"}
```

Result: selected app “Alchemy MCP”; response reported the API key cached, without exposing its value.

### 3. ethBlockNumber

```json
{"network":"eth-mainnet"}
```

Result: `"0x18c09ec"`. Local integer computation subtracted 499 to obtain `fromBlock: "0x18c07f9"`; this toBlock was frozen for both queries.

### 4. getAssetTransfers — inbound

```json
{
  "network": "eth-mainnet",
  "category": [
    "external"
  ],
  "order": "desc",
  "maxCount": "0x3",
  "withMetadata": true,
  "excludeZeroValue": true,
  "fromBlock": "0x18c07f9",
  "toBlock": "0x18c09ec",
  "toAddress": "0x28C6c06298d514Db089934071355E5743bf21d60"
}
```

Result: success; three external ETH transfers, all in block `0x18c09dc` at 2026-09-11T14:32:23.000Z. Full hashes, exact amounts and rule results are the three inbound rows above. The recipient was the watched address. In response order:

| Sender | rawContract.value |
|---|---|
| `0x1f68053cd925d467852cfecf78bd836f33f0e4a1` | `0x2128b5fc64a41b8d2` |
| `0x86a067030a9668c13ff2a8c4d5415afc776d4c63` | `0xe1d29ad21562b165` |
| `0x06fd4ba7973a0d39a91734bbc35bc2bcaa99e3b0` | `0xbe2e8e484b71b3be` |

Each rawContract had `decimal: "0x12"` and `address: null`; each uniqueId was its full transaction hash plus `:external`. Returned pageKey: `6484ddf1-fd61-4552-9b75-3ca55243065b`; not followed.

### 5. getAssetTransfers — outbound

```json
{
  "network": "eth-mainnet",
  "category": [
    "external"
  ],
  "order": "desc",
  "maxCount": "0x3",
  "withMetadata": true,
  "excludeZeroValue": true,
  "fromBlock": "0x18c07f9",
  "toBlock": "0x18c09ec",
  "fromAddress": "0x28C6c06298d514Db089934071355E5743bf21d60"
}
```

Result: success; three external ETH transfers, all below threshold, with hashes, exact amounts and timestamps in the outbound rows above. The sender was the watched address. In response order:

| Recipient | Block | rawContract.value |
|---|---|---|
| `0xb45589de314cbf00169d9a72bf7ed689e8fd847e` | `0x18c09ec` | `0x27324ce9046c00` |
| `0xd387ff2044de1bd9d1022e3f3b2f4a0a9420f56b` | `0x18c09e7` | `0x2a2428d8b6d4c00` |
| `0x9c71fc2560f369a5b416fec7ed2052c7029bc706` | `0x18c09e7` | `0xfd2d80b98ec00` |

Each rawContract had `decimal: "0x12"` and `address: null`; each uniqueId was its full transaction hash plus `:external`. Returned pageKey: `efe51eec-6b51-4eb3-9b8c-24da7a97b8db`; not followed.

### 6. ethGetTransactionReceipt

```json
{
  "network": "eth-mainnet",
  "transactionHash": "0xeb93899c763935252fd96af18aa763901dc1d457a7567709d051c3bd24800811"
}
```

Result: success; `status: "0x1"`, block `0x18c09dc`, block hash `0xcc9c85db7f65beae4a356ee74b83d3ae70682eb75a18fcf15f22f40899da81e0`, transaction index `0x9a`; from `0x1f68053cd925d467852cfecf78bd836f33f0e4a1` to the watched address. `gasUsed: "0x5208"`; `logs: []`. Transaction hash matched the requested hash.

### 7. ethGetTransactionByHash

```json
{
  "network": "eth-mainnet",
  "transactionHash": "0xeb93899c763935252fd96af18aa763901dc1d457a7567709d051c3bd24800811"
}
```

Result: success; hash, block number/hash and transaction index matched the receipt. `chainId: "0x1"`; from `0x1f68053cd925d467852cfecf78bd836f33f0e4a1` to `0x28c6c06298d514db089934071355e5743bf21d60`; `value: "0x2128b5fc64a41b8d2"`; `input: "0x"`. Raw value equals 38.22975516711857173 ETH and matches the selected transfer exactly.

### 8. list_webhooks

```json
{"app_id":"dhp14gyqfwvwa12q"}
```

Safe retained result:

```json
{
  "isError": false,
  "results": [
    {
      "data": [
        {
          "id": "wh_[redacted-preexisting]",
          "network": "SOLANA_DEVNET",
          "webhook_type": "ADDRESS_ACTIVITY",
          "is_active": true
        }
      ]
    }
  ],
  "parseFailures": 0
}
```

One preexisting ID was protected. The allowlist retained ID, network, webhook type and active status. No receiver URL or signing key was displayed or saved. Completeness and account-wide remaining capacity were not established by the retained fields. No mutation followed.

## Cold-run findings and unexercised steps

The missing-receiver fallback was unambiguous and stopped before proposal or creation. Existing user answers avoided redundant app/receiver questions. A minor instruction ambiguity remains: “first matching transfer” does not specify how to merge inbound and outbound response ordering. This run used the first matching inbound row; all returned matches were inbound at the same timestamp.

This run exercised live preview, exact ETH comparison, one chain verification, safe Notify inventory handling and refusal to create without a receiver/consent. It did not exercise concrete proposal approval, create_webhook, creation reconciliation, get_webhook_addresses, configuration readback, test/live delivery, signature verification, timed observation, separate deletion confirmation, delete_webhook or deletion readback. Those behaviors are not claimed validated by this run.
