# Reference runs

One report per scenario, produced by a fresh agent following the prompt from `PROMPTS.md` and the versioned skill. The user's app choice was supplied as an interaction answer: **Alchemy MCP**. For scenario A the user explicitly chose no public receiver and requested fallback/consent-gate validation. No webhook creation or deletion was authorized or attempted.

| File | Scenario | What it shows |
|------|----------|---------------|
| [live-no-receiver.md](live-no-receiver.md) | A. busy wallet, no receiver | Six real ETH transfers, a 38.23 ETH inflow checked against the chain, protected preexisting inventory, no creation without a receiver/consent |
| [treasury-replay.md](treasury-replay.md) | B. historical USDC rule | A reproducible 120,133.877066 USDC outflow above the 100,000 threshold, verified against its canonical token log |
| [personal-preview.md](personal-preview.md) | C. deposit rehearsal | No returned ETH transfer in a 500-block window; no invented activity, webhook or automatic monitoring |
| [cleanup-no-record.md](cleanup-no-record.md) | D. fresh cleanup request | App selection followed by a provenance pause; no guessed ID and no webhook calls |
| [live-receiver-validation.md](live-receiver-validation.md) | Receiver-backed follow-up | Consented creation, exact readback, two automatic POSTs, chain verification, an invalid deletion-ID refusal, and verified teardown |
| [safety-review.md](safety-review.md) | Independent instruction review | Seven hypothetical consent, quota, timeout and event-evidence cases; explicitly not runtime tests |
| [validation.md](validation.md) | Release checks | Schema inspection, Free/PAYG sources, safety checks, documentation checks, live lifecycle evidence and remaining boundaries |

The A, B and C cold runs used v0.1.0 on 2026-09-11; D and the independent instruction review used v0.1.2. Their complete non-secret call parameters and summarized results are **Call logs**, not Raw call logs. Receiver URLs and secrets are excluded. Chain state changes; compare scope, rule evaluation and evidence labels rather than requiring today's ETH amount to equal an old run. The fixed USDC replay should retain its amount, block and hash.

**Skill versions move; these files do not.** The cold runs found two small ambiguities: which matching row to verify first and what to do with two empty history lists. Those were clarified in v0.1.1. Independent safety review then tightened uncertain-creation reconciliation and unsupported webhook-transfer handling in v0.1.2. Version 0.1.3 preserves uncertainty after an empty immediate timeout readback and adds report labels for cleanup without an ID. The receiver-backed follow-up then moved no-receiver runs ahead of Notify inventory, clarified published redaction, and made mismatched deletion-ID consent explicitly invalid in v0.1.4. Version 0.1.5 aligns public product claims with Alchemy's current documentation and makes clear that remaining verification boundaries belong to the lab or receiver environment, not Alchemy delivery. See validation for the final-version checks.

**Live lifecycle exercised, secrets withheld.** The receiver-backed follow-up shows creation, readback, automatic delivery and verified deletion. Its private interaction used the exact receiver and resource IDs; the published artifact consistently redacts them. HMAC signature verification and the dashboard's synthetic test action remain untested.
