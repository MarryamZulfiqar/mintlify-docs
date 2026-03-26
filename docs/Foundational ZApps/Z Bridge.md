---
title: "Z Bridge"
sidebarTitle: "Z Bridge"
ai_summary: "Overview of Z's bridging infrastructure — the ZEC Bridge for native ZEC and the LayerZero Bridge for ERC-20 tokens — including shielded deposit flows that mint assets directly into the ShieldedPool with no public recipient address."
keywords: ["Z Bridge", "ZEC", "LayerZero", "bridging", "cross-chain", "ShieldedPool", "shielded deposits", "ERC-20", "USDZ", "shielded minting"]
---

Assets enter Z through bridging infrastructure. The ZEC Bridge handles ZEC. The LayerZero Bridge handles stablecoins and other ERC-20 tokens. Both support deposits directly into the [[ShieldedPool (MASP)|ShieldedPool]].

---

## ZEC Bridge

The primary bridge for ZEC. The goal is for ZEC to arrive on Z already shielded inside the ShieldedPool as private notes, with no intermediate public address and no custodian holding funds during the deposit flow.

Bridge implementation details are in development.

---

## LayerZero Bridge

For USDT and other ERC-20 tokens from external chains.

### How It Works

1. User locks tokens in a vault contract on the source chain
2. A LayerZero message is sent to Z
3. Z's LayerZero adapter receives the message and mints the corresponding asset on Z

### Shielded Deposits

Deposits can go directly into the [[ShieldedPool (MASP)|ShieldedPool]] without appearing on Z's public layer:

1. Generate a Poseidon commitment off-chain
2. Submit the commitment alongside the token deposit on the source chain
3. Only the commitment crosses the bridge. No recipient address on Z
4. [[USDZ]] is minted into the ShieldedMinter vault
5. The user claims the note by proving ownership with a ZK proof

### Three Message Types

| Message                | Direction   | What crosses        | Privacy                       |
| ---------------------- | ----------- | ------------------- | ----------------------------- |
| Shielded mint (0x01)   | Source to Z | Commitment + amount | Private. No recipient address |
| Direct mint (0x03)     | Source to Z | Recipient + amount  | Public. Standard bridge       |
| Burn and unlock (0x02) | Z to source | Recipient + amount  | Public. Destination visible   |

---

## What's Next

**Shielded ZEC deposits.** A future upgrade to accept ZEC deposits directly from Zcash's Orchard shielded pool, preserving privacy end to end.
