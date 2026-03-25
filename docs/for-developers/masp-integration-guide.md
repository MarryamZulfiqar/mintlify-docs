---
title: "MASP Integration Guide"
sidebarTitle: "MASP Integration Guide"
keywords: ['MASP', 'integration', 'shield', 'transact', 'unshield', 'commitments', 'broadcaster', 'ShieldedPool', 'developer']
ai_summary: "MASP integration conceptual guide. Three integration depths: (1) Read-only — query MASP state (Merkle root, nullifier set) from any contract. (2) User-facing app — use the broadcaster pattern and ZK proofs generated client-side. (3) ZApp with private execution — implement RelayAdapter for atomic unshield+execute+reshield. MASP holds UTXO-based notes. Commitment = Poseidon(Poseidon(npk, tokenHash, value), random, 0). Nullifier = Poseidon(nsk, leafIndex). Spending a note publishes its nullifier. New notes are inserted as commitments. 128-root Merkle history buffer prevents race conditions. Broadcaster pattern: user generates ZK proof, sends to broadcaster, broadcaster pays gas."
---

# MASP Integration Guide

This page explains how to build contracts and applications that interact with the ShieldedPool. It assumes you've read [Privacy Architecture](/docs/core-concepts/privacy-architecture) and understand the note-and-nullifier model.

---

## Three integration depths

| Depth | When to use | What it involves |
|-------|-------------|-----------------|
| Read-only | Query MASP state from a contract | Call view functions on the MASP contract |
| User-facing app | Let users make private transfers | Client-side proof generation + broadcaster |
| ZApp with private execution | Build DeFi that executes from the MASP | Implement the RelayAdapter pattern |

---

## The note model

MASP assets are UTXO-based notes, not ERC-20 balances. This is the most important architectural difference for developers coming from standard EVM.

A note contains:
- The owner's nullifier public key (`npk`)
- A token hash identifying the asset type
- The amount
- A random blinding factor

The commitment stored on-chain:
```
innerHash  = Poseidon(npk, tokenHash, value)
commitment = Poseidon(innerHash, random, 0)
```

Spending a note publishes its nullifier: `Poseidon(nsk, leafIndex)`. The MASP rejects any transaction that reuses a published nullifier — this is double-spend prevention.

**`balanceOf()` does not work inside the MASP.** To read a shielded balance, a client scans encrypted MASP events and decrypts them using the owner's viewing key. This scan happens off-chain.

---

## Reading MASP state from a contract

Any contract can query the MASP's public state:

```solidity
interface IMASP {
    // Get the current Merkle root
    function getMerkleRoot() external view returns (bytes32);
    
    // Check if a nullifier has been spent
    function isNullifierSpent(bytes32 nullifier) external view returns (bool);
    
    // Get a historical root (128-root buffer)
    function getMerkleRoot(uint256 blockNumber) external view returns (bytes32);
}
```

The 128-root history buffer allows ZK proofs to reference recent roots even if the tree has been updated since the proof was generated. This prevents race conditions in high-throughput environments.

> **⚠ Engineering input required:** The deployed MASP contract address and the exact interface ABI must be provided by the engineering team. Do not hardcode addresses from this document.

---

## User-facing app integration

For applications that let users make private transfers without building a ZApp:

1. **Client-side proof generation.** The user's wallet generates a Groth16 ZK proof off-chain using the MASP circuit and their private keys. The proof commits to the input notes being spent and the output notes being created.

2. **Broadcaster submission.** The signed proof is sent to a broadcaster rather than submitted directly by the user. The broadcaster pays gas and submits on-chain. The user's address never appears as `msg.sender`.

3. **MASP verification.** The MASP contract verifies the Groth16 proof on-chain using the BN254 ecPairing precompile (~250,000 gas). On success, the input note nullifiers are published and output note commitments are inserted into the Merkle tree.

> **⚠ Engineering input required:** The proof generation toolchain, circuit artifacts, ZK proving library, and the exact MASP function signatures (`shield`, `transact`, `unshield`) require documentation from the engineering team. The privacy architecture doc describes the cryptographic model; implementation details require team input.

---

## ZApp with private execution: RelayAdapter pattern

For DeFi applications that need to execute atomically from the MASP:

The RelayAdapter contract accepts a ZK proof from a user, verifies it through the MASP, executes a DeFi action (swap, lend, borrow) atomically, and reshields the output back into the MASP — all in one transaction.

For the full implementation reference, see [RelayAdapter Pattern](/docs/for-developers/relayAdapter-pattern).

---

## Broadcaster interaction

For applications that route user transactions through broadcasters:

```
User wallet:
  1. Generate ZK proof and transaction parameters off-chain
  2. Sign the transaction parameters
  3. Send signed proof to broadcaster API

Broadcaster:
  4. Verify proof validity locally (no gas cost if invalid)
  5. Submit transaction on-chain
  6. Receive fee from user's shielded note (atomic, within the MASP transaction)
```

For the broadcaster API specification and how to run your own broadcaster, see [Broadcaster Network](/docs/for-developers/broadcaster-network).

---

## Common integration patterns

| Pattern | Description |
|---------|-------------|
| Shield ERC-20 | User approves MASP → MASP locks tokens → commitment inserted into Merkle tree → encrypted note delivered to recipient |
| Private transfer | User spends input notes (nullifiers published) → new notes created for recipient (encrypted, inserted into tree) |
| Unshield to address | User spends notes → tokens transferred to plaintext recipient address → amount and address visible on-chain |
| ZApp private execution | RelayAdapter: unshield → execute DeFi → reshield, all atomic |

> **⚠ Engineering input required:** Working code examples for each pattern require the actual MASP ABI, proof circuit artifacts, and tested reference implementations from the engineering team.
