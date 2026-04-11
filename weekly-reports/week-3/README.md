# Week 3 Report — CKBuilder Track

**Period:** April 6 – April 11, 2026
**Participant:** Truth
**Track:** Builder

---

## Summary

Built and shipped **Haven Protocol** — a privacy reputation layer on Nervos CKB. Users earn a public Haven Score (0–1000) based on real activity across Twitter, GitHub, Discord, and LinkedIn, while the identity behind the score stays private inside a Phala TEE. The full system spans custom CKB scripts (Rust), a Phala TEE service (NestJS), an SP1 proof worker, a TypeScript SDK, and a React dashboard — all deployed and live on testnet.

---

## What I Built

### Custom CKB Scripts (Rust → RISC-V)

**Haven Lock Script — Dual-path authorization:**
- TEE update path (0x00): verifies TEE secp256k1 signature, ensures type script is present on output
- User direct path (0x01): standard secp256k1 signature for top-ups, migration, or cell closure
- Lock args: `user_blake160(20) | tee_blake160(20)` — 40 bytes
- Uses `k256` crate for pure Rust secp256k1 ECDSA recovery (no CKB dynamic loading)
- Blake2b-160 of compressed recovered pubkey for identity verification

**Haven Type Script — SP1 proof verification:**
- Three modes: creation (score=0, valid deposit), update (SP1 PLONK proof verification), destruction (always allowed)
- Top-up mode: `is_topup_only` check allows deposit-only changes without proof
- Validates 12 constraints per update: identity preservation, epoch increment, fee deduction, score range, breakdown sum, program hash against registry, expiry calculation
- Uses XuJiandong's CKB-optimized SP1 PLONK verifier

**Score Cell Data Layout — 127 bytes:**
- Version, score (u16), epoch (u32), user_identity (32B), program_hash (32B), proof_hash (32B), score breakdown (4x u16), timestamps (issued_at, expires_at), deposit_balance (u64)
- All little-endian, matching CKB convention

**Registry Cell — 139 bytes:**
- On-chain governance: program hash, epoch duration, min deposit, per-update fee, tier thresholds
- Supports grace-period program hash rotation

### Phala TEE Service (NestJS)

- Runs inside Phala Cloud TEE container with local PostgreSQL
- Manual OAuth 2.0 for Twitter (PKCE), GitHub, Discord, LinkedIn — no passport, direct token exchange
- Identity commitment: Blake2b hash of CKB public key
- Scoring engine with per-platform collectors (GitHub repos/commits/age, Discord guilds/Nitro/linked accounts, LinkedIn profile)
- Four scoring formulas: privacy (40%), contribution (30%), humanity (20%), community (10%)
- Automated 24-hour scoring cycle (configurable cron) — reads previous score from on-chain cell, skips proof when score unchanged
- DCAP attestation generation via dstack
- CKB transaction builder: manual sighash computation, WitnessArgs molecule encoding, dual witness signing (Haven lock + secp256k1 fee cells)
- TEE-assisted top-up co-signing endpoint for user deposit operations
- Duplicate social account prevention (same Twitter/GitHub can't connect to multiple wallets)

### SP1 Proof Worker

- Upgraded from SP1 v5 to v6 (Hypercube)
- Custom DCAP guest program compiled with `cargo prove build`
- Vendored automata-dcap crates for deterministic builds
- Deployed on Railway with Docker (builds from repo root context)
- Generates PLONK proofs via SP1 Network prover

### TypeScript SDK (`@haven-protocol-ckb/sdk`)

- Published on npm with auto-versioning CI/CD
- Score cell parser/serializer with correct unsigned 64-bit LE handling
- CKB client for score lookup, leaderboard collection (prefix search), attestation verification
- TEE client for OAuth flows, identity registration, score refresh, top-up co-signing
- React hooks: `useHavenScore`, `useHavenGate`, `useLeaderboard`, `useAuth`, `useDeposit`
- Sub-path exports: `sdk`, `sdk/react`, `sdk/tee`, `sdk/contracts`, `sdk/attestations`

### React Dashboard

- Full Haven Score display with tier badges (Observer → Sovereign)
- Two-step onboarding: register identity (wallet signature) → create score cell (CKB deposit)
- Integrity breakdown with per-component progress bars
- Score history chart (Recharts)
- OAuth connection cards for 5 platforms (Twitter, GitHub, Discord, LinkedIn, CKB wallet)
- Deposit management: create score cell (1000/2000/5000 CKB), top-up (100/500/1000 CKB)
- Multi-step loading overlay with elapsed timer and step counter
- Leaderboard with on-chain score cell collection
- Deployed on Vercel with SPA routing

### Key Technical Challenges Solved

- **Lock script secp256k1**: Replaced C dynamic loading with pure Rust `k256` — eliminates external dep cells, simpler deployment
- **SP1 v5 → v6 migration**: v5 ELF incompatible with v6 memory layout — wrote custom guest program, fixed "ELF below STACK_TOP" and "unreachable code" errors
- **Unsigned 64-bit parsing bug**: JavaScript's bitwise OR returns signed 32-bit — deposits ≥ ~21 CKB showed negative balance. Fixed `readU32LE` to apply `>>> 0` to the full expression
- **TEE-assisted top-up**: CCC wallet extensions can't sign custom lock scripts — TEE co-signs the Haven lock witness while user's wallet signs fee cells. Type script allows deposit-only changes via `is_topup_only`
- **On-chain sighash**: Manual Blake2b sighash-all computation matching CKB's group-based witness hashing algorithm

---

## Deployed Infrastructure

| Component | Platform | URL/Hash |
|-----------|----------|----------|
| Lock Script | CKB Testnet | `0x296b392e89ec260d8ddc81c3ade5f18bb1d9775f6f9a3885c0ea1fd81d11cf18` |
| Type Script | CKB Testnet | `0x134e98b02554060a248e337f63eb5a6136c379f41afad9f4bc023c0f3b52d715` |
| Registry Cell | CKB Testnet | `0x31105ea4e11bc6172be31f2aa04dfbd8fea103a55c708bc7ce36389acceeb52c` |
| TEE Service | Phala Cloud | Docker: `truthixify/haven-tee:latest` |
| Proof Worker | Railway | Auto-deploys on `proof-worker/**` changes |
| Dashboard | Vercel | [haven-protocol.vercel.app](https://haven-protocol.vercel.app) |
| SDK | npm | [`@haven-protocol-ckb/sdk`](https://www.npmjs.com/package/@haven-protocol-ckb/sdk) |

---

## Links

- **Dashboard:** [haven-protocol.vercel.app](https://haven-protocol.vercel.app)
- **GitHub:** [github.com/truthixify/haven](https://github.com/truthixify/haven)
- **SDK:** [npmjs.com/package/@haven-protocol-ckb/sdk](https://www.npmjs.com/package/@haven-protocol-ckb/sdk)

---

## Blockers

- npm granular tokens return 404 on publish — using classic token as workaround
- Twitter OAuth blocked by free-tier API restrictions (read-only, no user lookup)

---

## Plan for Week 4

- Test full end-to-end scoring cycle on testnet with multiple users
- Add Telegram as a connection platform
- Improve score history visualization with more data points
- Explore mainnet deployment requirements
