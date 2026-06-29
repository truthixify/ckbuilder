# Week 14 Report — CKBuilder Track

**Period:** June 21 – June 27, 2026
**Participant:** Truth
**Track:** Builder

---

## Summary

Two posts went up on the forum this week, both aimed at lowering the barrier to building on CKB. **DIR** launched — an open directory of CKB-native project ideas, seeded with 30 ideas across 9 categories and set up as a standing request for more, with a live site and a PR-based contribution flow. And **Vellum** turned last week's reputation design into a formal `[DIS]` grant proposal to the CKB Community Fund DAO — concrete milestones, budget, acceptance criteria, and CKBoost / CKB-PoP integrations co-designed with their maintainers.

---

## What I Did

### DIR — open directory of CKB-native project ideas

Launched [DIR](https://talk.nervos.org/t/introducing-dir-an-open-directory-and-standing-request-for-ckb-native-ideas/10415) (live site + introduction post). The problem it answers: new builders keep asking what to build on CKB, and good answers exist but are scattered across forum threads, Discord, and people's heads. DIR collects them in one place.

It's two things at once — a catalog you browse and build from, and a standing request for ideas. Anyone can open a PR adding an idea with a problem statement, why CKB fits, and acceptance criteria; PRs are validated against a schema in CI. It ships seeded with 30 ideas across 9 categories — developer tooling, Spore/DOB, RGB++, Fiber, OTX, AI agents, identity, storage, and social. Each idea carries a spec, the CKB properties that make it a fit, a difficulty level, and acceptance criteria, with beginner-friendly ones tagged for newcomers. Ideas are CC0; builders keep ownership of whatever they make from them.

Live now: the website, the catalog, and the specs. Where it's headed: wishes, public commitments, stakes, and bounties become real attestations and cells on testnet — so builders don't just pick an idea, they publicly commit, build, and ship on-chain.

### Vellum — `[DIS]` reputation extension proposal

Took last week's [from identity to reputation](https://talk.nervos.org/t/vellum-extended-from-identity-to-reputation-on-did-ckb/10406) design post and turned it into a formal `[DIS]` [proposal](https://talk.nervos.org/t/dis-vellum-reputation-extension-on-did-ckb/10419) to the CKB Community Fund DAO. Where the Week 13 post laid out the concept, this one operationalizes it: a $7,000 request with milestones, deliverables, acceptance criteria, and a clear out-of-scope line.

The gap it targets: emerging CKB programs need a Sybil-resistant signal to gate eligibility or weight participation, but builder activity is scattered across CKBoost, hackathons, bounties, and organizer notes, with nothing a governance contract can query. The proposal attaches portable, self-sovereign reputation to a builder's `did:ckb` through signed claim cells — verified social accounts (GitHub, Discord, Telegram, Bluesky via OAuth) plus quest completions and event attendance from CKBoost and CKB-PoP. The DID and claim cells live on chain; OAuth verification, scoring, and dashboard rendering stay off chain. The cells are subject-owned — the holder controls every record about them and can destroy any they no longer want, reclaiming the capacity.

The SDK surface stays small: `readClaims(client, { subject })` and `writeClaim(signer, { subject, schema, payload })` added to `@ckb-ccc/did-ckb`. CKBoost sits on both sides — issuer of signed quest-completion claims and consumer of the reputation primitive — co-designed with its maintainers, testnet-only for this proposal. The work is scoped into three $2,100 milestones over 12 weeks (plus $700 setup): M1 — claim cell type script, the two SDK functions, scoring engine, schema docs; M2 — builder profile pages, CKBoost integration, webpage polish; M3 — governance gating reference, an interactive governance demo, issuance UI, and a dedicated domain. Out of scope for v1: a formal audit, mainnet type-script promotion, cross-chain export, and ZK proof-of-personhood. @Hanssen (CCC) left a supportive note on the thread about the code quality and open-source discipline behind Vellum — his personal opinion, not a DevRel endorsement.

---

## Links

- **DIR — introduction:** [talk.nervos.org/t/introducing-dir](https://talk.nervos.org/t/introducing-dir-an-open-directory-and-standing-request-for-ckb-native-ideas/10415)
- **DIR (live):** [dir-nine.vercel.app](https://dir-nine.vercel.app)
- **DIR source:** [github.com/truthixify/dir](https://github.com/truthixify/dir)
- **Vellum — `[DIS]` proposal:** [talk.nervos.org/t/dis-vellum-reputation-extension-on-did-ckb](https://talk.nervos.org/t/dis-vellum-reputation-extension-on-did-ckb/10419)
- **Vellum (live):** [vellum-lyart.vercel.app](https://vellum-lyart.vercel.app)
- **Vellum source:** [github.com/truthixify/vellum](https://github.com/truthixify/vellum)

---

## Blockers

- None this week. The Vellum proposal is now in `[DIS]` discussion, awaiting Community Fund DAO review.

---

## Plan for Week 15

- Fold `[DIS]` feedback into the Vellum proposal and move it toward a DAO decision; if accepted, start M1 — claim cell type script, `readClaims` / `writeClaim`, scoring engine, and schema docs.
- Open DIR to contributors — invite the first community idea PRs and begin specifying the on-chain layer (commitments, stakes, and bounties as attestations/cells on testnet).
- Carry forward Infern's inference and honesty-check layers so a live provider means more than a heartbeat answering.
