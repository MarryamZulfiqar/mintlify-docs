---
title: "Privacy Architecture"
keywords: ['MASP', 'ShieldedPool', 'stealth addresses', 'Groth16', 'BN254', 'ZK proofs', 'Poseidon', 'nullifier', 'commitment', 'viewing key', 'broadcaster', 'privacy model', 'unlinkable', 'shielded']
ai_summary: "Z uses two privacy mechanisms. (1) MASP (Multi-Asset Shielded Pool): UTXO-based notes. Commitment = Poseidon(Poseidon(npk, tokenHash, value), random, 0). Nullifier = Poseidon(nsk, leafIndex). Merkle tree depth 16, 65,536 leaves, 128-root history buffer. Every MASP transaction includes Groth16 proof on BN254 (~250k gas to verify). (2) Stealth addresses: DeFi exits use fresh ECDH one-time addresses (ERC-5564). Activity visible but unlinkable to identity or pool balance. Broadcaster network: user submits signed proof to broadcaster who pays gas — user address never appears as msg.sender. boundParamsHash = keccak256(recipient, token, amount, fee, broadcaster) % FIELD. Key hierarchy: spendingKey = Poseidon(masterKey, 0), nsk = Poseidon(spendingKey, 0), npk = Poseidon(nsk), viewingKey = Poseidon(masterKey, 1). Groth16 trusted setup: Hermez Ceremony Phase 1 + circuit-specific Phase 2. Verification keys deployed via 48-hour timelock."
---

# Privacy Architecture

Z severs the connection between on-chain activity and identity. Assets sit in the MASP as private notes. When a user or agent needs to interact with DeFi, funds exit to a fresh stealth address with no connection to previous activity. The interaction executes from that address, and proceeds reshield back into the pool.

Different layers of Z use different methods for privacy preservation. This document is precise about how each one works.

Z's privacy architecture is designed to deepen over time. Future phases introduce shielded staking, in-pool RFQ swaps, in-pool lending and borrowing, and eventually fully private DeFi where all operations execute inside the MASP with zero footprint.

---

## Privacy levels

| Action | Privacy level | What an observer sees |
|--------|--------------|----------------------|
| Holding assets in the MASP | Fully shielded | Nothing. Balances are cryptographic commitments. No address-to-balance mapping exists. |
| Transferring inside the MASP | Fully shielded | That MASP transactions occurred (count and timing). Not who sent to whom, how much, or which asset. |
| In-pool swaps (future) | Fully shielded | Nothing about the trade. Notes swap atomically inside the pool with zero public footprint. |
| Engaging with DeFi via stealth address | Unlinkable | The transaction itself (amounts, contract calls). Not who initiated it. Each interaction uses a fresh address with no connection to previous activity. |
| Staking and delegation | Public | Standard EVM visibility. Delegation metadata is public. Private staking is in development. |
| Gas payments | Abstracted via broadcaster | The broadcaster's address and gas metadata. Not the user's identity. |

---

## The Multi-Asset Shielded Pool (MASP)

The MASP is Z's core privacy mechanism. A system-level smart contract that holds assets as UTXO-based notes. Inside the pool, balances are fully private. No outside observer can see who holds what, how much, or which asset type is present.

The MASP is a direct implementation of the note-and-nullifier architecture pioneered by Zcash, in production since 2016. Z extends it with multi-asset support and EVM integration.

### How notes work

When assets enter the MASP, the system creates a private ownership record called a note. Each note contains four fields: the owner's nullifier public key (npk), a token hash identifying the asset type, the amount, and a random blinding factor.

Only a one-way mathematical fingerprint of this note — a commitment — is stored on the blockchain. The commitment is computed using Poseidon hashing:

```
innerHash  = Poseidon(npk, tokenHash, value)
commitment = Poseidon(innerHash, random, 0)
```

Poseidon is a hash function designed for zero-knowledge circuits. It operates natively over the BN254 scalar field. The process is one-way: no one can reconstruct the note contents from the commitment.

### The commitment tree

Commitments are stored in an append-only Merkle tree with a depth of 16 levels (capacity: 65,536 leaves per tree). When a tree fills, a new tree begins. Parent nodes are computed as `Poseidon(left_child, right_child)`. The root summarizes every commitment in the pool.

