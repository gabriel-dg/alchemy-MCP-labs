Cold run, 2026-09-11 UTC, on skill v0.1.0

# Scenario B: historical USDC replay

Executed by a fresh agent against the connected Alchemy MCP server, following `skills/watch-a-wallet/SKILL.md` version `0.1.0`. This is an actual historical query run, not a scripted or fixture-based check. Observed between 14:35:04 and 14:35:47 UTC. Creation, configuration readback, receiver delivery, signature verification and deletion were not exercised. No resource creation was authorized.

## Verbatim prompt

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

**App-choice interaction answer supplied by the user:** `Alchemy MCP (dhp14gyqfwvwa12q)`. The answer was available before selection; no additional app-choice pause was necessary. No creation or deletion consent was requested or granted.

## Report

# Watch a wallet

- **Input:** `0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045`; mode `replay`; canonical USDC; minimum 100,000 USDC per transfer; receiver `none`.
- **Network:** eth-mainnet (Notify: ETH_MAINNET).
- **Observed:** 2026-09-11 14:35 UTC; historical range `0x17ea710` through `0x17ea710` (25,077,520; one block inclusive).
- **One-sentence summary:** Historical replay: the watched address sent 120,133.877066 canonical USDC to `0x3c7779d27348017415a9184acadc0d62052841ab`, exceeding the 100,000 USDC rule; the successful receipt verifies this transfer.
- **Recent activity:**

| When UTC | Direction | Asset | Amount | Hash | Rule |
|---|---|---|---:|---|---|
| 2026-05-12 07:24:11 | Outbound | USDC | 120,133.877066 | `0xcdca6219c1c3f2e34b9c0a20347a6338219663aa9acd7adb1426fdabda0267d7` | MATCH |

- **Verification:** `ethGetTransactionReceipt` returned `status: "0x1"`. Its log at index `0x214` (532) matches canonical USDC contract `0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48`, the ERC-20 Transfer topic, indexed sender and recipient, and amount `0x1bf8897d4a` = 120,133,877,066 base units. Dividing by 10^6 gives exactly 120,133.877066 USDC. The integer amount exceeds 100,000,000,000 base units. The log reports `removed: false`; this alone is not a finality guarantee.
- **Webhook:** PREVIEW ONLY. Historical replay only; no Notify calls or resources.
- **Delivery:** NOT TESTED. Evidence source is Alchemy transfer history and transaction receipt, not a receiver. Signature status: not verified.
- **Teardown:** NOT NEEDED.
- **Tools used (in order):** list_apps; select_app; getAssetTransfers (2, inbound and outbound in one independent batch); ethGetTransactionReceipt.
- **Gaps:** Only canonical USDC in one historical block was queried, page 1 with at most three rows per direction. Responses contained no `pageKey`; inbound was empty and outbound contained one row. This does not describe other blocks, assets or current activity. Account plan/capacity and receiver delivery/signature were not checked in replay mode. No automatic monitoring or scheduling was installed.

The wallet is a public demonstration input; this rule rehearsal does not establish that it is a treasury. Token identity was checked by contract address, not its symbol. One unique transfer row was returned, identified by its `uniqueId`; no duplicate or self-transfer required adjustment.

## Call log

All five Alchemy MCP invocations appear below with exact non-secret parameters and summarized results. Other apps' names and IDs are redacted as private account metadata. No receiver URL, API key, signing key or token is included. The exposed schemas for these four tool names were inspected before use. The call log is summarized, not an unmodified JSON transcript.

### 1. list_apps

Parameters:

```json
{}
```

Result summary: three apps returned, including `Alchemy MCP` with ID `dhp14gyqfwvwa12q`; the other two app names and IDs are redacted. No continuation cursor was returned. The supplied interaction answer selects Alchemy MCP.

### 2. select_app

Parameters:

```json
{"app_id":"dhp14gyqfwvwa12q"}
```

Result: selected app `Alchemy MCP` (`dhp14gyqfwvwa12q`); API key cached by the server. No key was returned in the response text.

### 3. getAssetTransfers — inbound

Parameters:

