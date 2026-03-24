---
title: "USDZ and yUSDZ"
slug: usdz-and-yusdz
description: "USDZ is Z's stablecoin — pegged 1:1 to the US dollar, fully backed by USDC and USDT, and mints directly into the MASP as private notes. yUSDZ is the yield-bearing version: deposit USDZ, earn yield, maintain privacy."
keywords: [USDZ, yUSDZ, stablecoin, private stablecoin, USDC, USDT, LayerZero, shielded minting, Poseidon commitment, ERC-4626, non-rebasing, yield, peg stability, cross-chain]
ai_summary: |
  USDZ is a 1:1 USDC/USDT backed stablecoin on Z. Shielded minting: user generates Poseidon commitment off-chain, commitment crosses LayerZero bridge (not a recipient address), ShieldedMinter contract verifies Groth16 proof and inserts commitment into Merkle tree, USDZ minted into ShieldedMinter vault, user later claims note via nullifier derivation proof. Direct minting: standard bridge, USDZ to plaintext address. Redemption: burn USDZ on Z → LayerZero message → USDC/USDT released from source chain vault. Peg stability: 1:1 full backing + dynamic redemption fees if velocity spikes + rate limiting on large redemptions. yUSDZ is an ERC-4626 vault. Non-rebasing share price model (like wstETH): balance stays constant, value per token grows. Non-rebasing required for MASP compatibility — rebasing changes balances, breaking ZK commitment values. Example: deposit 1000 USDZ at exchange rate 1.00 → receive 1000 yUSDZ → after 1 year at ~3.7% → exchange rate ~1.037 → 1000 yUSDZ redeemable for ~1037 USDZ. Yield sources TBD. yUSDZ can exist in MASP as shielded notes. LayerZero v2 cross-chain: 3 message types: shielded mint (0x01, private), direct mint (0x03, public), burn+unlock (0x02, public).
---

# USDZ and yUSDZ

USDZ is Z's stablecoin. It is pegged 1:1 to the US dollar, fully backed by USDC and USDT reserves, and mints directly into the MASP as private notes. yUSDZ is the yield-bearing version: deposit USDZ, earn yield, maintain privacy.

The analogy: USDC is HTTP. Everyone can see your balance, your transactions, your salary, your business expenses. USDZ is HTTPS. Same functionality. Private.

---

## Minting

A user deposits USDC or USDT into Z's bridge contract on the source chain (Ethereum, Arbitrum, Base). The backing is locked in a vault on the source chain. A LayerZero message is sent to Z, and USDZ is minted on Z at a 1:1 ratio.

### Shielded Minting

USDZ can mint directly into the MASP without ever appearing on the transparent layer. The user generates a commitment (`Poseidon(amount, blinding, secret)`) and a Groth16 proof off-chain. Only the commitment crosses the bridge via LayerZero. On Z, the ShieldedMinter contract verifies the proof and inserts the commitment into the Merkle tree. USDZ is minted into the ShieldedMinter vault (not to a user address). The user later claims the note by proving ownership with a nullifier derivation proof.

An observer watching the bridge sees that USDT was locked and a commitment was created. They cannot see who the recipient is or link the commitment to any address on Z.

### Direct Minting

For users who do not need privacy, a direct mint sends USDZ to a plaintext address. The recipient and amount are visible on-chain. This is the same flow as any standard stablecoin bridge.

---

## Redemption

Send USDZ to the redemption contract on Z. A LayerZero message is sent to the source chain, and the corresponding USDC or USDT is released from the vault to the specified recipient.

For shielded redemptions, the user generates a ZK proof of note ownership, burns the USDZ on Z, and receives USDT on the source chain. The proof verifies that the user owns the note being redeemed without revealing which note in the Merkle tree it is.

---

## Peg Stability

USDZ is backed 1:1. Every USDZ in circulation has corresponding USDC or USDT locked in source chain vaults. The solvency invariant: the USDT balance in the minter vault is always greater than or equal to the total USDZ minted (adjusted for decimal scaling).

Circuit breakers protect against redemption cascades:

- **Dynamic fees.** If redemption velocity spikes beyond normal parameters, fees adjust upward to slow outflows.
- **Rate limiting.** Large redemptions are subject to per-transaction and daily caps.

Reserve audits can confirm that source chain vaults hold sufficient collateral at any time.

---

## yUSDZ: Yield-Bearing Private Dollars

yUSDZ is an ERC-4626 vault. Deposit USDZ, receive yUSDZ shares. The exchange rate between yUSDZ and USDZ increases over time as yield accrues.

### How the Exchange Rate Works

yUSDZ uses a non-rebasing share price model (the same approach as wstETH). Your yUSDZ token balance stays constant. The value per token grows.

Example:
- Deposit 1,000 USDZ when the exchange rate is 1.00. Receive 1,000 yUSDZ.
- After one year at ~3.7% yield, the exchange rate is ~1.037.
- Your 1,000 yUSDZ is now redeemable for ~1,037 USDZ.

### Why Non-Rebasing

Standard rebasing tokens (where your balance changes) are incompatible with the MASP. ZK proof commitments lock in a specific balance at creation time. If the balance changes through rebasing, the commitment breaks and the note becomes unspendable. The share price model avoids this: the USDZ balance inside the commitment stays fixed, and the value accrues through the exchange rate.

### Yield Sources

Yield sourcing is not yet finalized. Potential sources include Z Lend borrow interest, returns from deployed USDC/USDT backing, and trading fee revenue. The protocol takes a performance fee on yield generated for yUSDZ holders, with the remainder accruing to depositors through the rising exchange rate.

### Privacy

yUSDZ can exist inside the MASP as shielded notes. Nobody outside can observe the holder's balance, yield earned, or deposit size. yUSDZ is the first yield-bearing private dollar token.

---

## Cross-Chain Architecture

USDZ minting uses LayerZero v2 for cross-chain messaging. Three message types handle the different flows:

| Message | Direction | Payload | Privacy |
|---------|-----------|---------|---------|
| Shielded mint (0x01) | Source chain to Z | Commitment + amount | Private. Only the commitment crosses. No recipient address. |
| Direct mint (0x03) | Source chain to Z | Recipient address + amount | Public. Standard bridge behavior. |
| Burn and unlock (0x02) | Z to source chain | Recipient address + USDT amount | Public. Withdrawal destination is visible. |

Source chain adapters lock USDT in vaults. Z's ShieldedMinter or USDZ contract mints the corresponding USDZ. The process reverses for redemptions. Peer authentication ensures only whitelisted adapters can trigger minting, and only the LayerZero endpoint can deliver messages.

For the full privacy architecture underlying shielded minting, see [Privacy Architecture](./03-privacy-architecture.md).
