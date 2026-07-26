# Week 18 Report — CKBuilder Track

**Period:** July 19 – July 25, 2026
**Participant:** Truth
**Track:** Builder

---

## Summary

Added **`/learn`** to ckb-viz — an interactive primer that teaches CKB's cell model from scratch. It came directly out of feedback from Neon at a CKBuilder meeting: the visualizer is great once you already understand cells, but a newcomer needs the mental model first. So `/learn` builds that model with a plain metaphor — a piggy bank for a wallet, coins for cells — then hands the reader the real thing. Live at [ckb-viz.truthixify.dev/learn](https://ckb-viz.truthixify.dev/learn).

---

## What I Did

### ckb-viz — `/learn` interactive primer

A guided, hands-on introduction to how CKB actually works, aimed at people seeing the cell model for the first time:

- **The core concepts, by metaphor.** The cell model, lock and type scripts, and capacity reserves, all introduced as a piggy bank (the wallet) holding coins (cells) — concrete before technical.
- **A build-a-transaction exercise.** The reader picks which coins to spend and watches inputs and outputs balance in real time, so the "a transaction consumes cells and creates new ones" idea is something they do rather than read.
- **Real constraints, enforced.** The exercise validates that change can't drop below the 61 CKB minimum cell capacity — the same rule the chain enforces — so the lesson matches reality instead of hand-waving it.
- **The transaction lifecycle.** A walkthrough of the stages a transaction moves through: mempool → proposed → committed.
- **Metaphor → real names.** Each metaphorical piece is mapped back to its actual field name, so the reader leaves able to read a real transaction (and the rest of ckb-viz).

Posted the update to the [ckb-viz forum thread](https://talk.nervos.org/t/ckb-viz-read-any-ckb-transaction-as-a-flow-of-cells/10482/8), crediting the feedback that prompted it.

---

## Links

- **Live:** [ckb-viz.truthixify.dev/learn](https://ckb-viz.truthixify.dev/learn)
- **Forum post:** [ckb-viz: read any CKB transaction as a flow of cells](https://talk.nervos.org/t/ckb-viz-read-any-ckb-transaction-as-a-flow-of-cells/10482/8)
- **Source:** [github.com/truthixify/ckb-viz](https://github.com/truthixify/ckb-viz)

---

## Blockers

- None this week.

---

## Plan for Week 19

- Start building Vellum M1 — claim cell type script, `readClaims` / `writeClaim`, scoring engine, and schema docs.
