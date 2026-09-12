Tabletop / instruction review, 2026-09-11 UTC, on skill v0.1.2

This is a bounded review of written decisions in [SKILL.md](../SKILL.md) and learning clarity in [Lab 5](../../../labs/05-watch-a-wallet/README.md). It is not a live cold run, an executed fixture suite, or a record of actual Alchemy tool results. The reviewer read the files and evaluated hypothetical requests against the instructions. No Alchemy calls, resource creation, delivery inspection, or deletion occurred during this review. No resource IDs, URLs, consent decisions, or successful tool responses are invented below. A later, separately labelled [receiver-backed validation](live-receiver-validation.md) exercised the lifecycle; that does not change this historical review's evidence.

## Decision checks

The following outcomes describe what the skill directs, not actions performed. All cases retain the skill's allowed-tool list, app-selection requirement, protected preexisting IDs, secret-handling rules, and prohibition on transaction signing, sending, or broadcasting.

| Hypothetical request or condition | Directed response | Review conclusion |
|---|---|---|
| Live mode with an acceptable receiver URL, but no creation consent | Select the chosen app, run the transfer preview, safely read the inventory, and show the exact proposal. Wait for unambiguous consent referring to that proposal. A URL or live-mode request is not creation consent. | Explicit creation gate is clear. |
| Fresh cleanup request without a creation record | Request the prior creation receipt or cleanup record and exact ID. Do not delete using only a pasted ID or similar name. | Provenance is required before deletion. |
| Request to delete a preexisting webhook by name | Preserve preexisting IDs and do not substitute that resource as the lab's cleanup target. Never delete an old webhook to free capacity. | Lab scope excludes this deletion even when a name seems plausible. |
| A creation attempt times out and its outcome is uncertain | Do not repeat creation. Compare a complete fresh inventory with protected pre-create IDs; evaluate exact configuration, proposed name, creation time, and address set. Escalate ambiguous or missing evidence to dashboard inspection. Keep cleanup pending until the uncertainty is resolved. | Identity checks are materially clearer in v0.1.2; immediate absence still cannot establish that creation did not occur. See remaining uncertainty below. |
| Creation approved, but deletion not confirmed | Show the actual new ID and safe identifying fields. Request separate explicit confirmation naming that ID. If unanswered or declined, do not delete; report CLEANUP PENDING and continuing delivery/CU implications. | Creation consent does not imply deletion consent. |
| Quota is full, inventory fails, or inventory is incomplete | Stop creation and retain the read-only preview. Do not delete existing resources, upgrade, or retry prohibited failed parameters. An app-scoped inventory below five does not prove account-wide capacity. | Capacity and inventory failure have conservative fallbacks. |
| Synthetic test, supported live transfer, unsupported transfer, duplicate, or reorg removal | A synthetic test remains TEST RECEIVED and cannot become live proof by checking its sample hash. A supported live row needs receiver evidence plus chain verification; signature evidence is separate. Unsupported-only events remain POST RECEIVED, TRANSFER UNVERIFIED. Deduplicate deliveries while preserving activity rows. Removed rows retract earlier observations and must not create fresh deposit alerts. | Transport, chain evidence, authentication, and retraction are distinguished. At this tabletop checkpoint, payload behavior had not yet been exercised; the later receiver-backed run did so. |

No instruction-level bypass of creation consent, separate deletion confirmation, or protected-resource provenance was found in these cases. This is a reading assessment, not proof that every agent or client will execute the decisions correctly.

## Verification of the v0.1.2 corrections

**Timeout identity reconciliation.** The skill now requires a new ID absent from the protected pre-create set, exact approved network/type/endpoint, the unique proposed name, creation time within the recorded attempt interval, and exactly one qualifying candidate. It then requires verification of the exact address set before treating that candidate as this run's resource. Missing name or time, differing fields, or multiple candidates require dashboard evidence. This closes the earlier lack of explicit candidate criteria. The phrase "name if returned" is bounded by the next instruction that an absent name requires dashboard evidence; it does not authorize identification by endpoint alone.

