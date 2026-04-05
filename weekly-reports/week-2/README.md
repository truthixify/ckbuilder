# Week 2 Report — CKBuilder Track

**Period:** March 30 – April 5, 2026
**Participant:** Truth
**Track:** Builder

---

## Summary

Deep-dive week. Studied all core CKB concepts in depth, built a working lock script in Rust, published a learning resource, and shipped **Grid3** — a fully on-chain Tic Tac Toe game on CKB testnet.

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

### Built and Shipped Grid3 — On-Chain Tic Tac Toe

Built a full-stack, fully on-chain Tic Tac Toe game deployed on CKB testnet. Every move is a real CKB transaction. Players stake CKB to play and the winner takes 80% of the pool.

**Smart Contract (Rust → RISC-V):**
- Single script serves dual role as both lock and type script
- As type script: validates all game state transitions (create, join, move, finish, reset, forfeit, cancel)
- As lock script: enforces player authorization (WAITING: open for join, ACTIVE: only the two players can spend)
- 84-byte cell data layout: game state, 3×3 board, turn, player lock hashes, stake amount, winner
- Win detection (8 lines), draw → board reset (play again), forfeit (abandon sends pool to fee address)
- Deployed to testnet with Type ID (upgradable)

**Frontend (Next.js + TypeScript + Tailwind):**
- "Lattice Neon" design system — dark void background, neon cyan/purple accents, Space Grotesk typography, glassmorphism panels
- Real wallet connection via CCC (`@ckb-ccc/connector-react`) — supports JoyID and other CKB wallets
- Real-time game state polling from the CKB indexer with forward-progress guard (prevents UI flickering during indexer transitions)
- Optimistic move display with pending cell spinner while transactions confirm on-chain
- Full game lifecycle: create game → share link → opponent joins → take turns → claim prize / reset on draw / forfeit
- Leaderboard and match history backed by Supabase (server-side API routes)
- Responsive design with mobile-first game board layout

**Key Technical Challenges Solved:**
- CKB cell model: each move creates a new cell (old consumed, new created) — built polling system that tracks cell migrations by `playerXLock` across outPoint changes
- Lock script for shared state cells: both players need to spend the game cell, solved with dual-role script (same binary as lock + type)
- Payout verification: contract computes blake2b hash of fee lock script and compares with `load_cell_lock_hash()` on output cells
- Indexer transition flickering: CKB indexer briefly shows both old and new cells — added `isForwardProgress()` guard that rejects backward state transitions

**Links:**
- Live: [grid3-ckb.vercel.app](https://grid3-ckb.vercel.app/)
- GitHub: [github.com/truthixify/grid3](https://github.com/truthixify/grid3)
- Contract code hash: `0x4245528f6287893443bb2a160757f32c4aba0669772c2575955abbff298dc59d`

---

## Blockers

- None this week.

---

## Plan for Week 3

- Polish Grid3 UI/UX based on testing feedback
- Add proper draw settlement (store full lock scripts in game data for on-chain 50/50 split)
- Explore adding a timeout mechanism (if no move within N blocks, opponent can claim the pool)
- Build an interactive CKB learning tool (rustlings/starklings-style) using the 13 chapters and 40 questions as content foundation
- Continue with CKB Academy modules
