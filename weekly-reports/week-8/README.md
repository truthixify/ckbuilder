# Week 8 Report — CKBuilder Track

**Period:** May 10 – May 16, 2026
**Participant:** Truth
**Track:** Builder

---

## Summary

Built **Vellum**, a reference dashboard and SDK for `did:ckb` — the Decentralized Identifier method defined in [WIP-01](https://github.com/web5fans/web5-wips/blob/master/01.md) and implemented in [`web5fans/did-ckb`](https://github.com/web5fans/did-ckb). The dashboard talks directly to the deployed Type Script on mainnet and testnet, and the reusable bits live in a draft `@ckb-ccc/identity` package on a fork of [`ckb-devrel/ccc`](https://github.com/ckb-devrel/ccc).

---

## What I Built

### Dashboard — every WIP-01 operation wired against the deployed contract

- **Claim.** Builds the DID Metadata Cell with type-id-style identifier args, fills a DiceBear pixel-art default avatar seeded on the new DID, signs, submits, polls for inclusion.
- **Resolve.** Paste any `did:ckb`, get the document, profile, handles, verification methods, services. Reads directly from the indexer, no API in between.
- **My DID.** Reverse-lookup by Lock Script, document body with editable fields, Lock Script card, and the full on-chain operation history (walks the cell chain backwards through tx inputs and classifies each as CREATE / UPDATE / MIGRATE).
- **Edit.** Document editor with inline validation hints for AT Protocol / Nostr / `did:key` shapes.
- **Rotate.** Move control of a DID to a different CKB Lock without changing the identifier. Parses pasted addresses via CCC's `Address.fromString`, signs with the current Lock.
- **Deactivate.** Burn flow with a 24-hour UI cool-down per DID.
- **did:plc migration (WIP-02).** Fetches the source op log from `plc.directory`, signs the CKB tx hash with one of the holder's PLC rotation keys (kept in browser memory only), attaches the witness in `WitnessArgs.output_type`, surfaces the 72-hour finalisation window.

### `@ckb-ccc/identity` SDK package

Mirrors the conventions of `@ckb-ccc/spore`, `@ckb-ccc/udt`: `@ckb-ccc/core` as a dep, ESM + CJS builds, an `identity` namespace export, optional `/plc` subpath for migration helpers. Exposes `resolveDid`, `buildCreateTx`, `buildUpdateTx`, `buildDeactivateTx`, `buildMigrationTx`, `getDidHistory`, `listDidsByLock`, and `fetchPlcLog`. 26 unit tests cover base32, identifier round-trips, document encoding, DAG-CBOR round-trip, and default-avatar semantics — all passing under vitest. Released as GitHub Release tarballs on the fork for now, pending a conversation with CCC maintainers on upstreaming.

### Design calls worth flagging

- **Profile convention.** Document carries the `did:plc`-compatible fields plus a `services.profile` entry of type `VellumProfile` with `displayName`, `avatar`, `bio` inline — open question whether this should be standardised across CKB ecosystem apps.
- **Default avatar.** SDK fills `https://api.dicebear.com/9.x/pixel-art/png?seed=<did>` at create time. Deterministic, identifies the holder visually without onboarding friction.
- **Capacity reserve.** Cell capacity computed exactly and bumped by a 200 CKB reserve so the holder can grow the document later without re-funding. Fully recoverable on deactivation.
- **Client-side history walk.** Walks the cell chain backwards through tx inputs (`client.getTransaction` + `previousOutput`), capped at 50 steps for safety — avoids needing indexing infrastructure.

---

## Links

- **Live dashboard:** [vellum-lyart.vercel.app](https://vellum-lyart.vercel.app)
- **Dashboard source:** [github.com/truthixify/vellum](https://github.com/truthixify/vellum)
- **SDK source:** [github.com/truthixify/ccc — feat/identity-package](https://github.com/truthixify/ccc/tree/feat/identity-package/packages/identity)
- **Spec:** [github.com/web5fans/web5-wips](https://github.com/web5fans/web5-wips)
- **Contract:** [github.com/web5fans/did-ckb](https://github.com/web5fans/did-ckb)

---

## Blockers

- None this week.

---

## Plan for Week 9

- Gather feedback from CCC maintainers on whether `@ckb-ccc/identity` belongs upstream.
- Prototype DID-anchored Spore issuance as a first integration target.