To prove that a note exists in the pool, a user provides a Merkle inclusion proof: the path from their commitment up to the root. The proof confirms the note is in the tree without revealing which leaf it is.

A 128-root history buffer stores recent Merkle roots. This prevents a race condition where a new deposit changes the root between when a user generates their proof and when it is verified on-chain. Any proof valid against one of the last 128 roots is accepted.

### Nullifiers: preventing double spends

When a note is spent, the system publishes a nullifier: `Poseidon(nsk, leafIndex)`, where nsk is the owner's nullifier secret key and leafIndex is the note's position in the Merkle tree.

The nullifier is stored permanently on-chain. If the same nullifier appears twice, the second transaction is rejected.

The critical property: the nullifier cannot be linked back to the original commitment. A different formula produces the nullifier than produces the commitment, and neither reveals the other. An observer watching the nullifier set knows that notes are being spent but cannot determine which commitments they correspond to.

### Encrypted note delivery

When a transfer occurs inside the MASP, the sender needs to tell the recipient about their new note. The note details are encrypted using the recipient's viewing key and published on-chain as encrypted event data.

The recipient's wallet scans new events and attempts decryption. When decryption succeeds, the wallet recovers the note data. No one else can read the encrypted data.

### Zero-knowledge proofs

Every MASP transaction includes a Groth16 zero-knowledge proof that verifies the transaction follows all rules without revealing any underlying data. Specifically, the proof establishes:

- The sender's note exists in the commitment tree (Merkle inclusion)
- The sender possesses the correct spending authority (knows the nullifier secret key)
- The nullifier was computed correctly
- The output commitments are well-formed
- The transaction balances: inputs equal outputs, no assets created or destroyed

The MASP contract verifies the proof on-chain using the BN254 ecPairing precompile before accepting any transaction. Proof verification costs approximately 250,000 gas. A full shielded transfer (2-in-2-out) costs approximately 2.1M gas.

---

## The privacy flow

### In-pool activity

Operations inside the MASP are fully private. A user holding ZEC as shielded notes can transfer them to another user entirely within the pool. The sender's note is spent (a nullifier is published), and a new note is created for the recipient with encrypted details only they can decrypt. An outside observer sees that a MASP transaction occurred, but not who sent to whom, how much, or which asset moved.

### DeFi via stealth address

When a user or agent needs to interact with the Zcosystem (swap on Z Trade, deposit on Z Lend, borrow USDZ), the flow is:

**1. Hold in the pool.** Assets sit as private notes in the MASP. Nobody outside can see them.

**2. Exit via stealth address.** Assets move from the pool to a fresh, one-time stealth address generated through ECDH key exchange (ERC-5564). The address has no on-chain connection to the user's pool balance or any previous address.

**3. Interact.** The DeFi interaction executes from the stealth address. The transaction is visible on-chain, but no observer can trace it back to the user.

**4. Reshield.** After the interaction, assets re-enter the MASP as shielded notes.

Each interaction uses a different stealth address. An observer can see that someone made a trade at a particular address, but cannot determine who initiated it, connect it to previous activity, or link it to a pool balance.

### In-pool operations (future)

Future phases introduce operations that happen entirely inside the MASP without exiting to a stealth address. This will create two execution tiers:

- **On-chain (via stealth address):** More liquidity, standard AMM pricing. Activity is unlinkable.
- **In-pool:** Stronger privacy, less liquidity initially. Activity is fully shielded.

---

## Broadcaster network

Submitting a transaction on-chain requires gas, and gas payments would reveal the sender's address. The broadcaster network solves this. Users generate their ZK proof and transaction parameters off-chain, then send the signed data to a broadcaster. The broadcaster submits the transaction on-chain and pays the gas. The user's address never appears as `msg.sender`.

Broadcasters are compensated through a fee deducted from the user's shielded note, paid atomically within the MASP transaction. The fee is visible to the broadcaster but not to on-chain observers.

The broadcaster role is permissionless. Anyone can operate as a broadcaster. A competitive fee market keeps costs low. If all broadcasters refuse to relay a transaction, the user can always submit it directly, sacrificing that layer of privacy.

**Frontrunning prevention.** Each proof is bound to specific external parameters:

```
boundParamsHash = keccak256(recipient, token, amount, fee, broadcaster) % FIELD
```

