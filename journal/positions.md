# Sleeve positions

Status: ACTIVE          <!-- ACTIVE | PAUSED | HALTED -->
Sleeve capital (S): $200.00   <!-- funded 2026-09-23, confirmed by Luke -->
Inception date: 2026-09-23
Options test: OPEN      <!-- 1 long call/put, max $40 premium, APPROVE FIRST, from 2026-09-28 (Luke 2026-09-25). OPEN → USED once placed -->

| Ticker | Shares | Entry date | Entry price | Cost | Stop | Target | Stop order ID | Proposal |
|---|---|---|---|---|---|---|---|---|
| CCL | 3 | 2026-09-30 | $25.4099 | $76.23 | $26.68 TRAILING (stop-limit $26.32, GTC; ratcheted 2026-10-27 13:46 to high $28.08 × 0.95; was $26.56 from 11:47, B/E $25.41 before) | $28.20 (+11.0%) | 6ae0e386-7d1b-4d58-bd8b-f4e5f520922d | P-2026-09-30-01 |

## Not sleeve (do not manage)
- DJT $10 call exp 2026-10-02, 1 contract, bought by Luke manually 2026-09-29 10:11 ET for $10.00 (placed_agent=user, order 6abbc718-8a40-4d63-bedd-10e13472c00d). It uses ~$10 of the Agentic account's cash, so sleeve cash available = account `cash`. Agent never touches it.
