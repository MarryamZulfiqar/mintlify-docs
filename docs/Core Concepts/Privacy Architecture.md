---
title: "Privacy Architecture"
sidebarTitle: "Privacy Architecture"
ai_summary: "Technical deep-dive into Z's layered privacy mechanisms: ShieldedPool note commitments, Merkle trees, nullifiers, Groth16 zero-knowledge proofs on BN254, stealth addresses, gas abstraction via broadcasters, and the master key hierarchy."
keywords: ["privacy", "ShieldedPool", "MASP", "zero-knowledge proofs", "Groth16", "BN254", "Poseidon", "nullifier", "stealth address", "viewing key", "gas abstraction"]
---

Z severs the connection between on-chain activity and identity. Assets sit in the [[ShieldedPool (MASP)]] as private notes. When a user or agent needs to interact with an app, funds exit to a fresh stealth address with no connection to previous activity. The interaction executes from that address, and proceeds re-shield back into the pool.

Different layers of the protocol use different methods for privacy preservation. This document is precise about how each one works.

Z's privacy architecture is designed to deepen over time. The long-term vision is for more of the protocol's operations to move inside the ShieldedPool.

---

## Privacy Levels

| Action | Privacy Level | What an Observer Sees |
|---|---|---|
| Holding assets in the ShieldedPool | Fully shielded | Nothing. Balances are cryptographic commitments. No address-to-balance mapping exists. |
| Transferring inside the ShieldedPool | Fully shielded | That ShieldedPool transactions occurred (count and timing). Not who sent to whom, how much, or which asset. |
| In-pool operations (future) | Fully shielded | Activity that stays inside the pool leaves no public footprint. |
| Engaging with apps via stealth address | Unlinkable | The transaction itself (amounts, contract calls). Not who initiated it. Each interaction uses a fresh address with no connection to previous activity. |
| Staking and delegation | Public | Standard EVM visibility. Delegation metadata is public. Private staking is a future upgrade. |
| Gas payments | Abstracted via broadcaster | The broadcaster's address and gas metadata are visible. The user's identity is not. |

---

## The ShieldedPool

The ShieldedPool is Z's core privacy mechanism. It is a Multi-Asset Shielded Pool: a system-level smart contract that holds multiple token types (ZEC, USDZ, stZEC, etc.) as UTXO-based notes in a single privacy system. Inside the pool, balances are fully private. No outside observer can see who holds what, how much, or which asset type is present.

The ShieldedPool is inspired by the note-and-nullifier architecture pioneered by Zcash, which has been in production since 2016. Z extends it with multi-asset support and EVM integration.

### How Notes Work

When assets enter the ShieldedPool, the system creates a private ownership record called a note. Each note contains four fields: the owner's nullifier public key (npk), a token hash identifying the asset type, the amount, and a random blinding factor.

Only a one-way mathematical fingerprint of this note (a commitment) is stored on the blockchain. The commitment is computed using Poseidon hashing:

```
innerHash = Poseidon(npk, tokenHash, value)
commitment = Poseidon(innerHash, random, 0)
```

Poseidon is a hash function designed specifically for zero-knowledge circuits. It operates natively over the BN254 scalar field, which means proving statements about Poseidon-hashed data in a ZK circuit is efficient. The process is one-way: no one can reconstruct the note contents from the commitment.

### The Commitment Tree

Commitments are stored in an append-only Merkle tree with a depth of 16 levels (capacity: 65,536 leaves per tree). When a tree fills, a new tree begins. Parent nodes are computed as `Poseidon(left_child, right_child)`. The root of the tree is a single hash that summarizes every commitment in the pool.

To prove that a note exists in the pool, a user provides a Merkle inclusion proof: the path from their commitment up to the root. The proof confirms the note is in the tree without revealing which leaf it is.

A 128-root history buffer stores recent Merkle roots. This prevents a race condition where a new deposit changes the root between when a user generates their proof and when it's verified on-chain. Any proof valid against one of the last 128 roots is accepted.

### Nullifiers: Preventing Double Spends

When a note is spent, the system publishes a nullifier: `Poseidon(nsk, leafIndex)`, where nsk is the owner's nullifier secret key and leafIndex is the note's position in the Merkle tree.

The nullifier is stored permanently on-chain in a mapping. If the same nullifier appears twice, the second transaction is rejected. This prevents double spending.

The critical property: the nullifier cannot be linked back to the original commitment by any outside observer. A different formula produces the nullifier than produces the commitment, and neither reveals the other. An observer watching the nullifier set knows that notes are being spent but cannot determine which commitments they correspond to.

### Encrypted Note Delivery

When a transfer occurs inside the ShieldedPool, the sender needs to tell the recipient about their new note. The note details (asset type, amount, blinding factor) are encrypted using the recipient's viewing key and published on-chain as encrypted event data.

The recipient's wallet scans new events and attempts decryption. When decryption succeeds, the wallet recovers the note data and updates the user's balance. No one else can read the encrypted data.

### Zero-Knowledge Proofs

Every ShieldedPool transaction includes a Groth16 zero-knowledge proof that verifies the transaction follows all rules without revealing any underlying data. Specifically, the proof establishes:

- The sender's note exists in the commitment tree (Merkle inclusion)
- The sender possesses the correct spending authority (knows the nullifier secret key)
- The nullifier was computed correctly
- The output commitments are well-formed
- The transaction balances: inputs equal outputs, no assets created or destroyed

