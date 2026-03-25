---
title: "Security Properties"
sidebarTitle: "Security Properties"
keywords: ['security', 'security properties', 'MASP', 'Groth16', 'trusted setup', 'double spend', 'Merkle tree', 'nullifier', 'ZK proofs', 'BN254']
ai_summary: "Z Protocol security properties. MASP security (contract-enforced): double-spend via nullifier mapping (Poseidon(nsk, leafIndex) stored permanently), 128-root Merkle history buffer, frontrunning prevention via boundParamsHash binding, reentrancy lock, field overflow validation against BN254 scalar field, zero commitment rejection, in-batch nullifier duplicate detection, 48h timelock on verifier changes, two-step ownership transfer, emergency halt. Design properties (architecture-enforced, not overridable): no surveillance backdoor (no master key), no asset freezing in MASP, transaction finality, counterparty privacy (viewing key reveals holder's activity only), forward-only compliance. Trusted setup: Hermez Ceremony Phase 1 (universal, 900+ participants) + circuit-specific Phase 2 ceremonies, verification keys deployed via 48h timelock."
---

# Security Properties

This page lists all security guarantees Z Protocol makes, organized by enforcement mechanism.

---

## MASP security properties

These properties are enforced at the smart contract level.

| Property | Mechanism |
|----------|-----------|
| **Double-spend prevention** | Nullifier mapping: `Poseidon(nsk, leafIndex)` published on spend, stored permanently on-chain. Duplicate nullifiers rejected. |
| **Merkle root staleness** | 128-root history buffer. Proofs valid against any of the last 128 Merkle roots are accepted. Prevents race condition between proof generation and on-chain state change. |
| **Frontrunning protection** | `boundParamsHash = keccak256(recipient, token, amount, fee, broadcaster) % FIELD` included in ZK proof's public inputs. If any bound parameter changes, the proof is invalid. |
| **Reentrancy** | Lock pattern on all pool operations (shield, transact, unshield). Re-entrant calls revert. |
| **Field overflow** | All inputs validated against the BN254 scalar field before processing. Values outside the field are rejected before reaching the ZK verifier. |
| **Zero commitment rejection** | Commitments of zero are rejected. Prevents trivial proof attacks. |
| **Batch nullifier duplicates** | In-batch duplicate detection prevents submitting the same nullifier twice within one transaction. |
| **Admin key safety** | Two-step ownership transfer on all MASP contracts. Current owner initiates. New owner must explicitly accept. |
| **Verifier change timelock** | All changes to ZK verifier contracts, verification keys, and compliance provider addresses require a 48-hour on-chain waiting period. |
| **Emergency halt** | Owner-controlled pause on MASP contract operations. Does not affect the chain, other contracts, or existing notes. Notes remain claimable after unpause. |

---

## Design properties

These properties are enforced by the architecture and cannot be overridden by protocol operators, developers, or governance.

**No surveillance backdoor.** No master key exists that can decrypt all MASP transactions. The ZK proof system and key derivation scheme make a protocol-wide decryption key impossible by design. Even a court order served to Z developers cannot produce a viewing key for a user who has not generated one.

**No asset freezing inside the MASP.** The protocol has no mechanism to freeze individual addresses or balances within the pool. The MASP contract has no function to freeze a specific note, restrict a specific address, or prevent a specific user from transacting inside the pool.

Individual token contracts may include issuer-controlled freeze functions (USDT allows Tether to freeze specific addresses). This is a property of the token contract, not of Z.

**Transaction finality.** All transactions are final, both on the public EVM layer and inside the MASP. No undo, reversal, or clawback mechanism exists.

**Counterparty privacy.** A viewing key reveals the holder's own activity (sends and receives involving their notes) but does not identify counterparties. A complete picture of any bilateral transaction requires both parties to independently provide viewing keys.

**Forward-only compliance.** Entry screening applies at the time of deposit. ASP updates take effect for future deposits and withdrawals but do not retroactively restrict existing notes inside the pool.

---

## ZK proof system security

**Proof system:** Groth16 on BN254 (alt_bn128).

**Soundness:** Groth16 is computationally sound under the q-PKE assumption. A malicious prover cannot generate a valid proof for an invalid statement — spending a note they do not own or creating assets from nothing — without breaking the underlying cryptographic assumption.

**Zero-knowledge:** Groth16 is zero-knowledge. A verifier learns nothing about the prover's inputs from the proof itself beyond the validity of the statement.

**Proof size:** ~200 bytes (constant, regardless of circuit complexity).

**Verification cost:** ~250,000 gas using the BN254 `ecPairing` precompile.

---

## Trusted setup

Groth16 requires a trusted setup ceremony. If all contributors to a ceremony collude and retain their toxic waste, an attacker could forge proofs. Multi-party ceremonies with many independent contributors mitigate this.

**Phase 1 — Universal (Powers of Tau):**
- Z reuses the Hermez Ceremony, a widely participated universal setup
- Compatible with any Groth16 circuit on BN254
- Security assumption: at least one contributor was honest and destroyed their toxic waste
- The ceremony transcript is publicly verifiable

**Phase 2 — Circuit-specific:**
- Each Z circuit (MASP transfer, shield, unshield) has its own Phase 2 ceremony
- Multiple independent contributors, each on independent hardware
- Security assumption: at least one contributor was honest
- Ceremony transcripts are published publicly

**Deployment:**
- Verification keys are deployed on-chain via 48-hour timelock
- Any change to verification keys requires a new 48-hour timelock
- Users have time to review new verification keys before they become active

---

## Audit status

Z's smart contracts are undergoing security audits prior to mainnet launch. Audit reports will be published as they become available.

Audit scope includes:
- MASP system contract
- ZSP consensus contracts (CandidateHub, ValidatorSet, StakeHub, ZcashStake, ZcashLightClient)
- ZApp contracts (Z Trade, Z Lend, USDZ, yUSDZ)
- Bridge contracts (SPV bridge, LayerZero adapter)
- Compliance contracts (ASPManager, AssociationSetProver, DepositScreener)

Bug bounty program details will be announced alongside mainnet launch.
