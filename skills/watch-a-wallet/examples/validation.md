# Release validation

Validation date: **2026-09-11 UTC**. Final skill: **watch-a-wallet v0.1.5**. Network: **eth-mainnet**, Notify **ETH_MAINNET**. App chosen explicitly by the user: **Alchemy MCP**, `dhp14gyqfwvwa12q`. Initial cold runs used the no-receiver fallback; a later receiver-backed follow-up exercised the complete lifecycle. This file is a validation summary, not a Raw call log.

SHA-256 of the final `SKILL.md` working-file bytes: `80b35e52b620da02c32c35e68386df8658ad551c5a00bfe506a4a1574b691b70`. Line-ending conversion can change this byte hash without changing the skill text.

## What actually ran

| Run | Skill | Alchemy calls | Observed outcome |
|-----|-------|---------------|------------------|
| [A. Busy wallet without receiver](live-no-receiver.md) | 0.1.0 | 8 | Six external ETH rows, one 38.229755167118571730 ETH inbound match verified; one protected preexisting webhook; no creation |
| [B. USDC replay](treasury-replay.md) | 0.1.0 | 5 | 120,133.877066 USDC outflow verified in block 25,077,520; threshold MATCH |
| [C. Personal preview](personal-preview.md) | 0.1.0 | 5 | Both 500-block history queries empty; no invented hash or monitoring |
| [D. Cleanup without provenance](cleanup-no-record.md) | 0.1.2 | 3 | App selected; creation record and exact ID missing; no Notify calls or deletion |
| [E. Receiver-backed lifecycle](live-receiver-validation.md) | 0.1.3, followed by 0.1.4 hardening | 20 | Consented create, exact configuration readback, two automatic POSTs, one chain-verified row, and confirmed deletion with preexisting inventory preserved |

Runs A to D were fresh-agent runs using the published prompts, with only the user's app/receiver answers supplied as interactions. D includes a repeated app-list call caused by a client response-projection issue; it was not hidden from the count. They made **21 Alchemy calls**, all read-only apart from session app selection. Run E was a collaborative receiver-backed follow-up, not a cold run; it made **20 calls**, including one consented create and one separately confirmed delete. No update, signing, transaction submission or gas-policy tool was called. Each report records its scope, exact non-secret parameters and summarized results.

Author setup calls before the cold runs, also on 2026-09-11, are recorded separately below. They used repository rules while the new skill was being authored, not a released skill version. A later publication review also repeated the five-call USDC replay against the exact published parameters; it reproduced the empty inbound result, 120,133.877066 USDC outbound row, expected transaction hash, successful receipt and canonical contract. Including those five review calls and run E, the release work made **49 Alchemy calls**.

### Author setup Call log

| Call | Exact parameters | Summarized result |
|------|------------------|-------------------|
| `list_apps` | `{}` | Three apps; no selection made while awaiting the user's choice |
| `list_apps` | `{}` | Repeated discovery after combined tool output was truncated; three apps, including Alchemy MCP (`dhp14gyqfwvwa12q`); other account metadata omitted here |
| `select_app` | `{"app_id":"dhp14gyqfwvwa12q"}` | User explicitly chose Alchemy MCP; selection confirmed and key cached without exposing its value |

This is a summarized Call log. Documentation browsing, tool-schema discovery, file reads, local arithmetic and validation commands are not Alchemy calls.

### Publication-review replay Call log