The ShieldedPool contract verifies the proof on-chain using the BN254 ecPairing precompile before accepting any transaction. Proof verification costs approximately 250k gas. A full shielded transfer (2-in-2-out) costs approximately 2.1M gas.

---

## The Privacy Flow

### In-Pool Activity

Operations inside the ShieldedPool are fully private. A user holding ZEC as shielded notes can transfer them to another user entirely within the pool. The sender's note is spent (a nullifier is published), and a new note is created for the recipient with encrypted details only they can decrypt. An outside observer sees that a ShieldedPool transaction occurred, but not who sent to whom, how much, or which asset moved.

### Private App Interactions

When a user or agent needs to interact with the [[The Zcosystem|Zcosystem]] (swap on [[Z Trade]], deposit on [[Z Lend]], borrow [[USDZ]]), the flow is:

1. **Hold in the pool.** Assets sit as private notes in the ShieldedPool.
2. **Interact privately.** The app interaction executes without any on-chain link to the user's pool balance or identity. An observer can see that a transaction occurred, but cannot trace it back to the user.
3. **Re-shield.** After the interaction, assets re-enter the ShieldedPool as shielded notes.

The entire sequence can execute atomically through the RelayAdapter pattern, which handles unshielding, execution, and reshielding in a single transaction.

### In-Pool Operations (Future)

The long-term vision includes operations that happen entirely inside the ShieldedPool without exiting to a stealth address. This would provide stronger privacy for users who don't need to interact with the public application layer.

---

## Gas Abstraction

Submitting a transaction on-chain requires gas, and gas payments would reveal the sender's address. Z abstracts this so the user's address never appears as `msg.sender`. Users generate their ZK proof and transaction parameters off-chain, and a broadcaster submits the transaction on their behalf.

---

## Key Hierarchy

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
- **viewingKey** decrypts encrypted note data. Sharing it gives read-only access to transaction history without any ability to spend.

---

## Viewing Keys and Selective Disclosure

Users can share a viewing key with regulators, auditors, or counterparties to provide read-only access to their shielded transaction history. For the full selective disclosure model, see [[Privacy and Compliance]].

A viewing key reveals:
- Complete transaction history within its scope
- All asset types and amounts
- Current shielded balances

A viewing key cannot:
- Spend funds
- View other users' activity
- Modify or selectively omit transactions within its scope

This is functionally similar to a bank providing account statements to a regulator: per-user visibility without requiring the entire system's records to be public.

---

## Proof System: Groth16 on BN254

Z uses Groth16, a proving system that produces constant-size proofs (three group elements) with fast on-chain verification. Proofs verify in a single pairing check using the BN254 ecPairing precompile available on all EVM chains.

### Why Groth16

Groth16 is the most gas-efficient proving system for EVM verification. Proofs are constant-size and cost roughly 250k gas to verify regardless of circuit complexity. For a system where every shielded transaction requires on-chain proof verification, gas efficiency is a hard constraint.

### Trusted Setup

Groth16 requires a trusted setup ceremony. If the setup is compromised, an attacker could forge proofs (creating assets from nothing). The setup procedure mitigates this through multi-party computation (MPC):

1. **Phase 1 (universal).** Z reuses the Hermez Ceremony Powers of Tau, a widely-participated universal setup that works for any Groth16 circuit.
2. **Phase 2 (circuit-specific).** Each circuit requires its own Phase 2 ceremony with multiple independent contributors. As long as at least one contributor is honest and destroys their randomness, the setup is secure.
3. **Deployment.** The resulting verification keys are deployed on-chain via 48-hour timelock, giving users time to review before the keys become active.

---

## Security Properties

| Property | How It's Enforced |
|---|---|
| Double-spend prevention | Nullifier mapping. Once published, a nullifier permanently marks a note as spent. |
| Merkle root staleness | 128-root history buffer accepts any recent root, preventing race conditions. |
| Frontrunning protection | boundParamsHash binds proofs to specific recipients, fees, and broadcasters. |
| Reentrancy | Lock pattern on all pool operations (shield, transact, unshield). |
| Field overflow | All inputs validated against the BN254 scalar field before processing. |
| Zero commitment rejection | Commitments of zero are rejected to prevent trivial proof attacks. |
| Batch nullifier duplicates | In-batch duplicate detection prevents submitting the same nullifier twice in one transaction. |
| Admin key safety | Two-step ownership transfer. All verifier changes require 48-hour timelock. |

---

## Design Properties

**No surveillance backdoor.** No master key exists that can decrypt all ShieldedPool transactions. Even protocol developers cannot view shielded transactions without a user's viewing key.

**No asset freezing inside the ShieldedPool.** The protocol has no mechanism to freeze individual addresses or balances within the pool. Individual token contracts may include issuer-controlled freeze functions (e.g., USDT), but this is a property of the token, not Z.

**Transaction finality.** All transactions are final, both on the public layer and inside the ShieldedPool. No undo or clawback mechanism exists.

**Counterparty privacy.** A viewing key reveals the holder's own activity but does not identify their counterparties. Both parties must independently provide viewing keys for a complete picture of any transaction.

**Forward-only compliance.** Entry screening checks wallets at the time of deposit. ASP updates block withdrawals to subsequently-sanctioned addresses.

For how Z handles compliance within these constraints, see [[Privacy and Compliance]].
