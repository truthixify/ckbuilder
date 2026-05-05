# Week 5 Report — CKBuilder Track

**Period:** April 19 – April 25, 2026
**Participant:** Truth
**Track:** Builder

---

## Summary

Addressed code review feedback on **Grid3** (on-chain Tic Tac Toe) from the CKBuilder review on [issue #10](https://github.com/Nervos-Community-Catalyst/CKBuilder-projects/issues/10#issuecomment-4211671628). Fixes covered the dual lock/type script architecture, draw payout implementation, and fee structure improvements.

---

## What I Fixed

### Dual Lock/Type Script Differentiation

**Review feedback:** The single script serving as both lock and type is OK, but it's hard to differentiate which role the script is running as, and the VM charges cycles to load the script even when it exits immediately.

**Fix:** Added a flag-based approach in the script args to clearly differentiate whether the current execution is for the lock script or the type script path. This avoids ambiguous execution paths and makes the script logic cleaner.

### Draw Payout Implementation

**Review feedback:** Two approaches suggested — (1) both parties quit in the same transaction by storing full lock scripts instead of lock hashes, or (2) parties quit in sequence where either player can withdraw 40% from the remaining cell if capacity allows.

**Fix:** Expanded the game cell data to store the full lock script info (lock args + code hash) instead of only the 32-byte lock hash. This allows either party to build the draw payout transaction without needing to exchange lock scripts out-of-band. On draw, the contract now splits the pool between both players rather than just resetting the board.

### Fee Structure for Small Stakes

**Review feedback:** The 20% fee cell needs minimum 61 CKB capacity, which can exceed 20% for small stakes. Suggested using Any-One-Can-Pay cells from the operator, or ensuring the game cell has enough capacity to hold the fee payout by having operators provide empty game cells in advance.

**Fix:** Added a minimum stake validation that ensures the game cell always has enough capacity to cover the fee payout cell. For small stakes, the contract now enforces a minimum that guarantees the 20% fee meets the 61 CKB cell capacity requirement.

### Indexer Flickering (Confirmed Approach)

**Review feedback:** The indexer RPC methods are not designed for rapidly updated cells. Local filtering (which was already implemented) is the recommended approach.

**Confirmed:** The forward-progress guard already in place is the correct pattern. No changes needed — the reviewer validated the existing approach.

---

## Links

- **Live:** [grid3-ckb.vercel.app](https://grid3-ckb.vercel.app/)
- **GitHub:** [github.com/truthixify/grid3](https://github.com/truthixify/grid3)
- **Review:** [CKBuilder-projects#10](https://github.com/Nervos-Community-Catalyst/CKBuilder-projects/issues/10#issuecomment-4211671628)

---

## Blockers

- None this week.

---

## Plan for Week 6

- Address code review feedback on Haven Protocol
- Fix critical verification issues flagged in the Haven lock and type scripts
