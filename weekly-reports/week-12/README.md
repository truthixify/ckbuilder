# Week 12 Report — CKBuilder Track

**Period:** June 7 – June 13, 2026
**Participant:** Truth
**Track:** Builder

---

## Summary

Got the **Infern** end-to-end demo working — a provider agent serving a small local model, a consumer paying per request over Fiber via the F402 handshake, real sats moving on testnet. Also shipped the follow-up PR to `@ckb-ccc/did-ckb` that the maintainer asked for in the Vellum forum thread — identifier helpers, resolver, history walk, and `did:plc` migration on top of the basic create/transfer/destroy that landed earlier.

---

## What I Built

### Infern demo

The smallest version of the loop that exercises every moving part:

- **Provider agent** registers a listing cell on CKB testnet pointing at a local `llama.cpp` server behind the OpenAI-compatible endpoint, advertises a Fiber node id, and exposes `/quote`, `/health`, and the inference passthrough.
- **F402 handshake** — request without payment returns `402` with a Fiber invoice and a short-lived bearer token; the consumer SDK pays the invoice via the typed Fiber JSON-RPC client in `core`, receives the preimage as proof, and resends the request with the token; the agent verifies, forwards to the model server, and streams the completion back.
- **Consumer SDK** wraps a standard OpenAI client, intercepts `402`, and runs the handshake transparently — calling code looks like a normal `chat.completions.create`.
- **Indexer** scans for listing and provider cells and serves a directory API the SDK queries to find a live provider. Heartbeat probing only this week; the inference and honesty layers are next.

End-to-end on testnet: a fresh consumer with a funded Fiber channel can call a model on my laptop and pay per request without either side trusting the other with a balance. The smallest version of the network that proves identity, serving, and payment.

Spec was tightened where the implementation pushed back — token TTL, quote reconciliation for max-token estimates, and the shape of the listing's `capabilities` field all changed from v0.1.

### CCC PR — [#376](https://github.com/ckb-devrel/ccc/pull/376)

`feat(did-ckb): identifier helpers, resolver, history walk, did:plc migration`. Builds on [#337](https://github.com/ckb-devrel/ccc/pull/337) (basic create / transfer / destroy) with the higher-level surface the maintainer asked about on the [Vellum forum thread](https://talk.nervos.org/t/vellum-a-reference-dashboard-and-sdk-for-did-ckb/10274/5).

- **Identifier helpers** — `argsToDid`, `didToArgs`, `isDidCkb` for converting between Type ID args and `did:ckb:` URIs per WIP-01 §2.2, plus RFC 4648 base32 utilities.
- **Resolver** — `findDidCkbCell`, `resolveDidCkb` (DID string aware), `listDidCkbsByLock` for reverse lookup, all returning a typed `DidCkbRecord`.
- **History walk** — `getDidCkbHistory` walks the cell chain backwards via tx inputs and returns ordered `CREATE` / `UPDATE` / `MIGRATE` entries with tx hash, output index, block number, capacity, and decoded `DidCkbData`. Capped by `maxSteps` to prevent runaways.
- **`did:plc` migration** — `migrateDidCkb` wraps `createDidCkb` with `localId` stamped to the source `did:plc`; `buildMigrationWitness` signs the CKB tx hash with a rotation private key and returns a typed `DidCkbWitness`. New `@ckb-ccc/did-ckb/plc` subpath with `fetchPlcLog`, `getGenesisOperation`, `getRotationKeys`, `parseDidKey`, `signRotationHash`, `verifyPrivateKeyMatch`. `@noble/curves` is a dep but only ships to consumers that import the subpath.

Package surface stays additive — nothing in #337 is renamed or moved. 28 tests across identifier round-trips, plc multicodec detection, resolver mocking, and a simulated three-step cell chain (UPDATE, UPDATE, CREATE newest-first; localId on genesis flips it to MIGRATE). Examples in `packages/examples/` for `resolveDid`, `didHistory`, and `migrateDid`.

+1352 / -1 across 17 files. Open, awaiting review.

---

## Links

- **Infern source:** [github.com/truthixify/infern](https://github.com/truthixify/infern)
- **CCC PR:** [ckb-devrel/ccc#376](https://github.com/ckb-devrel/ccc/pull/376)
- **Vellum forum thread (context for the PR):** [talk.nervos.org/t/vellum-a-reference-dashboard-and-sdk-for-did-ckb](https://talk.nervos.org/t/vellum-a-reference-dashboard-and-sdk-for-did-ckb/10274/5)

---

## Blockers

- None this week.

---

## Plan for Week 13

- Add the inference and honesty check layers so the live set means something beyond "the heartbeat answers".
- Stake cell and slashing terms in Rust, so claiming to be live actually costs something.
- Address review feedback on PR #376 as it comes in.
