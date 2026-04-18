# Week 4 Report — CKBuilder Track

**Period:** April 12 – April 18, 2026
**Participant:** Truth
**Track:** Builder

---

## Summary

Integrated CKB (Nervos) as a fully supported chain in **Wraith Protocol**, a multichain stealth address platform. Wrote and deployed a custom `wraith-stealth-lock` script and a `wraith-names-type` script on CKB testnet, added full CKB support to the published SDK (`@wraith-protocol/sdk`), built a CKB chain connector for the TEE agent server, and shipped a demo app with CCC wallet integration for sending and receiving stealth payments on CKB.

---

## What I Built

### Custom CKB Scripts (Rust, RISC-V)

**wraith-stealth-lock — Stealth address ownership verification:**
- Lock args: 53 bytes = `ephemeral_pubkey (33) || blake160(stealth_pubkey) (20)`
- The Cell itself is the announcement. No separate announcer contract needed. The ephemeral public key is embedded directly in the lock script args.
- Delegates secp256k1 signature verification to the on-chain `ckb-auth` via `exec_cell`
- Hardcoded ckb-auth code hash to avoid C build dependency
- Deployed on CKB testnet: code hash `0x31f6ab9c7e7a26ecba980b838ac3b5bd6c3a2f1b945e75b7cf7e6a46cb19cb87`

**wraith-names-type — .wraith name registration:**
- Type args: blake2b hash of the name string (32 bytes)
- Cell data: 66 bytes = `spending_pubkey (33) || viewing_pubkey (33)`
- Validates 66-byte meta-address data on create and update
- Ownership proven by the Cell's lock script, no signature verification needed
- Deployed on CKB testnet: code hash `0xc133817d433f72ea16a2404adaf961524e9572c8378829a21968710d6182e20d`

### Why CKB's Cell Model Fits Stealth Addresses

On EVM chains, stealth addresses need a separate announcer contract that emits events for recipients to scan. On CKB, the Cell itself carries the announcement data in its lock args. One transaction does both the payment and the announcement.

The `get_cells` RPC acts as a built-in indexer. Filter by lock script code hash to find all stealth Cells without deploying a subgraph or external indexer.

Name ownership is also simpler. On EVM, the WraithNames contract needs on-chain secp256k1 point decompression and signature verification. On CKB, the lock script handles ownership natively. If you can spend the Cell, you own the name.

### SDK Integration (`@wraith-protocol/sdk/chains/ckb`)

Added CKB as the fourth chain module in the SDK (alongside EVM, Stellar, and Solana). Published as versions 1.4.0 and 1.4.1 on npm.

**Stealth crypto module:**
- `deriveStealthKeys` — from wallet signature (secp256k1 ECDSA, same curve as EVM)
- `generateStealthAddress` — ECDH shared secret with SHA-256 hashing, blake160 address derivation with "ckb-default-hash" personalization
- `scanStealthCells` / `checkStealthCell` — scan live Cells by stealth-lock code hash, match with viewing key
- `deriveStealthPrivateKey` — `(spending_key + SHA256(shared_secret)) mod n`
- `fetchStealthCells` — query CKB RPC `get_cells` with stealth-lock filter, handles pagination
- `encodeStealthMetaAddress` / `decodeStealthMetaAddress` — `st:ckb:` prefix format

**Names module:**
- `hashName` — blake2b with CKB personalization, validates 3-32 char lowercase alphanumeric
- `buildRegisterName` — returns type script and 66-byte cell data for creating a name Cell
- `buildResolveName` — returns type script for querying name Cells via RPC
- `metaAddressFromNameData` — parses cell data to `st:ckb:...` meta-address

**Deployments config** with testnet code hashes and cell dep info baked into the SDK.

134 tests passing across all chain modules (17 new CKB name tests).

### TEE Agent Server (Spectre)

Added `CKBConnector` implementing the `ChainConnector` interface. The connector imports all crypto from `@wraith-protocol/sdk/chains/ckb` instead of reimplementing inline.

Also refactored all existing connectors (EVM, Stellar, Solana) to use the SDK instead of duplicate inline crypto. Removed 893 lines of redundant code, replaced with SDK imports.

Key operations:
- `scanPayments` — `fetchStealthCells` + `scanStealthCells` from SDK
- `resolveName` — queries CKB RPC `get_cells` by names type script, parses with `metaAddressFromNameData`
- `registerName` — uses `buildRegisterName` from SDK

### Demo App with CCC Wallet

Integrated CKB into the demo app at [demo.usewraith.xyz](https://demo.usewraith.xyz):

- CCC connector (`@ckb-ccc/connector-react`) for wallet connection — supports JoyID, OKX, UniSat, MetaMask
- Auto-sign stealth message on wallet connect (same pattern as Horizen/Stellar/Solana)
- Send page builds real CKB transactions with `wraith-stealth-lock` output via `ccc.Transaction.from()`
- Uses `tx.completeInputsByCapacity(signer)` and `tx.completeFeeBy(signer)` for input selection and fee calculation
- Minimum 95 CKB validation for stealth cell capacity requirement
- Receive page scans for stealth Cells and displays matched payments with private key reveal
- CCC connector styled to match the dark design system via CSS overrides

---

## Deployed Infrastructure

| Component | Platform | Details |
|-----------|----------|---------|
| wraith-stealth-lock | CKB Testnet | Code hash: `0x31f6...cb87`, Cell dep tx: `0xde1e...32b` |
| wraith-names-type | CKB Testnet | Code hash: `0xc133...e20d`, Cell dep tx: `0x9acd...1b8` |
| SDK | npm | `@wraith-protocol/sdk@1.4.1` with `chains/ckb` entry point |
| Demo | Vercel | [demo.usewraith.xyz](https://demo.usewraith.xyz) |
| Docs | Mintlify | [docs.usewraith.xyz](https://docs.usewraith.xyz) |

---

## Links

- **SDK:** [npmjs.com/package/@wraith-protocol/sdk](https://www.npmjs.com/package/@wraith-protocol/sdk)
- **Demo:** [demo.usewraith.xyz](https://demo.usewraith.xyz)
- **Docs (CKB):** [docs.usewraith.xyz/sdk/chains/ckb](https://docs.usewraith.xyz/sdk/chains/ckb)
- **Contracts:** [github.com/wraith-protocol/contracts](https://github.com/wraith-protocol/contracts)
- **GitHub Org:** [github.com/wraith-protocol](https://github.com/wraith-protocol)
- **Forum Post:** [Wraith Protocol: Stealth Address Payments on CKB](https://talk.nervos.org/)

---

## Blockers

- CKB stealth cells require minimum ~95 CKB capacity due to the 53-byte lock args, which is a high minimum payment amount compared to other chains
- CCC `signMessageRaw` return format varies by wallet type, needed runtime type coercion

---

## Plan for Week 5

- Test full end-to-end stealth payment cycle on CKB testnet with multiple users
- Explore reducing minimum cell capacity for stealth payments
- Deploy the multichain Spectre TEE server with CKB connector to Phala
