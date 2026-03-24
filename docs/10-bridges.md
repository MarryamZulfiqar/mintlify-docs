---
title: "Bridges"
slug: bridges
description: "Z connects to external assets through two bridge mechanisms. The ZEC SPV Bridge uses cryptographic verification with no custodian. The LayerZero Bridge handles non-ZEC assets."
keywords: [ZEC bridge, SPV, LayerZero, USDC, USDT, cross-chain, shielded deposit, MASP, Zcash Light Client, Equihash, Blake2b, FROST, ZK-Bridge, shielded minting]
ai_summary: |
  Z has two bridges. (1) ZEC SPV Bridge (post-launch): trustless, no custodian. User sends ZEC on Zcash. Relayer constructs SPV proof. Z's Zcash Light Client verifies block header via Equihash precompile (0x0B) + transaction inclusion via Blake2b precompile (0x0C). ZEC minted as private notes directly in MASP. Confirmation depth: 6 Zcash blocks (~7.5 min). Currently handles transparent Zcash transactions only. Shielded ZEC deposits require ZK-Prover Pipeline (Phase 2). ZEC withdrawals: not available at launch. Planned via FROST (Flexible Round-Optimized Schnorr Threshold) signatures, t-of-n scheme using Z's validator set. (2) LayerZero Bridge (at launch): for USDC, USDT, ETH, other ERC-20s from Ethereum/Arbitrum/Base. Oracle + relayer model. Shielded deposits: user generates Poseidon commitment off-chain, only commitment crosses bridge, USDZ minted into ShieldedMinter vault, user claims via ZK proof. 3 message types: 0x01 shielded mint (private), 0x03 direct mint (public), 0x02 burn+unlock (public). Stablecoin withdrawals available at launch via LayerZero. Future: ZK-Prover Pipeline → ZK-Bridge (fully trustless for shielded Zcash transactions).
---

# Bridges

Z connects to external assets through two bridge mechanisms. The ZEC SPV Bridge uses cryptographic verification with no custodian. The LayerZero Bridge uses an oracle and relayer model for non-ZEC assets.

---

## ZEC SPV Bridge

The primary bridge for ZEC. This is how ZEC enters Z.

### How It Works

1. A user sends ZEC on the Zcash chain.
2. A relayer constructs an SPV (Simplified Payment Verification) proof and submits it to Z.
3. Z's Zcash Light Client contract verifies the Zcash block header using the Equihash precompile (0x0B) and validates the transaction's inclusion in that block via a Merkle proof computed with Blake2b (0x0C).
4. Upon successful verification, ZEC is minted as private notes directly inside the MASP.

The user's ZEC arrives on Z already shielded. No public transaction on Z, no visible balance, no intermediate wrapping step. The user does not receive "wZEC" or a synthetic token. They get ZEC notes inside Z's privacy system.

### Why SPV

Most cross-chain bridges use a group of validators or a multisig to custody deposits. The user trusts the bridge operators not to steal or lose the funds. SPV verification removes that trust assumption. The proof is cryptographic: Z's on-chain contracts verify that the Zcash transaction actually happened and was included in a valid block. No custodian holds the ZEC at any point in the deposit flow.

SPV is the same mechanism Bitcoin light clients have used since 2012. It is the most battle-tested approach to cross-chain transaction verification.

### Verification Infrastructure

The SPV bridge depends on the same Zcash Light Client and precompiles described in [Consensus (ZSP)](./04-consensus-zsp.md):

- **Zcash Light Client.** Maintains a verified chain of Zcash block headers on Z. Relayers submit headers in batches (~1,152 per day). Each header is validated using the Equihash precompile before being stored.
- **Equihash precompile (0x0B).** Validates Zcash proof-of-work solutions on-chain. Memory-hard computation with a flat-rate gas cost.
- **Blake2b precompile (0x0C).** Handles Merkle root verification and transaction ID computation per ZIP-244 format.
- **Confirmation depth.** 6 blocks (~7.5 minutes). Deposits are not credited until the Zcash transaction has sufficient confirmations.

### Current Scope

The SPV bridge currently handles transparent Zcash transactions only. The bridge reads transaction data from the transparent UTXO format using ZIP-244 parsing. Support for shielded Zcash transactions (Orchard pool) requires a ZK-Prover Pipeline, covered in the What's Next section below.

---

## LayerZero Bridge

