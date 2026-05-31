# CKBuilder Dev Log

This repo tracks my learning journey as part of the [CKBuilder track](https://talk.nervos.org/t/announcing-the-launch-of-the-nervos-community-catalyst/8759) under the Community Keeps Building initiative by Nervos Community Catalyst.

## About the Program

CKBuilder is a 3-month structured program for developers new to blockchain development, focused on building skills on [Nervos CKB](https://docs.nervos.org/docs/tech-explanation/nervos-blockchain). It covers everything from core CKB concepts to writing and deploying scripts, with a capstone project in month 3.

## My Goals

- Build a solid understanding of CKB's cell model, transaction structure, and scripting system
- Get hands-on with the CCC SDK and CKB tooling
- Complete all beginner and intermediate learning materials
- Ship a real application as my capstone that demonstrates real CKB functionality

## Weekly Reports

| Week | Period | Highlights |
|------|--------|------------|
| [Week 1](weekly-reports/week-1/) | Mar 23–29 | Environment setup, CKB Academy Lesson 1, core concepts |
| [Week 2](weekly-reports/week-2/) | Mar 30–Apr 5 | Lock script in Rust, "Learn CKB in 45 Minutes", Grid3 on-chain Tic Tac Toe |
| [Week 3](weekly-reports/week-3/) | Apr 6–11 | Built and shipped Haven Protocol — privacy reputation layer on CKB |
| [Week 4](weekly-reports/week-4/) | Apr 12–18 | CKB integration into Wraith Protocol — stealth-lock, names-type, SDK, demo |
| [Week 5](weekly-reports/week-5/) | Apr 19–25 | Grid3 fixes — draw payouts, fee structure, dual script differentiation |
| [Week 6](weekly-reports/week-6/) | Apr 26–May 2 | Haven Protocol fixes — critical proof verification, type script enforcement, deposit validation |
| [Week 7](weekly-reports/week-7/) | May 3–9 | Fiber Network — payment channel network foundations |
| [Week 8](weekly-reports/week-8/) | May 10–16 | Vellum — `did:ckb` dashboard + draft `@ckb-ccc/identity` SDK package |
| [Week 9](weekly-reports/week-9/) | May 17–23 | CKB Action Links — draft protocol for shareable CKB transaction URLs, reference SDK/server/client |
| [Week 10](weekly-reports/week-10/) | May 24–30 | CKB Action Links — UI/UX iteration from forum feedback (typed forms, sign preview, error mapping, mobile) |

## Projects

- **[Learn CKB in 45 Minutes](https://github.com/truthixify/learn-ckb-in-45-minutes)** — Structured learning guide with 13 chapters, 40 questions, and a working Rust script
- **[Grid3](https://github.com/truthixify/grid3)** — Fully on-chain Tic Tac Toe on CKB testnet ([live](https://grid3-ckb.vercel.app/))
- **[Haven Protocol](https://github.com/truthixify/haven)** — Privacy reputation layer on CKB with TEE + SP1 proofs ([live](https://haven-protocol.vercel.app))
- **[Wraith Protocol](https://github.com/wraith-protocol)** — Multichain stealth address platform with CKB support — custom scripts, SDK, demo ([live](https://demo.usewraith.xyz))
- **[Vellum](https://github.com/truthixify/vellum)** — Reference dashboard for `did:ckb` (WIP-01) + draft `@ckb-ccc/identity` SDK package ([live](https://vellum-lyart.vercel.app))
- **[CKB Action Links](https://github.com/truthixify/ckb-actions)** — Draft protocol for shareable CKB transaction URLs, with SDK, server, and client ([client](https://ckb-actions.vercel.app) · [endpoint](https://ckb-actions.onrender.com) · [forum](https://talk.nervos.org/t/ckb-action-links-a-draft-protocol-for-shareable-ckb-transaction-urls/10315))

## How This Repo Is Organized

```
weekly-reports/
  week-1/
    README.md       # Weekly progress report
    images/         # Screenshots and proof of completion
  week-2/
    README.md       # Weekly progress report
    images/         # Screenshots
  week-3/
    README.md       # Weekly progress report
  week-4/
    README.md       # Weekly progress report
  week-5/
    README.md       # Weekly progress report
  week-6/
    README.md       # Weekly progress report
  week-7/
    README.md       # Weekly progress report
  week-8/
    README.md       # Weekly progress report
  week-9/
    README.md       # Weekly progress report
  week-10/
    README.md       # Weekly progress report
```

## Resources

- [CKBuilder Handbook](https://docs.google.com/document/d/1aFHXU1ZL1MyIbBAIVRjG6stqdWwPUPyHV90O1QNwY-M/edit?usp=sharing)
- [Nervos CKB Docs](https://docs.nervos.org)
- [CKB Academy](https://academy.ckb.dev/courses)
- [CCC Docs](https://docs.ckbccc.com/docs/CCC)
