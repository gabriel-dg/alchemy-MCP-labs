# Receiver-backed validation, 2026-09-11, on skill v0.1.3

This was a collaborative follow-up against the connected Alchemy MCP server, not a fresh-agent cold run. The user supplied a disposable public HTTPS receiver, reviewed the exact proposal and explicitly consented to creation. The run exercised creation, configuration readback, automatic delivery and separately confirmed deletion. Its findings produced skill v0.1.4.

The published record replaces the receiver URL, temporary webhook ID, event IDs and preexisting webhook ID with stable redactions. Exact values were retained privately for identity comparison and the valid creation/deletion consent gates; a mismatched deletion question was rejected. No API key, auth token or signing key was printed, persisted or written to the repository. This is a summarized **Call log**, not a Raw call log.

## Input and proposal

- **App:** Alchemy MCP (`dhp14gyqfwvwa12q`), explicitly chosen by the user
- **Address:** `0x28C6c06298d514Db089934071355E5743bf21d60` — public demo input
- **Network:** `eth-mainnet` (Notify: `ETH_MAINNET`)
- **Minimum ETH:** 1 per transfer
- **Receiver:** `https://webhook.site/[redacted-disposable-receiver]`
- **Name:** `mcp-labs-watch-a-wallet-20260911T193715Z`
- **Observation:** five minutes or first live event
- **Protected inventory:** one preexisting webhook, ID redacted

The exact private proposal contained the real receive URL. The user answered, "go ahead then," while referring to that proposal. A pasted URL alone was not treated as consent.

Published create arguments:

```json
{
  "app_id": "dhp14gyqfwvwa12q",
  "body": {
    "network": "ETH_MAINNET",
    "webhook_type": "ADDRESS_ACTIVITY",
    "webhook_url": "https://webhook.site/[redacted-disposable-receiver]",
    "name": "mcp-labs-watch-a-wallet-20260911T193715Z",
    "addresses": [
      "0x28C6c06298d514Db089934071355E5743bf21d60"
    ]
  }
}
```

## Result

# Watch a wallet

- **Input:** one public address, live mode, minimum 1 ETH
- **Network:** eth-mainnet (Notify: ETH_MAINNET)
- **Observed:** 2026-09-11 UTC; preview blocks `0x18c0dd2` to `0x18c0fc5`; live events in blocks `0x18c0ff4` and `0x18c0ff6`
- **One-sentence summary:** Alchemy automatically delivered activity for the watched wallet; four zero-value external calls stayed below the 1 ETH rule, one was verified against Ethereum, and the temporary webhook was safely removed.
- **Webhook:** CONFIGURED, ID `wh_[redacted-temporary]`
- **Configuration:** exact app, network, type, endpoint and one-address set matched; active status was true
- **Delivery:** LIVE RECEIVED from user-reported receiver evidence; signature not verified
- **Teardown:** DELETED; exact target absent after deletion; one protected preexisting webhook remained
- **Gaps:** the receiver receipt timestamp and HMAC evidence were not supplied, the dashboard synthetic test was not used, no live row crossed 1 ETH, history pages were incomplete, and account tier/capacity remained unverified

### Preview

| When UTC | Direction | Asset | Amount | Hash | Rule |
|---|---|---|---:|---|---|
| 2026-09-11 19:35:59 | inbound | ETH | 0.3500415305753307 | `0x84770b42861fe54b41eee0c7a35c372584eeafbe56a760bbb5ebd377abb5e00d` | below threshold |
| 2026-09-11 19:35:59 | inbound | ETH | 0.1896729125640206 | `0x6a6d08cd65b2845ad501374d6d6e410c6b3b57b8b5b648006d415a51b2906b11` | below threshold |
| 2026-09-11 19:35:59 | inbound | ETH | 0.1492418680277760 | `0x67b5a6a45728b14017fdf37a6323efa40bc3437bfda299d094af8c3bccaec567` | below threshold |
| 2026-09-11 19:35:23 | outbound | ETH | 3.1009259 | `0x1c8f552d0c73d03eabf4c759385bb5e69daf49309e0d8f97eb4b0da414395ab0` | MATCH |
| 2026-09-11 19:34:47 | outbound | ETH | 0.03791818 | `0xf014307ba9b99c65f8519f19d6742c91eb0e6431b544d9c31186ec73cc05b23c` | below threshold |
| 2026-09-11 19:34:11 | outbound | ETH | 0.03046407 | `0xa0ad86cd09b7818f52c935c33ebcb00557222b0da70ec8fa86143c3c753bdd5a` | below threshold |

Both transfer calls returned a `pageKey`; the six rows are not complete history. The matching 3.1009259 ETH outflow was verified with transaction value `0x2b08b3ad384ef800`, matching sender and recipient, block `0x18c0fbf`, and receipt status `0x1`.

### Automatic deliveries

The first POST was reported received by the user; its payload carried `createdAt: 2026-09-11T19:46:03.708Z`. It matched the private temporary webhook ID, `ADDRESS_ACTIVITY`, `ETH_MAINNET` and the watched address. It contained four external ETH rows with raw value `0x0`, so every row was below the 1 ETH rule. The deterministic row chosen for chain verification was:

```json
{
  "webhookId": "wh_[redacted-temporary]",
  "id": "whevt_[redacted-live-event]",
  "type": "ADDRESS_ACTIVITY",
  "event": {
    "network": "ETH_MAINNET",
    "activity": [{
      "fromAddress": "0x28c6c06298d514db089934071355e5743bf21d60",
      "toAddress": "0xee7ae85f2fe2239e27d9c1e23fffe168d63b4055",
      "blockNum": "0x18c0ff4",
      "hash": "0x2f9955b487f690fc059afeb798938e203385e2ba77d1aba7eb6bbc1a639750da",
      "value": 0,
      "asset": "ETH",
      "category": "external",
      "rawContract": {"rawValue":"0x0","decimals":18}
    }]
  }
}
```