**Unsupported transfer verification.** The skill now limits chain-verification recipes to external ETH and canonical USDC. Internal ETH, other tokens, and NFTs remain unverified unless the same event contains a supported, verified row; all unverified rows remain in Gaps. A successful parent receipt alone cannot establish an internal transfer amount. USDC uses its own amount threshold, not an ETH threshold. The report template includes POST RECEIVED, TRANSFER UNVERIFIED. These instructions close the earlier temptation to apply external transaction-value checks to broader Address Activity rows. A supported row does not verify unrelated rows in the same event.

The v0.1.1 clarifications remain present: empty transfer lists skip transaction checks, verification selects the newest matching transfer across both directions, and an explicit no-receiver/fallback choice does not trigger another receiver question.

## Remaining uncertainty

The timeout paragraph permits cleanup to finish when "a complete reconciliation establishes that none was created," but it does not define sufficient evidence for this negative conclusion. A complete list means its returned inventory is not visibly truncated; it does not establish read-after-write consistency or prove that a timed-out request will never complete. Proof that no resource was created after a timeout cannot be inferred solely from an eventually consistent list immediately after the attempt. This review did not test the service's consistency semantics.

Conservative interpretation: an immediate empty candidate set after an uncertain create leaves a possible live resource and CLEANUP PENDING. Do not call it NOT NEEDED, repeat creation, or infer deletion authority. Seek further read-only reconciliation and dashboard/provider evidence establishing the operation's final outcome. If that evidence is unavailable, disclose the unresolved possibility rather than claim cleanup is complete. The skill could make this limitation explicit to avoid different interpretations of "complete reconciliation."

The creation-time interval is also intentionally restrictive. A delayed server-side completion or timestamp precision difference could fall outside the locally recorded interval. That is a reason to request additional evidence, not to widen the interval silently or conclude that no resource exists.

No readback, timeout, delayed consistency, incomplete pagination, retry, malformed payload, secret projection, or deletion race was exercised. Tabletop review cannot prove any of those runtime behaviors.

## Learning and Alchemy showcase

The lab clearly distinguishes Alchemy's automatic address-activity POST from the receiver's threshold logic and notification destination. It explains why Address Activity fits a wallet watch, how the transfer preview differs from the broader webhook feed, and why a configured subscription is not evidence of delivery. Synthetic tests, live chain checks, signatures, and finality have separate meanings. Those distinctions make the showcase useful without implying a production alerting service was installed. At the time of this review the live lifecycle was untested; the later receiver-backed run now supplies that evidence.

The fixed USDC replay provides a concrete rule rehearsal without receiver setup. The quiet personal preview teaches that no returned transfer in a bounded window does not prove inactivity. At this review checkpoint, the README openly stated that live resource creation, new-resource readback, test/live delivery, and deletion had not been exercised. The later receiver-backed run successfully exercised creation, readback, live delivery and deletion. Free entitlement and quota enforcement remain documented from official sources rather than inferred from the selected account's unknown billing tier.

Two wording improvements remain optional. Scenario C is described as a deposit rule even though the skill evaluates inbound and outbound transfers; calling it a wallet-movement rule, or stating both directions, would match its behavior. The phrase "Free profile" in the observed-results introduction could be read as a billing determination; "Free-tier assumptions" would fit the stated validation boundary more precisely.

The main path requires receiver provisioning, optional dashboard testing, receiver evidence sharing, and separate cleanup confirmation. The walkthrough explains these steps, but an early sentence saying that users are demonstrating the event plumbing rather than installing a finished notification service would set expectations sooner.

## Validation boundary

This review supports only the conclusion that the evaluated written decision paths are bounded and that the two requested corrections are present. By itself it does not establish Notify acceptance, Free entitlement, actual secret handling, POST authenticity, live delivery or cleanup. The later receiver-backed run separately demonstrates creation, configuration readback, live delivery and confirmed deletion; it does not prove the account tier or HMAC authenticity. Historical and preview evidence still must not be presented as proof that a webhook fired.
