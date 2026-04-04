# Week 2 Report — CKBuilder Track

**Period:** March 30 – April 4, 2026
**Participant:** Truth
**Track:** Builder

---

## Summary

Deep-dive week. Studied all core CKB concepts in depth and built a working lock script in Rust. Packaged everything into a public learning resource: **"Learn CKB in 45 Minutes"**.

---

## Completed This Week

### Core Concepts Studied

Wrote detailed notes (with questions and answers) on 13 CKB topics:

1. **Cells and Scripts** — cell structure (`capacity`, `lock`, `type`, `data`), live vs dead cells, how scripts reference code
2. **Transaction Structure** — inputs, outputs, `cell_deps`, `witnesses`, validation flow, lock vs type script roles
3. **Script Identification** — `code_hash`, `hash_type` (`data` vs `type`), `args`, upgradeability trade-offs
4. **NC-Max Consensus** — propose/commit model, why it reduces orphan blocks, orphan-aware difficulty adjustment
5. **Economic Model** — 1 CKByte = 1 byte, base/secondary issuance, Nervos DAO as inflation shield, state rent
6. **User Defined Tokens (UDT)** — distributed token model vs ERC-20, transfer validation, minting authorization, storage costs
7. **CKB-VM Execution** — RISC-V ISA, syscalls (`load_script`, `load_witness`, etc.), cycles, pure validation model
8. **Off-Chain Computation, On-Chain Verification** — the core CKB design pattern, no re-entrancy, RISC-V expressiveness
9. **`since` Field** — absolute/relative time locks, block/epoch/timestamp metrics, why DAO uses epoch-based locks
10. **Dep Groups** — `dep_type: "code"` vs `"dep_group"`, bundling cell dependencies
11. **Witnesses** — proof data, `WitnessArgs` structure, witness-to-input matching, interaction with script groups
12. **Script Groups** — grouping by identical script, `Source::GroupInput`/`GroupOutput`, efficiency gains
13. **Writing a Script** — full Rust lock script development using official `ckb-script-templates`

### Built a Lock Script in Rust

- Scaffolded project using `cargo generate` with `ckb-script-templates`
- Wrote a "password lock" script that validates witness data against `args`
- Compiled to RISC-V binary (sub-1KB)
- Wrote two tests with `ckb-testtool` (correct password passes, wrong password rejects)
- Deployed to local devnet using `offckb`

### Published Learning Resource

Created **"Learn CKB in 45 Minutes"** — a structured guide for developers to learn CKB fast:

- 13 chapters covering all core concepts
- 40 questions with hidden hints and answers
- Working Rust script project with tests
- Deployment instructions for local devnet

GitHub: [learn-ckb-in-45-minutes](https://github.com/truthixify/learn-ckb-in-45-minutes)

Forum post: *(pending publication on Nervos Talk)*

---

## Blockers

- None this week.

---

## Plan for Week 3

- Build an interactive CKB learning tool (rustlings/starklings-style) using the 13 chapters and 40 questions as content foundation
- Continue with CKB Academy modules
- Explore the CCC SDK and CCC Playground
- Start the Transfer CKB and Store Data on Cell tutorials