```json
{
  "network": "eth-mainnet",
  "category": ["erc20"],
  "contractAddresses": ["0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48"],
  "order": "desc",
  "maxCount": "0x3",
  "withMetadata": true,
  "excludeZeroValue": true,
  "fromBlock": "0x17ea710",
  "toBlock": "0x17ea710",
  "toAddress": "0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045"
}
```

Result summary: `transfers: []`; no `pageKey`. No inbound canonical USDC transfer returned in the specified block.

### 4. getAssetTransfers — outbound

Parameters:

```json
{
  "network": "eth-mainnet",
  "category": ["erc20"],
  "contractAddresses": ["0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48"],
  "order": "desc",
  "maxCount": "0x3",
  "withMetadata": true,
  "excludeZeroValue": true,
  "fromBlock": "0x17ea710",
  "toBlock": "0x17ea710",
  "fromAddress": "0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045"
}
```

Result summary: one transfer, no `pageKey`:

```json
{
  "blockNum": "0x17ea710",
  "uniqueId": "0xcdca6219c1c3f2e34b9c0a20347a6338219663aa9acd7adb1426fdabda0267d7:log:532",
  "hash": "0xcdca6219c1c3f2e34b9c0a20347a6338219663aa9acd7adb1426fdabda0267d7",
  "from": "0xd8da6bf26964af9d7eed9e03e53415d37aa96045",
  "to": "0x3c7779d27348017415a9184acadc0d62052841ab",
  "value": 120133.877066,
  "asset": "USDC",
  "category": "erc20",
  "rawContract": {
    "value": "0x1bf8897d4a",
    "address": "0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48",
    "decimal": "0x6"
  },
  "metadata": {"blockTimestamp":"2026-05-12T07:24:11.000Z"}
}
```

Calls 3 and 4 were initiated together, within the four-call read-batch limit. Both succeeded without retries.

### 5. ethGetTransactionReceipt

Parameters:

```json
{
  "network": "eth-mainnet",
  "transactionHash": "0xcdca6219c1c3f2e34b9c0a20347a6338219663aa9acd7adb1426fdabda0267d7"
}
```

Result summary: successful receipt with one log. Relevant fields:

```json
{
  "status": "0x1",
  "transactionHash": "0xcdca6219c1c3f2e34b9c0a20347a6338219663aa9acd7adb1426fdabda0267d7",
  "blockNumber": "0x17ea710",
  "blockHash": "0x40fb8d899877e4c56460b8759782d70adabb7abacce54f0a714e1bc07f3debf2",
  "from": "0xd8da6bf26964af9d7eed9e03e53415d37aa96045",
  "to": "0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48",
  "logs": [{
    "address": "0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48",
    "topics": [
      "0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef",
      "0x000000000000000000000000d8da6bf26964af9d7eed9e03e53415d37aa96045",
      "0x0000000000000000000000003c7779d27348017415a9184acadc0d62052841ab"
    ],
    "data": "0x0000000000000000000000000000000000000000000000000000001bf8897d4a",
    "blockNumber": "0x17ea710",
    "blockTimestamp": "0x6a02d59b",
    "transactionHash": "0xcdca6219c1c3f2e34b9c0a20347a6338219663aa9acd7adb1426fdabda0267d7",
    "logIndex": "0x214",
    "removed": false
  }]
}
```

Local exact-integer decoding and field comparison confirmed the contract, Transfer topic, indexed addresses, amount and status. Receipt log timestamp decodes to `2026-05-12T07:24:11.000Z`, matching transfer metadata. No `ethBlockNumber`, transaction-body lookup, traces or Notify call was needed in this USDC replay.

## Cold-run observations

No blocking ambiguity was encountered for Scenario B. Replay direction calls inherit Step 1's shared parameters while replacing the category and adding the canonical USDC filter. The supplied app-choice answer resolved the multiple-app interaction. All required chain reads succeeded and the historical matching transfer was verified. There is no claim of webhook delivery or exercised cleanup. Local documentation reads, exposed-schema inspection, UTC clock checks and exact integer calculations supported the run; they did not query or mutate Alchemy account resources.
