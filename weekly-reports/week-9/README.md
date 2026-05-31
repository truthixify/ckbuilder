# Week 9 Report — CKBuilder Track

**Period:** May 17 – May 23, 2026
**Participant:** Truth
**Track:** Builder

---

## Summary

Drafted **CKB Action Links**, a protocol for shareable CKB transaction URLs, and built a full reference implementation. The idea adapts Solana's Actions and Blinks pattern to CKB's cell model: a single URL resolves into a wallet-aware transaction intent so a tip, an invoice, or a mint can travel through a chat message, a QR code, or an email without dragging a dApp along for the ride. Posted as a draft for community feedback on the Nervos forum.

---

## What I Built

### The protocol

A user encounters a `ckb-action:` (or plain HTTPS) URL, the client `GET`s a manifest describing the action and its parameters, the user fills them in, the client `POST`s the parameters plus the user's address, and the publisher returns an Open Transaction (OTX) with outputs already specified. The wallet then completes inputs, fees, and change, signs, and broadcasts.

The cell model is the reason this needs OTX rather than a Solana-style fully-formed transaction. In account-based chains a publisher with the consumer's public key can build the whole thing. On CKB, only the consumer can decide which cells to spend, so the publisher commits to what they control (outputs and any pre-signed inputs they contribute) and the consumer fills the rest. The OTX format carries publisher outputs, optional pre-signed publisher inputs, an empty (or partial) inputs list, and witness placeholders for the consumer's signature.

**URL formats** — `ckb-action:https://example.com/actions/tip/truth` for wallet-aware clients, and the bare `https://...` form so the same URL works in a browser if the client supports protocol detection.

**Manifest (GET response)** — `type: "action"` with `title`, `description`, `icon`, `label`, `network`, and a `links.actions` array. Each action has a `label`, an `href` template, and optional `parameters` (currently `number`, `text`, `select`).

**Transaction (POST response)** — `type: "transaction"`, the OTX as a hex blob, an `encoding` discriminator (`molecule` for now), a human-readable `message` shown to the user before signing, and an optional `callback` URL the client hits after broadcast.

**Errors** — normative tags (`INVALID_ADDRESS`, `INSUFFICIENT_CAPACITY`, `INVALID_PARAMS`, `UNSUPPORTED_NETWORK`, `EXPIRED`, `INTERNAL`) so wallets can render consistent UX regardless of which publisher served the action.

### Reference implementation

TypeScript monorepo, four published packages and three example endpoints.

| Package | Purpose |
|---|---|
| `@ckb-actions/sdk` | Zod schemas for the manifest and transaction shapes, manifest fetching with redirect handling, OTX parsing and assembly helpers |
| `@ckb-actions/server` | Express 5 reference endpoint — registers actions, validates parameters against declared types, returns a typed OTX |
| `@ckb-actions/client` | Vite + React UI that resolves any action URL, renders the manifest, walks the user through parameter entry, and hands the OTX to a wallet via CCC |
| `@ckb-actions/examples` | Tip jar, invoice creation, DOB minting — each one a runnable endpoint plus a deep-link |

132 tests across the four packages (schema round-trips, manifest fetcher edge cases, OTX assembly, server parameter validation, client URL parsing). Wallet integration through `@ckb-ccc/ccc` so any CCC-compatible wallet works without per-wallet code.

### Design calls worth flagging

- **OTX over fully-formed transactions.** Trying to mimic Solana Actions one-to-one would force the publisher to know cell selection, which a hosted endpoint generally can't. Splitting the contract — publisher specifies outputs, consumer specifies inputs — is the smallest change that respects the cell model.
- **Two URL schemes, same target.** `ckb-action:` is the canonical form for wallet-aware clients, but every action endpoint also responds to bare HTTPS so the URL is shareable in places where custom schemes get stripped (Twitter, Telegram previews).
- **Normative error tags.** Wallets render the error UI, not publishers — so the spec pins down the error vocabulary instead of leaving it as freeform strings.
- **Encoding discriminator on the wire.** OTX is hex-prefixed molecule today, but the `encoding` field on the response leaves room for base64 or a JSON shape later without breaking older clients.

### Forum post + open questions

Posted the spec to talk.nervos.org along with six open questions for the community: manifest signing and trust, which parameter types should be normative, hex vs. base64 OTX encoding, whether canonical Spore code hashes belong in the spec, whether Fiber Network operations get their own scheme, and whether multi-transaction batching is in scope. First feedback already arriving — Matt pointed out that batched operations inside a single transaction fit the scheme cleanly, while multi-transaction batching would need orchestration patterns that don't exist yet. Useful framing for v0.2.

---

## Links

- **Forum post:** [talk.nervos.org/t/ckb-action-links — a draft protocol for shareable CKB transaction URLs](https://talk.nervos.org/t/ckb-action-links-a-draft-protocol-for-shareable-ckb-transaction-urls/10315)
- **Live client:** [ckb-actions.vercel.app](https://ckb-actions.vercel.app)
- **Live action endpoint:** [ckb-actions.onrender.com](https://ckb-actions.onrender.com)
- **Source:** [github.com/truthixify/ckb-actions](https://github.com/truthixify/ckb-actions)

---

## Blockers

- None this week.

---

## Plan for Week 10

- Triage forum feedback and roll the obvious wins into the client UI.
- Tighten parameter form validation and the pre-sign preview based on what wallets are actually showing.
- Map out a v0.2 of the spec covering the batching question.