For all non-ZEC assets: USDC, USDT, ETH, and other ERC-20 tokens. This is how stablecoins and other tokens enter Z.

### How It Works

LayerZero is a cross-chain messaging protocol used by hundreds of DeFi protocols. Z integrates LayerZero v2 for assets from account-based chains (Ethereum, Arbitrum, Base) where SPV verification is not practical.

The flow:

1. A user locks tokens (USDC, USDT) in a vault on the source chain.
2. A LayerZero message is sent to Z containing the deposit information.
3. Z's LayerZero adapter receives the message and mints the corresponding asset (USDZ for stablecoins, or wrapped tokens for other assets).

### Shielded Deposits

LayerZero deposits can go directly into the MASP. The user generates a Poseidon commitment off-chain and submits it with the deposit. Only the commitment crosses the bridge. No recipient address appears on Z. The user later claims their notes by proving ownership with a ZK proof.

This is the same shielded minting mechanism described in [USDZ and yUSDZ](./08-usdz-and-yusdz.md). The commitment is inserted into the ShieldedMinter's Merkle tree. USDZ is minted into the vault (not to any user address). The user claims by deriving a nullifier and providing a Groth16 proof of Merkle membership.

### Why Not SPV for Everything

SPV verification works for UTXO-based protocols (Bitcoin, Zcash) where transaction proofs are compact Merkle paths against a block header. Account-based protocols like Ethereum have different state structures (Patricia Merkle tries) that make SPV proofs significantly larger and more expensive to verify on-chain. LayerZero's oracle and relayer model is a pragmatic choice for these assets.

### Three Message Types

| Message | Direction | What crosses | Privacy |
|---------|-----------|-------------|---------|
| Shielded mint (0x01) | Source to Z | Commitment + amount | Private. No recipient address. |
| Direct mint (0x03) | Source to Z | Recipient + amount | Public. Standard bridge. |
| Burn and unlock (0x02) | Z to source | Recipient + amount | Public. Withdrawal destination visible. |

Peer authentication ensures only whitelisted source chain adapters can trigger minting. Only the LayerZero endpoint can deliver messages to Z's adapter contract.

---

## The USDZ Minting Path

For most users, the bridge experience is abstracted:

1. Start with USDC or USDT on Ethereum (or Arbitrum or Base).
2. Bridge via LayerZero to Z.
3. Mint USDZ at 1:1.
4. USDZ arrives in the MASP as private notes.

The user sees: "I sent USDC, I got private USDZ." The underlying steps (LayerZero message, shielded commitment, Merkle tree insertion, note creation) are handled by the bridge contracts and the frontend.

---

## Withdrawals

### ZEC Withdrawals

ZEC deposits are available at launch via the SPV bridge. ZEC withdrawals are not available at launch but are a priority addition.

The planned withdrawal mechanism is FROST (Flexible Round-Optimized Schnorr Threshold) signatures, where Z's validator set participates as threshold signers in a t-of-n scheme. No single party can authorize a withdrawal. The threshold key is established through a key ceremony at deployment with a configurable signer set. The security assumption is that at least t-of-n validators are honest.

### Stablecoin Withdrawals

Stablecoin withdrawals work immediately through LayerZero. The user generates a ZK proof of note ownership, burns USDZ on Z, and a LayerZero message triggers the release of USDT from the source chain vault to the specified recipient. The withdrawal destination address is visible on the source chain.

---

## What's Next

The bridges described above handle everything at launch. Two upgrades are planned, in this order:

**ZK-Prover Pipeline.** An off-chain prover network that wraps Zcash's Halo 2 proofs (Pallas/Vesta curves) into BN254-compatible proofs that Z's EVM can verify. This unlocks shielded ZEC deposits, meaning users can bridge ZEC from Zcash's Orchard pool without revealing their balance or identity on the Zcash side. It also enables private staking from shielded Zcash addresses.

**ZK-Bridge.** Once the prover pipeline is operational, Z can verify Zcash state transitions cryptographically on-chain. This replaces the oracle model entirely, making cross-chain verification as trustless as the SPV bridge but for shielded transactions.

Both are upgrades, not dependencies. Z functions on the SPV bridge (for ZEC) and LayerZero (for stablecoins) indefinitely.

For the privacy architecture these bridges feed into, see [Privacy Architecture](./03-privacy-architecture.md). For the consensus mechanism that maintains the Zcash Light Client, see [Consensus (ZSP)](./04-consensus-zsp.md).
