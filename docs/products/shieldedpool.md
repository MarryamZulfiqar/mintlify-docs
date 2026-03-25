---
title: "ShieldedPool (MASP)"
keywords: ['ShieldedPool', 'MASP', 'private balances','shield', 'commitment', 'viewing key', 'stealth address', 'ZEC', 'USDZ']
ai_summary: "The MASP is Z's core privacy layer from a user perspective. Assets enter via LayerZero (stablecoins, at launch) or ZEC SPV bridge (post-launch). Shielded minting: user generates Poseidon commitment off-chain, only commitment crosses bridge, USDZ minted into ShieldedMinter vault, user claims via ZK proof — source chain deposit and Z balance are unlinkable. Inside MASP: UTXO-based notes, balances are commitments in Merkle tree, private transfers reveal nothing. Exit: to stealth address for DeFi (handled automatically by RelayAdapter), or to plaintext address (amount and recipient visible on-chain, MASP origin not). Viewing keys: read-only, scopable to time period, cannot spend or omit."
---

# ShieldedPool (MASP)

The ShieldedPool — also called the MASP (Multi-Asset Shielded Pool) — is Z's home base. Assets deposited into the MASP become private notes. No outside observer can see who holds what, how much, or that a specific user is present at all.

For the full technical mechanics, see [Privacy Architecture](/docs/core-concepts/privacy-architecture). This page covers the user-facing view: how to enter, what happens inside, and how to exit.

---

## How to enter the MASP

### Stablecoins and other ERC-20 tokens (available at launch)

USDC, USDT, and other ERC-20 tokens enter via LayerZero from Ethereum, Arbitrum, or Base.

**Shielded minting (private path):**

1. Deposit USDC or USDT into the bridge contract on the source chain
2. Generate a Poseidon commitment off-chain — only the commitment crosses the bridge, no recipient address
3. USDZ is minted into the ShieldedMinter vault (not to any user address)
4. Claim your note by proving ownership with a ZK proof

Your deposit on the source chain and your USDZ on Z are not linkable when you use shielded minting. An observer on the source chain sees that USDC was locked and a commitment was created. They cannot link the commitment to any address on Z.

**Direct minting (no privacy):**

Standard bridge flow. USDZ is minted to a plaintext address on Z. The recipient and amount are visible on-chain.

### ZEC (post-launch)

ZEC enters via the SPV bridge — a cryptographic, non-custodial bridge. A relayer submits an SPV proof to Z's Zcash Light Client, which verifies the Zcash block header and transaction inclusion on-chain. ZEC is then minted directly inside the MASP as private notes. There is no intermediate public address on Z and no wZEC step. Your ZEC arrives already shielded.

For the full bridge architecture, see [Z Bridge](/docs/products/z-bridge).

---

## What happens inside

**Private balances.** Your balance is a commitment in a Merkle tree. No address-to-balance mapping exists on-chain. No block explorer shows your balance.

**Private transfers.** Send assets to another MASP user without revealing who you are, who the recipient is, how much you sent, or which asset type you sent. An outside observer sees that a MASP transaction occurred. Nothing else.

**DeFi access via stealth address.** When you want to trade on Z Trade, borrow on Z Lend, or interact with any ZApp, assets exit to a fresh one-time stealth address. You interact with DeFi from that address. Then assets reshield back into the MASP. The DeFi activity is visible on-chain but not linkable to you.

---

## How to check your balance

Shielded balances are not readable from the chain directly — they are encrypted. Your wallet scans MASP event data and attempts decryption using your viewing key. Notes that belong to you decrypt successfully and your balance is derived. No one else can read the encrypted event data.

---

## How to exit the MASP

**To a stealth address for DeFi:** The relevant ZApp handles this automatically via the RelayAdapter pattern. You do not manually unshield before trading or lending. The ZApp's RelayAdapter performs the unshield, executes the DeFi action, and reshields output — all atomically.

**To a plaintext address:** Send assets from the MASP to any public address. The recipient address and amount are visible on-chain. The MASP origin of the funds is not revealed.

---

## Viewing keys

A viewing key gives read-only access to your shielded transaction history without spending authority. Use cases include:

- Sharing with a tax accountant (scoped to the relevant fiscal year)
- Responding to a regulatory inquiry (scoped to specific transactions)
- Auditing by an institutional compliance team (full history within a time range)

**What a viewing key reveals:** complete transaction history within scope, all asset types and amounts, current shielded balances.

**What a viewing key cannot do:** spend funds, view other users' activity, modify or selectively omit transactions within scope.

For how viewing keys work in compliance contexts, see [Selective Disclosure](/docs/compliance/selective-disclosure).

---

## Privacy properties

| Property | Guarantee |
|----------|-----------|
| Balance privacy | No address-to-balance mapping exists on-chain |
| Transfer privacy | Sender, receiver, amount, and asset type are hidden |
| Presence privacy | No observer can confirm a specific user is in the pool |
| Nullifier unlinkability | Nullifier confirms a note was spent, not which note |
| Counterparty privacy | Your viewing key reveals your activity only, not your counterparty's |
| Protocol backdoor | None — no master key exists |
| Asset freezing | Not possible inside the MASP at the protocol level |
