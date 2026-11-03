# Sleeve positions

Status: ACTIVE          <!-- ACTIVE | PAUSED | HALTED -->
Sleeve capital (S): $200.00   <!-- funded 2026-09-23, confirmed by Luke -->
Inception date: 2026-09-23
Options test: OPEN      <!-- 1 long call/put, max $40 premium, APPROVE FIRST, from 2026-09-28 (Luke 2026-09-25). OPEN → USED once placed -->

| Ticker | Shares | Entry date | Entry price | Cost | Stop | Target | Stop order ID | Proposal |
|---|---|---|---|---|---|---|---|---|
| TEVA | 0.923789 (fractional) | 2026-11-03 10:46 | $43.2999 | $40.00 | $41.54 SOFT (alert a0faca0d; check hourly, sell at market if ≤ stop) | $46.80 | — (fractional: no RH stop) | P-2026-11-03-01 |

## Not sleeve (do not manage)
- DJT $10 call exp 2026-10-02, 1 contract, bought by Luke manually 2026-09-29 10:11 ET for $10.00 (placed_agent=user, order 6abbc718-8a40-4d63-bedd-10e13472c00d). It uses ~$10 of the Agentic account's cash, so sleeve cash available = account `cash`. Agent never touches it.
