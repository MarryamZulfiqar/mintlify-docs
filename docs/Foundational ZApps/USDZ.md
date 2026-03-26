# USDZ

USDZ is Z's private stablecoin. Pegged 1:1 to the US dollar, fully backed by stablecoin reserves, and mints directly into the [[ShieldedPool (MASP)|ShieldedPool]] as private notes.

Helpful analogy:
**Today's stablecoins are HTTP.** Everyone can see your balance, your transactions, your salary.
**USDZ is HTTPS.** Same functionality, private.

---

## Minting

A user deposits USDT into Z's bridge contract on the source chain (Ethereum, Arbitrum, or Base). The backing is locked in a vault on the source chain. A LayerZero message is sent to Z, and USDZ is minted at a 1:1 ratio.

### Shielded Minting

USDZ can mint directly into the ShieldedPool without ever appearing on the transparent layer.

1. Generate a Poseidon commitment off-chain
2. Deposit USDT into the bridge contract with the commitment included
3. Only the commitment crosses the bridge via LayerZero. No recipient address on Z
4. On Z, the ShieldedMinter contract verifies the proof and inserts the commitment into the Merkle tree
5. USDZ is minted into the ShieldedMinter vault (not to any user address)
6. The user later claims the note by proving ownership with a nullifier derivation proof

An observer watching the source chain sees that stablecoins were locked and a commitment was created. They cannot link the commitment to any Z address.

### Direct Minting

For users who do not desire privacy, a direct mint sends USDZ to a plaintext address on Z. The recipient and amount are visible on-chain.

---

## Redemption

Send USDZ to the redemption contract on Z. A LayerZero message is sent to the source chain, and the corresponding backing stablecoins are released from the vault to the specified recipient address.

For shielded redemptions, the user generates a ZK proof of note ownership, burns the USDZ on Z, and receives USDT on the source chain. The proof verifies that the user owns the note being redeemed without revealing which note in the Merkle tree it is.

The withdrawal destination address on the source chain is always visible on-chain. The ShieldedPool origin of the funds is not.

---

## Peg Stability

USDZ is backed 1:1. Every USDZ in circulation has corresponding stablecoins locked in source chain vaults. Reserve audits can verify that source chain vaults hold sufficient collateral at any time.

---

## USDZ in the Zcosystem

| Use | Where |
|---|---|
| Private dollar transfers | Inside the [[ShieldedPool (MASP)|ShieldedPool]] (shielded note to shielded note) |
| Yield bearing | Deposit into [[yUSDZ]] |
| Collateral | [[Z Lend]] |
| Trading pair | [[Z Trade]] (primary pairs: ZEC/USDZ, Z/USDZ) |
| Borrowing asset | [[Z Lend]] |
