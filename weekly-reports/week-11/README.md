# Week 11 Report — CKBuilder Track

**Period:** May 31 – June 6, 2026
**Participant:** Truth
**Track:** Builder

---

## Summary

Started **Infern**, a compute marketplace where an individual running a model can serve it and get paid per request over Fiber. Drafted the spec and scaffolded the repo. The interesting piece isn't the inference — it's the payment rail. An individual selling inference to strangers has no way to collect many small payments without a custodian; Fiber plus CKB is that rail.

---

## What I Built

### Spec (v0.1, draft)

- **Who it serves:** home/self-hosted operators, individuals on rented GPUs, and fine-tuners with a niche model. The thread through all three is no billing system and a wish to charge per request.
- **What CKB holds:** a model provenance registry (`model_id → weights_hash`), provider cells, listing cells (price + capabilities), stake, reputation. Inference stays off chain on the provider's hardware.
- **What Fiber moves:** every payment. Three settlement patterns — F402 (an L402-style 402 handshake over Fiber's `new_invoice` / `settle_invoice`), prepaid balance, and atomic multi-hop for routed mode.
- **Modes:** direct (consumer picks a specific live provider) and routed (consumer names a `model_id` and a router picks from the live set of that pool).
- **Liveness:** three layers — heartbeat, inference check, and unannounced honesty checks against known-answer prompts so a provider can't claim a capable model and quietly serve a smaller one.

### Monorepo scaffold

```
infern/
  packages/
    core/        shared types, Zod schemas, typed Fiber JSON-RPC client, F402 handshake
    sdk/         consumer SDK over a standard inference client
    agent/       provider agent — registration, quoting, F402, request forwarding, health
    router/      live set selection, relay, settlement, failover
    indexer/     cell scanning, directory API, probing
    checks/      liveness / inference / honesty checks
    free-tier/   identity gating, quotas, treasury settlement
  contracts/     registry, provider, listing, stake cells (Rust, ckb-std)
  examples/      runnable provider + consumer quickstarts
```

TypeScript strict mode across the off-chain stack, Rust for the on-chain scripts, pnpm workspaces, Vitest, Zod at every boundary. CKB through CCC, Fiber through a typed JSON-RPC client in `core`. Picked the OpenAI-compatible wire format for the inference API so a provider can point Infern at a server they already run (vLLM, llama.cpp, Ollama, TGI) and do no extra integration work.

### F402 — what's actually new

Fiber already has the primitives an L402-style paywall needs: HTLCs with preimage reveal, invoices with payment hashes, and `new_invoice` / `settle_invoice` RPCs that accept an optional preimage. What doesn't exist is a ready-made implementation — Lightning has L402 proxies and token libraries, Fiber has neither. So F402 is the 402 challenge/response layer, the token format, and the glue to Fiber's RPCs, built on top rather than imported. Wrote the handshake in `core/` as the first real piece of code.

---

## Links

- **Source:** [github.com/truthixify/infern](https://github.com/truthixify/infern)

---

## Blockers

- None this week.

---

## Plan for Week 12

- Stand up a minimal end-to-end demo: provider agent serving a small local model, consumer SDK paying via F402, real sats moving over a Fiber channel on testnet.
- Land the follow-up PR to `@ckb-ccc/did-ckb` that the maintainer asked for in the Vellum thread.
