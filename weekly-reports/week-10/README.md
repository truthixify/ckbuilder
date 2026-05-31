# Week 10 Report — CKBuilder Track

**Period:** May 24 – May 30, 2026
**Participant:** Truth
**Track:** Builder

---

## Summary

Iterated on **CKB Action Links** based on feedback from the [forum thread](https://talk.nervos.org/t/ckb-action-links-a-draft-protocol-for-shareable-ckb-transaction-urls/10315) and from people who tried the live client. The spec didn't change this week — all the work landed in the reference client and the example endpoints. Result: the flow is now legible on mobile, errors map cleanly onto the normative tags from the spec, the parameter forms catch bad input before the user signs anything, and what the wallet is about to sign is visible before they commit.

---

## What Changed

### Parameter forms — validation and input types

Every parameter type declared in the spec (`number`, `text`, `select`) now has a dedicated input with inline validation:

- **`number`** — `inputmode="decimal"` on mobile, min/max/step honoured from the manifest, formatted thousands separators on blur, CKB-shannon conversion shown beneath the input for amount fields.
- **`text`** — pattern attribute is wired through, so a publisher can constrain to a CKB address or a hex string and the form blocks submission until the value parses. Address fields also run a client-side `Address.fromString` check (via CCC) and show the parsed lock script preview.
- **`select`** — proper accessible combobox, keyboard navigation, option labels and values disambiguated.

The submit button stays disabled with a reason tooltip until every field validates — no more round-tripping to the server just to get an `INVALID_PARAMS` back.

### Manifest preview before signing

The pre-sign screen used to be a bare "Sign?" button. Now it renders:

- the manifest's `icon`, `title`, and `description` at the top, with the publisher origin shown directly under the title so the user can see who they're trusting
- the action `label` and the resolved `message` from the transaction response
- a parsed breakdown of the OTX — publisher outputs grouped by lock with capacities and any UDT/Spore type scripts decoded, plus a placeholder block showing which inputs the wallet will fill in
- network badge (mainnet / testnet) in a colour the user actually notices

It's the smallest version of a sign-screen that lets someone catch a spoofed action before they sign.

### Loading and error states

- Every async step (manifest fetch, parameter POST, OTX assembly, broadcast) now has its own loading state with a label so the user knows what's happening.
- Server errors are mapped to the spec's normative error tags. The client has a per-tag copy table so `INSUFFICIENT_CAPACITY` renders as "This action needs more CKB than your wallet has" with the shortfall in CKB, `EXPIRED` shows a retry button that re-fetches the manifest, `UNSUPPORTED_NETWORK` tells the user which network to switch to, and so on.
- A generic fallback for `INTERNAL` plus any non-spec error string, with the raw publisher message hidden behind a "details" disclosure.

### Mobile / responsive layout

- Single-column layout below 640px, full-width tap targets on the action buttons, parameter inputs at 16px to stop iOS Safari from zooming on focus.
- The QR code on the action page now scans cleanly — embedded the action URL in a `ckb-action:` link with a fallback HTTPS, and added a "copy link" affordance next to it for desktop.
- Wallet connect sheet redone for mobile so it doesn't get hidden behind the keyboard.

### Examples updated

The tip-jar, invoice, and DOB-minting example endpoints all now declare richer parameter metadata (placeholders, min/max where applicable, select option labels), so the new client UI has something to actually render. Useful as a reference for anyone writing their own action endpoint.

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

## Plan for Week 11

- Draft v0.2 of the spec, focused on the batching question Matt raised on the forum.
- Start a writeup on hosting an action endpoint end-to-end, so the next person who wants to publish one isn't reading source.
