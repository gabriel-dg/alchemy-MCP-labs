Cold run, 2026-09-11 UTC, on skill v0.1.0

Live Alchemy MCP cold run of Scenario C by a fresh agent. Chain state changes, so a later run will use a different block window and may return transfers. Resource creation, delivery, and cleanup were not exercised. No Notify calls were made.

## Prompt

Verbatim Scenario C from `skills/watch-a-wallet/PROMPTS.md`:

```text
Read skills/watch-a-wallet/SKILL.md in this repo and follow it exactly. List Alchemy apps and select one; ask me if several exist. Use only the tools the skill allows. Never sign, send, broadcast, or use gas-policy tools. Do not create, update, or delete any resource. Label the output as a transfer preview, not automatic monitoring.

Mode: preview
Address: 0x1111111111111111111111111111111111111111
Network: eth-mainnet
Minimum ETH: 0.01
Receiver: none
```

User app-choice interaction answer supplied for this run: `Alchemy MCP (dhp14gyqfwvwa12q)`.

No resource creation or deletion was authorized. Preview mode requires no consent pause, receiver, or Notify inventory.

# Watch a wallet

- **Input:** `0x1111111111111111111111111111111111111111`; mode `preview`; highlight each inbound or outbound external ETH transfer of at least `0.01 ETH`; receiver `none`. This is the public demo address, not an ownership claim.
- **Network:** eth-mainnet (Notify: ETH_MAINNET)
- **Observed:** 2026-09-11 14:35:21–14:35:53 UTC. Frozen range `0x18c07f8`–`0x18c09eb` (25,954,296–25,954,795), 500 blocks inclusive.
- **One-sentence summary:** No external ETH transfer was returned in either direction in this 500-block window, so there is no returned transfer matching the 0.01 ETH rule; this does not prove that the address is inactive.
- **Recent activity:** Transfer preview only, not automatic monitoring.

| When UTC | Direction | Asset | Amount | Hash | Rule |
|---|---|---|---|---|---|
| — | — | — | — | — | No transfers returned |

- **Webhook:** PREVIEW ONLY
- **Delivery:** NOT TESTED; no receiver or event evidence. Signature status: not verified.
- **Teardown:** NOT NEEDED; no resource was created.
- **Tools used (in order):** list_apps, select_app, ethBlockNumber, getAssetTransfers (2, inbound and outbound in one parallel batch).
- **Gaps:** Coverage is restricted to nonzero external ETH transfers within the frozen 500-block window, page 1 with at most three rows per direction. Both responses were empty and had no pageKey. This says nothing about older history, tokens, or internal transfers. There was no transaction hash to verify with ethGetTransactionReceipt or ethGetTransactionByHash. Billing plan and webhook capacity were not verified; Free was assumed. No webhook delivery, receiver signature validation, or finality was demonstrated.

## Call log

All five Alchemy MCP calls are recorded below with exact parameters and summarized results. Unselected app identifiers and names are redacted as private account metadata. No API key, signing key, token, or receiver URL is included. Connected tool schemas were inspected before calls; all calls used exposed tool names and parameter shapes. No errors or retries occurred.

### 1. list_apps

Parameters:

```json
{}
```

Result summary: three apps were listed. One was `Alchemy MCP` with app_id `dhp14gyqfwvwa12q`; two unrelated apps are redacted. No pagination cursor was returned. The supplied user answer selected Alchemy MCP, so no additional app-choice question was necessary.

### 2. select_app

Parameters:

```json
{"app_id":"dhp14gyqfwvwa12q"}
```

Result summary: app `Alchemy MCP` (`dhp14gyqfwvwa12q`) selected; server reported its API key cached. No key value was returned or recorded.

### 3. ethBlockNumber

Parameters:

```json
{"network":"eth-mainnet"}
```

Result summary: `0x18c09eb` (25,954,795). Frozen as toBlock. Local integer computation produced fromBlock = max(0, 25,954,795 − 499) = 25,954,296 = `0x18c07f8`.

### 4. getAssetTransfers — inbound

Parameters:

```json
{
  "network": "eth-mainnet",
  "category": ["external"],
  "order": "desc",
  "maxCount": "0x3",
  "withMetadata": true,
  "excludeZeroValue": true,
  "fromBlock": "0x18c07f8",
  "toBlock": "0x18c09eb",
  "toAddress": "0x1111111111111111111111111111111111111111"
}
```

Result summary: `transfers: []`; no pageKey returned.

### 5. getAssetTransfers — outbound

Parameters:

```json
{
  "network": "eth-mainnet",
  "category": ["external"],
  "order": "desc",
  "maxCount": "0x3",
  "withMetadata": true,
  "excludeZeroValue": true,
  "fromBlock": "0x18c07f8",
  "toBlock": "0x18c09eb",
  "fromAddress": "0x1111111111111111111111111111111111111111"
}
```

Result summary: `transfers: []`; no pageKey returned. Calls 4 and 5 ran concurrently; their ordering here is submission order.

Clock support calls used `{}` and returned `2026-09-11 14:35:21 UTC` before app selection and `2026-09-11 14:35:53 UTC` after the transfer reads. File reads, schema discovery, integer range computation, and artifact writing were local validation support, not Alchemy calls.

## Cold-run feedback

The preview routing and no-resource boundary were clear. One small instruction gap: Step 1 directs the agent to verify the first matching transfer or newest returned transfer, but does not explicitly describe the case where both lists are empty. This run skipped receipt and transaction calls because no transaction hash was available and recorded that limitation in Gaps. No invented hash or sample activity was substituted.