`ethGetTransactionByHash` confirmed the same sender, recipient, block and zero value. `ethGetTransactionReceipt` returned the same hash and block with `status: "0x1"`. This is a verified live zero-value contract interaction, not a deposit or a 1 ETH alert.

The second POST was also reported received; its payload carried `createdAt: 2026-09-11T19:46:25.078Z`, one 14.34 USDT token row in block `0x18c0ff6`, transaction `0xd8693f3eae23b1bbe2e95d74f538509403d94dc0528f0f87929e65303d9c3165`, and `removed: false`. Alchemy correctly delivered this ERC-20 activity. The lab labelled its independent chain-check status `POST RECEIVED, TRANSFER UNVERIFIED` because its compact verifier covers external ETH and canonical USDC, not arbitrary tokens. `removed: false` was not presented as finality proof.

### Safe recovery and teardown

Alchemy's create and first readback calls succeeded. A local result-formatting typo prevented the agent's summary from rendering; this was a presentation issue, not an API or delivery failure. Creation was not retried. A fresh list found exactly one new resource with the approved unique name, endpoint, network and type; the address read confirmed the exact one-address set. This safely reconciled the result without risking a duplicate webhook.

The agent's first deletion question accidentally displayed an ID different from the creation record; Alchemy had returned and retained the correct resource identity. The user's affirmative reply was rejected as authority for the real resource, and no delete call occurred. The agent re-read the creation record and live configuration, asked again with the exact underlying ID, and received explicit confirmation. The public record redacts both values rather than preserving the erroneous or real identifiers.

`delete_webhook` then succeeded. A final complete `list_webhooks` response did not contain the temporary ID and still contained the one protected preexisting ID.

## Call log

Twenty Alchemy MCP calls were made. Results below are summarized and sensitive Notify fields are redacted.

1. `list_apps` with `{}`: chosen app was present; other app metadata omitted.
2. `select_app` with `{"app_id":"dhp14gyqfwvwa12q"}`: selection succeeded; no key value was returned or retained.
3. `ethBlockNumber` with `{"network":"eth-mainnet"}`: `0x18c0fc5`.
4. `getAssetTransfers` inbound with the parameters below: three rows and a `pageKey`.
5. `getAssetTransfers` outbound with the parameters below: three rows and a `pageKey`.

```json
{
  "network": "eth-mainnet",
  "category": ["external"],
  "order": "desc",
  "maxCount": "0x3",
  "withMetadata": true,
  "excludeZeroValue": true,
  "fromBlock": "0x18c0dd2",
  "toBlock": "0x18c0fc5",
  "toAddress": "0x28C6c06298d514Db089934071355E5743bf21d60"
}
```

The outbound call replaced `toAddress` with:

```json
{"fromAddress":"0x28C6c06298d514Db089934071355E5743bf21d60"}
```

6. `ethGetTransactionByHash` with `{"network":"eth-mainnet","transactionHash":"0x1c8f552d0c73d03eabf4c759385bb5e69daf49309e0d8f97eb4b0da414395ab0"}`: sender, recipient and 3.1009259 ETH value matched.
7. `ethGetTransactionReceipt` with the same network and hash: status `0x1`, block `0x18c0fbf`.
8. `list_webhooks` with `{"app_id":"dhp14gyqfwvwa12q"}`: one protected preexisting record; sensitive fields omitted.
9. `list_webhooks` with the same parameters immediately before creation: protected inventory refreshed; one record.
10. `create_webhook` with the redacted proposal shown above: one new ID returned. Safe configuration fields were retained; any signing key was discarded and never displayed.
11. `list_webhooks` with the selected app: the exact new ID was present with matching network, type, endpoint, name and active status.
12. `get_webhook_addresses` with `{"app_id":"dhp14gyqfwvwa12q","webhook_id":"wh_[redacted-temporary]"}`: exactly the one approved address.
13. `list_webhooks` with the selected app after the local formatter failure: one unique new candidate matched the approved configuration.
14. `get_webhook_addresses` for that exact redacted ID: exact one-address set matched again.
15. `ethGetTransactionByHash` with `{"network":"eth-mainnet","transactionHash":"0x2f9955b487f690fc059afeb798938e203385e2ba77d1aba7eb6bbc1a639750da"}`: live row sender, recipient, block and zero value matched.
16. `ethGetTransactionReceipt` with the same network and hash: status `0x1`, matching block and hash.
17. `list_webhooks` with the selected app before the first deletion question: the temporary resource matched its private creation record and the protected record remained.
18. `list_webhooks` again after detecting the displayed-ID mismatch: the correct target was still active and matched; no deletion had occurred.
19. `delete_webhook` with `{"app_id":"dhp14gyqfwvwa12q","webhook_id":"wh_[redacted-temporary]"}` after a new confirmation referring to the exact private ID: succeeded.
20. `list_webhooks` with the selected app: temporary ID absent, protected preexisting ID present.

Calls 4 and 5, 6 through 8, 11 and 12, and 15 and 16 were independent read batches. One local syntax error before calls 15 and 16 made no Alchemy call and is not included. No `update_webhook`, transaction submission, signing, gas-policy, receiver-inbox or test-delivery tool was called.

## What changed in v0.1.4

- A missing or declined receiver now ends before `list_webhooks`, reducing access to unrelated account metadata.
- Published examples explicitly redact receiver URLs, webhook IDs and event IDs while using exact values privately for verification and consent.
- A deletion reply is invalid when the displayed or referenced ID differs from the creation record; the agent must re-read and ask again.
- Receiver instructions now say exactly which body fields are useful and which credentials or headers must not be pasted.
