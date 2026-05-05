# Week 6 Report — CKBuilder Track

**Period:** April 26 – May 2, 2026
**Participant:** Truth
**Track:** Builder

---

## Summary

Addressed code review feedback on **Haven Protocol** (privacy reputation layer on CKB) from the CKBuilder review on [issue #9](https://github.com/Nervos-Community-Catalyst/CKBuilder-projects/issues/9#issuecomment-4277404335). Fixes included a critical SP1 proof verification vulnerability, type script enforcement, deposit balance validation, and code simplification.

---

## What I Fixed

### [CRITICAL] SP1 Proof Verification — vk_hash and journal_bytes Validation

**Review feedback:** It is incorrect that `vk_hash` comes from the witness, as anyone can substitute their own trivial script (e.g. always-success) in its place. It is also incorrect that `journal_bytes` (the public inputs) is not checked.

**Fix:** Moved `vk_hash` validation away from witness-sourced data. The type script now verifies `vk_hash` against the on-chain registry cell's program hash rather than trusting the witness. Added explicit validation of `journal_bytes` (public inputs) to ensure the proof actually corresponds to the claimed score computation. This prevents an attacker from substituting a trivial verification key or forged public inputs to bypass proof verification.

### Type Script Output Enforcement

**Review feedback:** `verify_type_script_on_output` only requires that one type script exists but does not restrict which type script it is. The rule is effectively useless.

**Fix:** Updated the verification to check that the output cell's type script matches the expected Haven type script hash specifically, not just that any type script is present. This ensures the TEE update path cannot be abused to strip or replace the type script.

### Simplified Cell Data Loading

**Review feedback:** In `load_input_score_cell_data` / `load_output_score_cell_data`, data can be loaded directly with `load_cell_data(0, Source::GroupInput)`, without needing `find_cell_index_by_type`.

**Fix:** Replaced the indirect lookup via `find_cell_index_by_type` with direct `load_cell_data(0, Source::GroupInput)` and `load_cell_data(0, Source::GroupOutput)`. This simplifies the code and removes unnecessary cell index searches since the type script already runs in the context of the correct cell group.

### Deposit Balance Top-up Enforcement

**Review feedback:** There is no rule enforcing `deposit_balance` top-up. `is_topup_only` alone is not sufficient.

**Fix:** Added explicit validation in the type script that verifies the output cell's capacity has increased by at least the claimed deposit amount during top-up operations. The script now checks that `output_deposit_balance >= input_deposit_balance + actual_capacity_increase`, preventing fake top-ups where the deposit field is inflated without corresponding CKB being added.

### Removed Unnecessary Constant-Time Comparison

**Review feedback:** Constant-time comparison is not needed for the identity check in the lock script.

**Fix:** Replaced the constant-time comparison with a standard equality check. Constant-time comparison is a defense against timing side-channel attacks, which are not applicable in the CKB VM execution context since script execution is not observable by an attacker in real-time.

---

## Links

- **Dashboard:** [haven-protocol.vercel.app](https://haven-protocol.vercel.app)
- **GitHub:** [github.com/truthixify/haven](https://github.com/truthixify/haven)
- **SDK:** [npmjs.com/package/@haven-protocol-ckb/sdk](https://www.npmjs.com/package/@haven-protocol-ckb/sdk)
- **Review:** [CKBuilder-projects#9](https://github.com/Nervos-Community-Catalyst/CKBuilder-projects/issues/9#issuecomment-4277404335)

---

## Blockers

- None this week.

---

## Plan for Week 7

- Explore new project ideas to build on CKB
- Research underexplored use cases that leverage CKB's cell model