This binding is included in the ZK proof's public inputs. A broadcaster cannot change the recipient, alter the fee, or substitute themselves. If any bound parameter changes, the proof is invalid.

---

## Key hierarchy

A single master key derives all the keys a user needs:

```
masterKey (random 256-bit)
  |
  +-- spendingKey = Poseidon(masterKey, 0)
  |     +-- nsk (nullifier secret key) = Poseidon(spendingKey, 0)
  |     +-- npk (nullifier public key) = Poseidon(nsk)
  |
  +-- viewingKey = Poseidon(masterKey, 1)
```

- **npk** identifies the note owner inside commitments. It appears nowhere else.
- **nsk** derives nullifiers to spend notes. Only the owner knows it.
- **viewingKey** decrypts encrypted note data. Sharing it gives read-only access without any ability to spend.

---

## Viewing keys and selective disclosure

Users can share a viewing key with regulators, auditors, or counterparties to provide read-only access to their shielded transaction history.

**A viewing key reveals:** complete transaction history within its scope, all asset types and amounts, current shielded balances.

**A viewing key cannot:** spend funds, view other users' activity, modify or selectively omit transactions within its scope.

This is functionally similar to a bank providing account statements to a regulator: per-user visibility without requiring the entire system's records to be public. For the full compliance architecture, see [Privacy & Compliance](/docs/compliance/privacy-and-compliance).

---

## Proof system: Groth16 on BN254

Z uses Groth16, a proving system that produces constant-size proofs (three group elements) with fast on-chain verification. Proofs verify in a single pairing check using the BN254 ecPairing precompile available on all EVM chains.

Groth16 is the most gas-efficient proving system for EVM verification. A proof is approximately 200 bytes and costs roughly 250,000 gas to verify regardless of circuit complexity. For a system where every shielded transaction requires on-chain proof verification, gas efficiency is a hard constraint.

### Trusted setup

Groth16 requires a trusted setup ceremony. Z follows a two-phase approach:

1. **Phase 1 (universal):** Z reuses the Hermez Ceremony Powers of Tau, a widely participated universal setup that works for any Groth16 circuit. The security assumption is that at least one contributor was honest and destroyed their randomness.
2. **Phase 2 (circuit-specific):** Each circuit requires its own Phase 2 ceremony with multiple independent contributors.
3. **Deployment:** Resulting verification keys are deployed on-chain via 48-hour timelock, giving users time to review before they become active.

---

## Security properties

| Property | How it is enforced |
|----------|-------------------|
| Double-spend prevention | Nullifier mapping. Once published, a nullifier permanently marks a note as spent. |
| Merkle root staleness | 128-root history buffer accepts any recent root, preventing race conditions. |
| Frontrunning protection | boundParamsHash binds proofs to specific recipients, fees, and broadcasters. |
| Reentrancy | Lock pattern on all pool operations (shield, transact, unshield). |
| Field overflow | All inputs validated against the BN254 scalar field before processing. |
| Zero commitment rejection | Commitments of zero are rejected to prevent trivial proof attacks. |
| Batch nullifier duplicates | In-batch duplicate detection prevents submitting the same nullifier twice in one transaction. |
| Admin key safety | Two-step ownership transfer. All verifier changes require 48-hour timelock. |
| Emergency halt | Owner-controlled pause on MASP contract operations. Does not affect the chain or other contracts. |

---

## Design properties

These properties are enforced by the architecture and cannot be overridden by protocol operators.

**No surveillance backdoor.** No master key exists that can decrypt all MASP transactions. Even protocol developers cannot view shielded transactions without a user's viewing key.

**No asset freezing inside the MASP.** The protocol has no mechanism to freeze individual addresses or balances within the pool. Individual token contracts may include issuer-controlled freeze functions (USDT, for example), but this is a property of the token, not Z.

**Transaction finality.** All transactions are final, both on the public layer and inside the MASP. No undo or clawback mechanism exists.

**Counterparty privacy.** A viewing key reveals the holder's own activity but does not identify their counterparties. Both parties must independently provide viewing keys for a complete picture of any transaction.

**Forward-only compliance.** Entry screening checks wallets at the time of deposit. ASP updates block withdrawals to subsequently sanctioned addresses.
