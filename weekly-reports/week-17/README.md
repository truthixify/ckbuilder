# Week 17 Report — CKBuilder Track

**Period:** July 12 – July 18, 2026
**Participant:** Truth
**Track:** Builder

---

## Summary

Took **ckb-viz** public and pushed it well past a single-transaction viewer. Posted the [introduction thread](https://talk.nervos.org/t/ckb-viz-read-any-ckb-transaction-as-a-flow-of-cells/10482) — read any CKB transaction as a flow of cells — and shipped two substantial new views on top of it: an **address view** for balances, holdings, and history, and a **transaction simulator** at `/simulate` that runs a raw transaction against current chain state and shows cycles, or which script failed and why. Also closed out the "Gone in 60ms" Fiber hackathon window (submissions ended July 15).

---

## What I Did

### ckb-viz — introduction post

Posted the [introduction thread](https://talk.nervos.org/t/ckb-viz-read-any-ckb-transaction-as-a-flow-of-cells/10482) now that the tool does more than render one transaction. The framing stays the same as the build itself: a raw CKB transaction is a wall of `0x`-prefixed hex that never announces what actually moved, and ckb-viz turns it into a legible flow of cells. Closed the post by asking the community for gnarly transaction hashes — the ones that are genuinely hard to read — as test cases.

### ckb-viz — address view

Paste a `ckb` / `ckt` address and get its state instead of just a single transaction: CKB balance, token holdings, and transaction history. This turns the tool from "explain this one tx" into "explain this account," while keeping the same read-only, node-direct, no-backend posture.

### ckb-viz — transaction simulator (`/simulate`)

Accepts a raw transaction and runs it against the current chain state before it's ever broadcast:

- Reports **cycle consumption** for a transaction that would pass.
- For one that would fail, identifies **which script fails** and surfaces the error, rendering the rejected transaction in red in the flow so the failing point is visible rather than buried in an RPC error string.

This is the piece that makes ckb-viz useful while building, not just while debugging after the fact — you can see what a transaction will do before committing it.

### ckb-viz — smaller improvements

- Raw JSON view with copy, for when you want the underlying object rather than the decoded flow.
- Off-chain Spore content links, so DOB/Spore cells point at what they actually carry.
- Clearer error messaging across the board.

### Fiber hackathon — window closed

The "Gone in 60ms" Fiber Network infrastructure hackathon submission window ended July 15. Project details stay held back until submissions are public; write-up to follow.

---

## Links

- **Forum post:** [ckb-viz: read any CKB transaction as a flow of cells](https://talk.nervos.org/t/ckb-viz-read-any-ckb-transaction-as-a-flow-of-cells/10482)
- **Live:** [ckb-viz.truthixify.dev](https://ckb-viz.truthixify.dev)
- **Source:** [github.com/truthixify/ckb-viz](https://github.com/truthixify/ckb-viz)

---

## Blockers

- None this week.

---

## Plan for Week 18

- Start building Vellum M1 now that the Community Fund DAO proposal has passed — claim cell type script, `readClaims` / `writeClaim`, scoring engine, and schema docs.
- Fold community-submitted transaction hashes into ckb-viz as test cases and fix whatever they break.
