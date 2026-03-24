---
title: USDZ
description: USDZ is Z's stablecoin — pegged 1:1 to the US dollar, fully backed by USDC and USDT, and mints directly into the MASP as private notes.
keywords: [USDZ, stablecoin, private stablecoin, USDC, USDT, LayerZero, shielded minting, Poseidon commitment, peg stability]
ai_summary: USDZ is 1:1 USDC/USDT backed stablecoin on Z. Two minting paths: (1) Shielded — user generates Poseidon commitment off-chain, only commitment crosses LayerZero bridge (not recipient address), USDZ minted into ShieldedMinter vault, user claims via nullifier derivation proof — source chain deposit and Z balance are unlinkable; (2) Direct — standard bridge, USDZ minted to plaintext address. Redemption: burn USDZ on Z → LayerZero message → USDC/USDT released from source chain vault. Peg stability: 1:1 backing + dynamic redemption fees if velocity spikes + rate limiting on large redemptions. Source chains: Ethereum, Arbitrum, Base. LayerZero v2, three message types: 0x01 shielded mint, 0x03 direct mint, 0x02 burn+unlock.
---

# USDZ

USDZ is Z's stablecoin. Pegged 1:1 to the US dollar, fully backed by USDC and USDT reserves, and mints directly into the MASP as private notes.

The analogy: USDC is HTTP — everyone can see your balance, your transactions, your salary. USDZ is HTTPS — same functionality, private.

---

## Minting

A user deposits USDC or USDT into Z's bridge contract on the source chain (Ethereum, Arbitrum, or Base). The backing is locked in a vault on the source chain. A LayerZero message is sent to Z, and USDZ is minted at a 1:1 ratio.

### Shielded minting

USDZ can mint directly into the MASP without ever appearing on the transparent layer.

1. Generate a Poseidon commitment off-chain
2. Deposit USDC or USDT into the bridge contract with the commitment included
3. Only the commitment crosses the bridge via LayerZero — no recipient address on Z
4. On Z, the ShieldedMinter contract verifies the proof and inserts the commitment into the Merkle tree
5. USDZ is minted into the ShieldedMinter vault (not to any user address)
6. The user later claims the note by proving ownership with a nullifier derivation proof

An observer watching the source chain sees that USDC was locked and a commitment was created. They cannot link the commitment to any Z address.

### Direct minting

For users who do not need privacy, a direct mint sends USDZ to a plaintext address on Z. The recipient and amount are visible on-chain.

---

## Redemption

Send USDZ to the redemption contract on Z. A LayerZero message is sent to the source chain, and the corresponding USDC or USDT is released from the vault to the specified recipient address.

For shielded redemptions, the user generates a ZK proof of note ownership, burns the USDZ on Z, and receives USDT on the source chain. The proof verifies that the user owns the note being redeemed without revealing which note in the Merkle tree it is.

The withdrawal destination address on the source chain is always visible on-chain. The MASP origin of the funds is not.

---

## Peg stability

USDZ is backed 1:1. Every USDZ in circulation has corresponding USDC or USDT locked in source chain vaults.

Circuit breakers protect against redemption cascades:

- **Dynamic fees.** If redemption velocity spikes beyond normal parameters, fees adjust upward to slow outflows.
- **Rate limiting.** Large redemptions are subject to per-transaction and daily caps.

Reserve audits can verify that source chain vaults hold sufficient collateral at any time.

---

## Cross-chain architecture

USDZ minting uses LayerZero v2 for cross-chain messaging. Three message types handle the different flows:

| Message | Direction | What crosses | Privacy |
|---------|-----------|-------------|---------|
| Shielded mint (0x01) | Source to Z | Commitment + amount | Private — no recipient address |
| Direct mint (0x03) | Source to Z | Recipient address + amount | Public |
| Burn and unlock (0x02) | Z to source | Recipient address + amount | Public — destination visible |

Source chain adapters lock USDC/USDT in vaults. Z's ShieldedMinter or USDZ contract mints the corresponding USDZ. Peer authentication ensures only whitelisted adapters can trigger minting.

---

## USDZ in the Zcosystem

| Use | Where |
|-----|-------|
| Private dollar transfers | Inside the MASP (shielded note to shielded note) |
| Yield bearing | Deposit into yUSDZ |
| Collateral | Z Lend |
| Trading pair | Z Trade (primary pairs: ZEC/USDZ, Z/USDZ) |
| Borrowing asset | Z Lend |
