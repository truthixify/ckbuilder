# Week 19 Report — CKBuilder Track

**Period:** July 26 – August 1, 2026
**Participant:** Truth
**Track:** Builder

---

## Summary

Another round of ckb-viz work, this one aimed at the hard cases — transactions that are big, or that carry assets the plain flow view doesn't explain on its own. Added an **owner net view** that collapses a transaction down to who gained and lost what, made the canvas handle **thousands of inputs** without falling over (tested on a real mainnet tx with 5,241 inputs), taught the decoder about **RGB++ / BTC time locks, Spore DOBs, and Nervos DAO withdrawals**, added a **lineage graph** for hopping between neighbouring transactions, and wired up **SVG / PNG export**. Posted the update to the [forum thread](https://talk.nervos.org/t/ckb-viz-read-any-ckb-transaction-as-a-flow-of-cells/10482/11).

---

## What I Did

### Owner net view

A transaction with a dozen cells per side is hard to read as individual cards. The owner net view aggregates cells by lock holder and shows the **net CKB and token change per party** — so a swap or a batched payout reads as "this address is down 100 CKB and up 500 tokens" instead of a wall of cell cards you have to net out in your head.

### Large-transaction support

Transactions with thousands of inputs now render **immediately from a sample**, with the rest resolved on demand behind a progress bar rather than blocking the first paint. Tested end to end on a mainnet transaction with **5,241 inputs and 263 outputs** — it renders right away and fills in as it resolves, where before a transaction that size would have been unusable.

### Richer decoding

- **RGB++ and BTC time locks** now surface the linked Bitcoin UTXO, so the CKB side of an RGB++ asset points at its Bitcoin anchor.
- **Spore DOBs** are labelled as generative objects with a DNA visualization, instead of showing as opaque cell data.
- **Nervos DAO withdrawals** show the unlock epoch and the 180-epoch maturity note, so the lock reads as a real deposit-and-wait rather than an unexplained `since`.

### Lineage graph

A neighbourhood view around the current transaction: parent transactions on the left, child transactions on the right, each a clickable node so you can walk the graph of where cells came from and where they went without leaving the tool.

### Export

Flows can be exported as **SVG or PNG** for sharing — dropping a legible transaction diagram into a thread or a doc instead of a raw hash.

All of this stays read-only against a standard CKB node, no indexer required for the core views.

---

## Links

- **Live:** [ckb-viz.truthixify.dev](https://ckb-viz.truthixify.dev)
- **Forum post:** [ckb-viz: read any CKB transaction as a flow of cells](https://talk.nervos.org/t/ckb-viz-read-any-ckb-transaction-as-a-flow-of-cells/10482/11)
- **Source:** [github.com/truthixify/ckb-viz](https://github.com/truthixify/ckb-viz)

---

## Blockers

- None this week.

---

## Plan for Week 20

- Start building Vellum M1 — claim cell type script, `readClaims` / `writeClaim`, scoring engine, and schema docs.