The five calls repeated Scenario B exactly: `list_apps` with `{}`; `select_app` with `{"app_id":"dhp14gyqfwvwa12q"}`; inbound and outbound `getAssetTransfers` with block `0x17ea710`, canonical USDC contract, `maxCount: "0x3"`, metadata and zero-value exclusion; and `ethGetTransactionReceipt` for `0xcdca6219c1c3f2e34b9c0a20347a6338219663aa9acd7adb1426fdabda0267d7`. Full transfer parameters are unchanged from [the original replay Call log](treasury-replay.md#call-log). No mutation or Notify read occurred.

The scenario A inventory returned one protected, redacted preexisting ID: an active Address Activity webhook on **SOLANA_DEVNET**. It was neither created nor modified by this lab. Its receiver and signing key were not printed or saved. The projected list did not establish full inventory completeness or account-wide slot availability. No address-filter read was made against that unrelated resource to manufacture a configuration-verification result.

## Tool schemas inspected

The connected server exposed **173 Alchemy tools** on the validation date. The following 11 allowed names all existed. Parameters below summarize the actual exposed schemas, not proposed tools. No exact hosted-server release version was exposed in these tool declarations; the skill version above identifies the playbook being released.

| Tool | Exposed input shape used by this lab | Validation |
|------|-------------------------------------|------------|
| `list_apps` | optional `cursor: string`, `limit: number` (1–25) | Live calls with `{}`; returned text app entries, not JSON |
| `select_app` | required `app_id: string` | Live selection of the user-chosen app; key cached, value not exposed |
| `ethBlockNumber` | required `network: string` | Live tip snapshots |
| `getAssetTransfers` | required `network: string`, `category: array`; optional `fromAddress`, `toAddress`, `fromBlock`, `toBlock`, `maxCount`, `order`, `withMetadata`, `excludeZeroValue`, `contractAddresses`, `pageKey` | Live direction queries; fixed replay and bounded current window |
| `ethGetTransactionByHash` | required `network`, `transactionHash`, both strings | Live external ETH value/from/to verification |
| `ethGetTransactionReceipt` | required `network`, `transactionHash`, both strings | Live ETH status and canonical USDC log verification |
| `get_usage_summary` | object, no required properties | Schema only; optional account-wide cost check, not called in cold runs |
| `list_webhooks` | optional `app_id: string` | Live inventory read, allowlisted safe response projection |
| `get_webhook_addresses` | required `webhook_id: string`, optional `app_id: string` | Live exact-address readback; no pagination input exposed |
| `create_webhook` | required `body: object`, optional `app_id: string` | One live call after exact proposal consent; accepted the documented Address Activity body |
| `delete_webhook` | required `webhook_id: string`, optional `app_id: string` | One live call after separate confirmation of the exact private ID |

The transfer schema's category enum is `external`, `internal`, `erc20`, `erc721`, `erc1155`, `specialnft`; `order` is `asc` or `desc`; address/contract filters are strings or string arrays, block/count/cursor fields are strings, and metadata/zero-value flags are booleans. The lab sends only `external` or `erc20` in its history reads. It does not send a threshold to the API.

The create tool accepts a flexible `body` object. The exact Address Activity field set was checked against the [official create API](https://www.alchemy.com/docs/data/webhooks/webhooks-api-endpoints/notify-api-endpoints/create-webhook): `network`, `webhook_type`, `webhook_url`, `name`, `addresses`. Run E submitted exactly those fields with a real accepted receiver after consent; the selected account accepted them, and subsequent list/address reads matched. The public Call log redacts the endpoint and resource IDs.

Other exposed Notify tools were `update_webhook` and `get_webhook_nft_filters`; neither is part of the lab. In the inspected workflow, MCP handled configuration and readback while the receiver or Alchemy dashboard supplied delivery evidence, synthetic testing and signing-key management. No dedicated MCP call for receiver-inbox reads, test delivery, signing-key retrieval or background scheduling appeared in the inspected list; the lab does not invent one. Notify may return signing keys even though no dedicated key-fetch tool exists.

## Free and PAYG evidence

Checked official sources on 2026-09-11:

- [Pricing](https://www.alchemy.com/pricing): Free includes 30M CU/month and five webhook slots; PAYG has 100 webhook slots and metered usage.
- [Webhook account limits](https://www.alchemy.com/support/how-many-webhook-can-i-create): the five-Free/100-PAYG quota is per account, not an entitlement per selected app.
- [Compute-unit costs](https://www.alchemy.com/docs/reference/compute-unit-costs): ordinary webhook delivery is bandwidth-priced at 0.04 CU/byte. A small address list and short window constrain exposure; receiver thresholding does not reduce incoming bandwidth.
- [Webhook quickstart](https://www.alchemy.com/docs/reference/notify-api-quickstart): HTTP 200 acceptance, HMAC verification over the raw body, and failed-delivery retry backoff up to ten minutes for Free/PAYG.
- [Address Activity](https://www.alchemy.com/docs/reference/address-activity-webhook) and [webhook types](https://www.alchemy.com/docs/reference/webhooks-overview): address-based ETH/token movements fit this use case; the feed is broader than the lab's external-ETH preview.

Runtime evidence proves that the selected account could read transfers and receipts, create and read back one Address Activity webhook, receive automatic events, and delete the exact new resource. It does **not** establish the account's billing tier, remaining account-wide capacity, general Free creation entitlement, quota-error behavior or delivered-event cost. No paid probe, quota exhaustion, upgrade or mutation retry was used to infer those facts. The documentation explicitly degrades to a historical/current preview when Notify, quota or receiver access is unavailable. An error mentioning payg, upgrade or billing is recorded once; it is not retried with the same parameters.

## Safety and learning review

[Independent instruction review](safety-review.md) evaluated seven hypothetical cases: URL without consent, missing cleanup provenance, preexisting target, uncertain creation timeout, unconfirmed deletion, quota/inventory failure, and synthetic/live/unsupported/reorg activity. This was a tabletop assessment, not mocked runtime execution or successful external calls.

The review led to explicit timeout candidate checks and unsupported-transfer labels in v0.1.2. Version 0.1.3 states that an immediately empty inventory cannot prove a timed-out create failed, and provides `NOT IDENTIFIED` / missing-ID cleanup labels. Run E then demonstrated no duplicate creation after a local reporting failure and no deletion after a confirmation question displayed the wrong ID. Both were agent-side recovery cases, not Alchemy API or delivery failures. Version 0.1.4 encodes both observed safeguards, moves no-receiver runs ahead of Notify inventory, and clarifies redaction. Version 0.1.5 aligns public product claims with Alchemy's current documentation and attributes remaining validation boundaries to the lab or receiver environment. No provider-side create timeout was deliberately induced. Earlier cold-run files retain their original version headers and findings.

From the **learning** perspective, the first result is a real transfer with a rule and a chain check. The fixed USDC block makes the receiver-free route reproducible. The quiet wallet teaches the limited meaning of empty history. The walkthrough explains each evidence label and both consent pauses.

From the **Alchemy showcase** perspective, run E demonstrates durable event delivery outside the chat, configured through the same OAuth MCP connection as the preceding reads. Two automatic POSTs arrived without sending a transaction for the demo. Address Activity keeps the main route to one address list, without GraphQL. The README makes clear that Alchemy supplies the feed; receiver thresholds and email/Slack routing are application work.

## Local checks

All 11 skill-allowed tool names and all 44 entries in `.claude/settings.json` were compared with the connected server list. None were missing. The only shared permission additions are `list_webhooks` and `get_webhook_addresses`. No wildcard, resource mutation, transaction submission, gas-policy or airdrop permission was added or pre-approved. The preexisting untracked `.claude/settings.local.json` had no `permissions` block and was left unchanged; user/global client settings outside this repo were not audited.

The skill-creator's Python validator was attempted for both entry points. `python` was unavailable; the installed `py` launcher reported that Python 3 was not installed. This is an environment limitation, not a successful validator run. The frontmatter, matching name/description, allowed keys and completed scaffold were checked directly instead. No runtime or dependency was installed to validate documentation.

Final checks were rerun against v0.1.5:

| Check | Result |
|-------|--------|
| Local Markdown paths and heading anchors | 82 local paths across 55 tracked/new Markdown files; no missing paths; linked Lab 5 anchors matched |
| Walkthrough structure | All eight required sections, in the requested order |
| Prompt parity | Both walkthrough prompts exactly match A/B in `PROMPTS.md`; all four prompt blocks are self-contained |
| Entry-point rules | AGENTS.md and CLAUDE.md lab routing/rules match exactly |
| Product claims and attribution | Public scale copy matches Alchemy's current 100+ blockchain wording; observed 173-tool evidence is dated; Alchemy delivery success is separated from lab-verifier and receiver-test boundaries |
| Skill frontmatter | Both names match the folder, descriptions match, allowed top-level keys only, no unfinished scaffold; canonical metadata version 0.1.5 |
| JSON examples and formatting | 31 JSON blocks parsed across 12 new files; balanced fences, no trailing whitespace or sensitive live identifiers detected |
| Shared permissions | 44 exposed read/session-selection tools; only two Notify reads added; no mutating pre-approvals |
| Tracked diff | `git diff --check` passed; changes confined to Lab 5 artifacts and affected repository references |
| Receiver lifecycle | Create, exact readback, two automatic POSTs, supported chain verification, separate delete consent and post-delete absence all observed; HMAC not tested |

The links check covers local paths and Markdown heading anchors, not the future availability of external sites. Secret-pattern checks supplement manual review; they are not a claim to detect every possible credential format. Existing lab examples were not rewritten to change their historical log headings or versions.

## Remaining release boundary

One temporary webhook was created and deleted. Its exact configuration and address filter were read back, two live POSTs arrived, one supported row was checked against the chain, and post-delete inventory proved absence while the protected preexisting resource remained. Notify responses were projected before display; no signing key or endpoint was written to this repository.

Remaining untested behavior: HMAC verification by the receiver, the dashboard's synthetic test action, actual quota denial, a provider-side create/delete timeout, queued delivery after deletion, and a live transfer crossing the 1 ETH threshold. The selected account's successful creation does not prove its billing tier or universal Free entitlement. The receiver-backed evidence is separate from the historical replay and tabletop review; none substitutes for another.
