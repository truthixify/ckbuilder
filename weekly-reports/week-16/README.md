# Week 16 Report — CKBuilder Track

**Period:** July 5 – July 11, 2026
**Participant:** Truth
**Track:** Builder

---

## Summary

Two things this week. Kept building toward the [**Gone in 60ms** Fiber Network infrastructure hackathon](https://talk.nervos.org/t/gone-in-60ms-fiber-network-infrastructure-hackathon-announcement/10418) submission ahead of the July 15 deadline — specifics still held back until submissions are public. And in parallel shipped **ckb-viz**, a read-only transaction visualizer for CKB. Paste a transaction hash and it lays the flow out end to end — inputs on the left, outputs on the right, the spine connecting them — decodes the lock and type scripts against a per-network registry, and writes a one-sentence plain-language summary of what actually happened. The pitch: a raw CKB transaction from a node is a wall of `0x`-prefixed hex, and nothing in it announces "Alice sent 100 CKB to Bob and got change back." ckb-viz closes that gap. Live at [ckb-viz.truthixify.dev](https://ckb-viz.truthixify.dev).

---

## What I Built

### Fiber hackathon (continued)

Carried on the "Gone in 60ms" build from Week 15 — the two-week window runs through July 15, so this week was more hours on the entry. Holding the project details until submissions go public after the deadline; the write-up follows then.

### ckb-viz — the visualizer

- **Visual flow.** Three-column layout — inputs, transaction spine, outputs — with curved connectors tracing capacity from the cells being spent to the cells being created. Boots on the network's latest transaction so there's something on screen before you paste anything.
- **Script decoding.** Lock and type scripts are matched against a per-network registry — Secp256k1, Multisig, ACP, Omnilock, JoyID, RGB++, sUDT, xUDT, Nervos DAO, Spore, Cluster — so a cell reads as "JoyID lock / xUDT type" instead of two anonymous code hashes.
- **Plain-language summaries.** A one-sentence headline per transaction: CKB transfers, token transfers, DAO deposits, Spore mints. The decoder recognizes the shape and says what it is.
- **Cell-level detail.** Exact capacity, full script bodies, raw data with decoded views, and field decoders for UDT amounts, Nervos DAO operations, timelock (`since`) values, witness args, and addresses.
- **Lineage tracing.** Trace an input back to the transaction that created it, or an output forward to where it gets consumed — the indexer resolves consumption, `get_live_cell` / `get_transaction` resolve provenance.
- **Dual networks.** Mainnet and testnet, each with its own registry.
- **Accessibility.** Keyboard-operable, visible focus states, `prefers-reduced-motion` honoured.

### ckb-viz — architecture

Data moves through a testable pipeline where all the CKB-specific logic lives in pure functions:

```
TransactionSource → normalize → ScriptRegistry → decoder → FlowCanvas
   (node RPC)       (raw model)  (script naming)  (meaning)  (display)
```

- **TransactionSource** fetches the transaction and resolves its inputs via `get_live_cell` / `get_transaction`, using the indexer to track output consumption.
- **Normalize** converts snake-case hex quantities into camelCase bigint shannons — exact integer arithmetic throughout, no float rounding on capacities.
- **Registry + Decoder** are pure functions — the registry names scripts, the decoder assigns meaning — which keeps the whole CKB layer unit-testable in isolation.
- **FlowCanvas** renders the flow and the interactive detail panel.

No proxy or dedicated backend. It talks straight to the public CKB JSON-RPC nodes (`mainnet.ckb.dev` / `testnet.ckb.dev`), which serve permissive CORS, and because committed transaction data is immutable it caches aggressively through TanStack Query.

### ckb-viz — stack and design

React 18 + Vite + TypeScript (strict), Tailwind v4 with custom design tokens, CCC (`@ckb-ccc/core`) for JSON-RPC, the molecule codec, and script/address handling, self-hosted Geist / Geist Mono, Vitest for tests.

The interface reads like an instrument panel: a dark warm base, elevation from lightness and hairline borders rather than shadows or glows, squared corners throughout, monospace for every hash and capacity, and a single warm accent (ember) reserved for the spine and connectors so the eye follows the flow of value.

### ckb-viz — what it deliberately isn't

Not a block explorer, not a wallet, not an indexer. One transaction, visualized well.

---

## Links

- **Live:** [ckb-viz.truthixify.dev](https://ckb-viz.truthixify.dev)
- **Source:** [github.com/truthixify/ckb-viz](https://github.com/truthixify/ckb-viz)

---

## Blockers

- None this week.

---

## Plan for Week 17

- Wrap up the Fiber hackathon submission ahead of the July 15 deadline.
- Start building Vellum — the Community Fund DAO proposal has passed (decision window closes July 12), so kick off M1: the claim cell type script, `readClaims` / `writeClaim`, the scoring engine, and schema docs.
