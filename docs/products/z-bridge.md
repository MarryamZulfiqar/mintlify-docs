---
title: Z Bridge
description: How assets enter Z. The ZEC SPV Bridge uses cryptographic verification with no custodian. The LayerZero Bridge handles stablecoins and other ERC-20s. Both support direct-to-MASP deposits.
keywords: [Z Bridge, ZEC bridge, SPV, LayerZero, USDC, USDT, cross-chain, shielded deposit, MASP, Zcash Light Client, Equihash, Blake2b, FROST]
ai_summary: Z has two bridge mechanisms. (1) ZEC SPV Bridge (post-launch, not at launch): trustless, no custodian. User sends ZEC on Zcash. Relayer submits SPV proof. Z's Zcash Light Client verifies block header via Equihash precompile (0x0B) and transaction inclusion via Blake2b precompile (0x0C). ZEC notes minted directly in MASP. Confirmation depth: 6 Zcash blocks (~7.5 min). Transparent Zcash transactions only at launch. ZEC withdrawals via FROST (not at launch — priority post-launch addition). (2) LayerZero Bridge (at launch): USDC, USDT, other ERC-20s from Ethereum/Arbitrum/Base. Three message types: 0x01 shielded mint (commitment only, private), 0x03 direct mint (recipient + amount, public), 0x02 burn+unlock (withdrawal, public). Stablecoin withdrawals at launch. Future: ZK-Prover Pipeline, ZK-Bridge.
---

# Z Bridge

Assets enter Z through two bridge mechanisms. The ZEC SPV Bridge handles ZEC with no custodian. The LayerZero Bridge handles stablecoins and other ERC-20 tokens. Both support deposits directly into the MASP.

---

## ZEC SPV Bridge (post-launch)

The primary bridge for ZEC. SPV verification means no custodian holds your ZEC at any point in the deposit flow — the proof is cryptographic.

### How it works

1. Send ZEC to the bridge deposit address on the Zcash chain
2. A permissionless relayer constructs an SPV (Simplified Payment Verification) proof and submits it to Z
3. Z's Zcash Light Client contract verifies the Zcash block header using the Equihash precompile (0x0B)
4. The transaction's inclusion in that block is validated via a Merkle proof using the Blake2b precompile (0x0C)
5. Upon successful verification, ZEC notes are minted directly inside the MASP — no public address on Z receives ZEC

Your ZEC arrives already shielded. No intermediate wZEC step. No visible public address on Z.

### Why SPV

Most bridges trust a group of validators or a multisig to custody deposits. SPV removes that trust assumption. Z's on-chain contracts verify cryptographically that the Zcash transaction happened and was included in a valid block. SPV is the same mechanism Bitcoin light clients have used since 2012.

### Verification infrastructure

- **Zcash Light Client:** Maintains a verified chain of Zcash block headers on Z. Relayers submit ~1,152 headers per day (one per Zcash block).
- **Equihash precompile (0x0B):** Validates Zcash proof-of-work solutions on-chain.
- **Blake2b precompile (0x0C):** Handles Merkle root verification and transaction ID computation per ZIP-244.
- **Confirmation depth:** 6 blocks (~7.5 minutes). Deposits are not credited until the Zcash transaction has sufficient confirmations.

### Current scope

The SPV bridge currently handles transparent Zcash transactions only. Shielded ZEC deposits (from Zcash's Orchard pool) require the ZK-Prover Pipeline, which is a post-launch upgrade.

---

## LayerZero Bridge (at launch)

For USDC, USDT, and other ERC-20 tokens from Ethereum, Arbitrum, and Base.

### How it works

1. User locks tokens in a vault contract on the source chain
2. A LayerZero message is sent to Z
3. Z's LayerZero adapter receives the message and mints the corresponding asset on Z

### Shielded deposits

Deposits can go directly into the MASP without appearing on Z's public layer:

1. Generate a Poseidon commitment off-chain
2. Submit the commitment alongside the token deposit on the source chain
3. Only the commitment crosses the bridge — no recipient address on Z
4. USDZ is minted into the ShieldedMinter vault
5. The user claims the note by proving ownership with a ZK proof

### Three message types

| Message | Direction | What crosses | Privacy |
|---------|-----------|-------------|---------|
| Shielded mint (0x01) | Source to Z | Commitment + amount | Private — no recipient address |
| Direct mint (0x03) | Source to Z | Recipient + amount | Public — standard bridge |
| Burn and unlock (0x02) | Z to source | Recipient + amount | Public — destination visible |

---

## Withdrawals

### ZEC withdrawals

ZEC withdrawals are not available at launch. The planned mechanism is FROST (Flexible Round-Optimized Schnorr Threshold) signatures, where Z's validator set participates as threshold signers in a t-of-n scheme. No single party can authorize a withdrawal. This is a priority post-launch addition.

### Stablecoin withdrawals (available at launch)

The user generates a ZK proof of MASP note ownership, burns USDZ on Z, and a LayerZero message triggers the release of USDC or USDT from the source chain vault. The recipient address on the destination chain is visible. The MASP origin of the funds is not.

---

## What's next

**ZK-Prover Pipeline.** An off-chain prover network that wraps Zcash's Halo 2 proofs (Pallas/Vesta curves) into BN254-compatible proofs that Z's EVM can verify. This unlocks shielded ZEC deposits from Zcash's Orchard pool.

**ZK-Bridge.** Once the prover pipeline is operational, Z can verify Zcash state transitions cryptographically on-chain, replacing the oracle model entirely.

Both are post-launch upgrades, not dependencies. Z functions on the SPV bridge (for ZEC) and LayerZero (for stablecoins) at launch.
