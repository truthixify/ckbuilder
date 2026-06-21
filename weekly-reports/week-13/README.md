# Week 13 Report — CKBuilder Track

**Period:** June 14 – June 20, 2026
**Participant:** Truth
**Track:** Builder

---

## Summary

Two public write-ups went up on the forum this week, and the CCC PR landed. **Infern** got its introduction post now that the loop runs on public testnet end-to-end. **Vellum** got the design post that takes it from identity into reputation — self-declared profile plus signed external claims as on-chain cells. And the `@ckb-ccc/did-ckb` follow-up PR — identifier helpers, resolver, history walk, `did:plc` migration — was **merged** after review.

---

## What I Did

### Infern — introduction post

Wrote the [introduction thread](https://talk.nervos.org/t/introducing-infern-serve-an-ai-model-from-your-own-machine-and-get-paid-per-request-over-fiber/10408) now that the demo from Week 12 is running on public testnet, not just my devnet. The framing: anyone running a model can serve it and get paid per request over Fiber, with no accounts, cards, or chargebacks. CKB holds the shared state — provenance registry, provider/listing cells, stake, reputation — while every payment moves over Fiber.

The post lays out the parts now live: the F402 402-handshake (provider answers with a Fiber invoice, consumer pays over a channel and retries with the preimage as proof), the TypeScript provider agent / router / indexer / SDK, the Rust cells in `ckb-std`, the three settlement paths (prepaid balance, atomic multi-hop, identity-gated free tier), and the trust layers — liveness, inference check, honesty probes against the registered weights hash, and stake slashing for repeat failures. Public testnet, working chat interface, provider and consumer quickstarts, registration CLIs.

### Vellum — from identity to reputation

Wrote the [reputation design post](https://talk.nervos.org/t/vellum-extended-from-identity-to-reputation-on-did-ckb/10406). Vellum started as a `did:ckb` (WIP-01) dashboard; this extends it into a two-layer reputation system:

- **Self-declared identity** — profile, verification methods, social links living in the DID Document itself (the `services.profile` convention — displayName, avatar, bio — is live on testnet and mainnet).
- **External claims** — signed attestations stored as cells. A claim is `issuer, subject, schema, payload, issued_at, expires_at?, issuer_signature`. The subject's lock controls every claim cell, so the holder can destroy any record and reclaim capacity. V1 schemas cover GitHub, socials (X / Discord / Telegram / Bluesky), on-chain activity, task completions, fellowship milestones, and computed scores.

Reputation reads return a structured object — verified socials, on-chain metrics, recognitions, and a breakdown by dimension (technical, community, tenure, contribution) — not a single opaque number. Reads query the chain directly, so they don't depend on Vellum's infrastructure. Two SDK functions — `readClaims()` for eligibility checks and `writeClaim()` for issuers — let an external app gate voters or recognize tasks in ~10–20 lines. The post sketches concrete integrations (CKBoost, CKB-PoP) and the out-of-scope line for v1.

### CCC PR — [#376](https://github.com/ckb-devrel/ccc/pull/376) (merged)

`feat(did-ckb): identifier helpers, resolver, history walk, did:plc migration` was **merged on June 20**. It builds on [#337](https://github.com/ckb-devrel/ccc/pull/337) (basic create / transfer / destroy) with the higher-level surface the maintainer asked for in the Vellum thread:

- **Identifier helpers** — `argsToDid`, `didToArgs`, `isDidCkb` between Type ID args and `did:ckb:` URIs per WIP-01 §2.2, plus RFC 4648 base32 utilities.
- **Resolver** — `findDidCkbCell`, `resolveDidCkb`, `listDidCkbsByLock` for reverse lookup, returning a typed `DidCkbRecord`.
- **History walk** — `getDidCkbHistory` walks the cell chain backward via tx inputs and returns ordered `CREATE` / `UPDATE` / `MIGRATE` entries, capped by `maxSteps`.
- **`did:plc` migration** — `migrateDidCkb` and `buildMigrationWitness`, plus a `@ckb-ccc/did-ckb/plc` subpath (`fetchPlcLog`, `getGenesisOperation`, `getRotationKeys`, `parseDidKey`, `signRotationHash`, `verifyPrivateKeyMatch`). `@noble/curves` ships only to consumers importing the subpath.

Review feedback was folded in before merge — parallel RPC calls with client caching, accepting DID URIs directly, and dropping redundant type conversions. 28 tests, three examples in `packages/examples/`. This unblocks the advanced SDK operations Vellum's reputation work depends on.

---

## Links

- **Infern introduction:** [talk.nervos.org/t/introducing-infern](https://talk.nervos.org/t/introducing-infern-serve-an-ai-model-from-your-own-machine-and-get-paid-per-request-over-fiber/10408)
- **Vellum → reputation:** [talk.nervos.org/t/vellum-extended-from-identity-to-reputation-on-did-ckb](https://talk.nervos.org/t/vellum-extended-from-identity-to-reputation-on-did-ckb/10406)
- **CCC PR (merged):** [ckb-devrel/ccc#376](https://github.com/ckb-devrel/ccc/pull/376)
- **Infern source:** [github.com/truthixify/infern](https://github.com/truthixify/infern)
- **Vellum:** [github.com/truthixify/vellum](https://github.com/truthixify/vellum)

---

## Blockers

- None this week.

---

## Plan for Week 14

- Draft the v1 Vellum claim schemas and conventions for community review, and start on sponsored minting and social verification.
- Reach out to CKBoost and CKB-PoP about plugging into the claim-cell format.
- Build out Infern's inference and honesty check layers so the live set means more than a heartbeat answering.
