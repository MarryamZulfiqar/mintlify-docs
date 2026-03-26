---
title: "ShieldedPool (MASP)"
sidebarTitle: "ShieldedPool"
ai_summary: "User-facing overview of Z's Multi-Asset Shielded Pool — how to enter via shielded or direct minting, what happens inside (private balances, transfers, DeFi via stealth addresses), how to check balances and exit, and viewing key capabilities."
keywords: ["ShieldedPool", "MASP", "privacy", "shielded notes", "viewing key", "ZEC", "USDZ", "stealth address", "multi-asset", "shielded minting"]
---

The ShieldedPool, also called the MASP (Multi-Asset Shielded Pool), is Z's home base. Assets deposited into the ShieldedPool become private notes. No outside observer can see who holds what, how much, or that a specific user is present at all.

For more technical mechanics, see [[Privacy Architecture]]. This page covers the user-facing view: how to enter, what happens inside, and how to exit.

---

## How to Enter the ShieldedPool

### Stablecoins and Other ERC-20 Tokens (Available at Launch)

USDT and other ERC-20 tokens enter via LayerZero from Ethereum, Arbitrum, or Base.

**Shielded minting (private path):**

1. Deposit USDT into the bridge contract on the source chain
2. Generate a Poseidon commitment off-chain (only the commitment crosses the bridge, no recipient address)
3. USDZ is minted into the ShieldedMinter vault (not to any user address)
4. Claim your note by proving ownership with a ZK proof

Your deposit on the source chain and your USDZ on Z are not linkable when you use shielded minting. An observer on the source chain sees that stablecoins were locked and a commitment was created. They cannot link the commitment to any address on Z.

### ZEC (Post-Launch)

ZEC enters Z via the [[Z Bridge]]. The goal is for ZEC to arrive already shielded inside the ShieldedPool as private notes with no intermediate public address on Z. Bridge implementation details are in development.

For the full bridge architecture, see [[Z Bridge]].

---

## What Happens Inside

**Private balances.** Your balance is a commitment in a Merkle tree. No address-to-balance mapping exists on-chain. No block explorer shows your balance.

**Private transfers.** Send assets to another ShieldedPool user without revealing who you are, who the recipient is, how much you sent, or which asset type you sent. An outside observer sees that a ShieldedPool transaction occurred. Nothing else.

**DeFi access via stealth address.** When you want to trade on [[Z Trade]], borrow on [[Z Lend]], or interact with any ZApp, assets exit to a fresh one-time stealth address. You interact with DeFi from that address. Then assets reshield back into the ShieldedPool. The DeFi activity is visible on-chain but not linkable to you.

---

## How to Check Your Balance

Shielded balances are not readable from the chain directly. They are encrypted. Your wallet scans ShieldedPool event data and attempts decryption using your viewing key. Notes that belong to you decrypt successfully and your balance is derived. No one else can read the encrypted event data.

---

## How to Exit the ShieldedPool

**To a stealth address for DeFi:** The RelayAdapter pattern handles unshielding, executing the DeFi action, and reshielding output in one atomic transaction.

**To a plaintext address:** Send assets from the ShieldedPool to any public address. The recipient address and amount are visible on-chain. The ShieldedPool origin of the funds is not revealed.

---

## Viewing Keys

A viewing key gives read-only access to your shielded transaction history without spending authority. Use cases include:

- Sharing with a tax accountant (scoped to the relevant fiscal year)
- Responding to a regulatory inquiry (scoped to specific transactions)
- Auditing by an institutional compliance team (full history within a time range)

**What a viewing key reveals:** complete transaction history within scope, all asset types and amounts, current shielded balances.

**What a viewing key cannot do:** spend funds, view other users' activity, modify or selectively omit transactions within scope.

For how viewing keys work in compliance contexts, see [[Privacy and Compliance]].

---

## Privacy Properties

| Property                | Guarantee                                                            |
| ----------------------- | -------------------------------------------------------------------- |
| Balance privacy         | No address-to-balance mapping exists on-chain                        |
| Transfer privacy        | Sender, receiver, amount, and asset type are hidden                  |
| Presence privacy        | No observer can confirm a specific user is in the pool               |
| Nullifier unlinkability | Nullifier confirms a note was spent, not which note                  |
| Counterparty privacy    | Your viewing key reveals your activity only, not your counterparty's |
| Protocol backdoor       | None. No master key exists                                           |
| Asset freezing          | Not possible inside the ShieldedPool at the protocol level           |
