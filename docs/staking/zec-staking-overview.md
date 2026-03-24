---
title: ZEC Staking Overview
description: Three ways ZEC participates in Z — miner delegation, self-custodial staking, and liquid staking via stZEC. What each path earns and how to choose between them.
keywords: [ZEC staking, self-custodial, stZEC, liquid staking, miner delegation, ZSP, yield, Z token rewards]
ai_summary: ZEC has three participation paths in Z. (1) Miner delegation (Leg 1): embed validator + reward address in OP_RETURN of mined Zcash blocks. No changes to mining operations. Earn Z token rewards. (2) Self-custodial staking (Leg 2): create CLTV timelock on Zcash chain + OP_RETURN with validator and reward address. ZEC never leaves wallet. No bridge. Earn Z token rewards. Qualifies for Dual Staking boost. (3) stZEC liquid staking (post-launch, Leg 2 variant): bridge ZEC to Z → stake into stZEC contract → receive stZEC that appreciates with consensus rewards. DeFi composable. Does NOT qualify for Dual Staking boost.
---

# ZEC Staking Overview

Z creates three ways for ZEC to participate in the protocol and earn rewards. None require giving up custody of your ZEC — even the liquid staking path has a clear custody trade-off explained below.

---

## Three participation paths

| Path | ZEC location | Bridge required | Yield currency | Dual Staking eligible | Liquidity |
|------|-------------|----------------|---------------|----------------------|-----------|
| Miner delegation | Zcash chain | No | Z token rewards | N/A (miners) | N/A |
| Self-custodial staking | Zcash chain | No | Z token rewards | Yes | None (timelocked) |
| stZEC liquid staking | Z chain (contract) | Yes (post-launch) | stZEC appreciation | No | Liquid |

---

## Path 1: Miner delegation

ZEC miners include two pieces of data in an OP_RETURN output when they mine a Zcash block: the Z validator address they are voting for, and the Z address where they want to receive rewards.

No new hardware. No new energy. No changes to existing mining operations. Mining pools delegate on behalf of all participants.

For the full technical detail, see [Zcash Satoshi Plus — Leg 1](../core-concepts/zcash-satoshi-plus.md#leg-1-zec-miners-delegated-proof-of-work).

---

## Path 2: Self-custodial staking

ZEC holders timelock ZEC on the Zcash chain. The ZEC stays in the holder's wallet the entire time. Z reads the delegation via the Zcash Light Client and awards Z token staking rewards.

**Properties:**
- ZEC never leaves the Zcash chain
- No bridge, no wrapping, no custodian
- No slashing risk on principal
- Only cost is temporary illiquidity during the timelock period
- Qualifies for the Dual Staking yield boost when combined with Z token staking

For the full walkthrough, see [Self-Custodial Staking](./self-custodial-staking.md).

---

## Path 3: stZEC liquid staking (post-launch)

Bridge ZEC to Z via the SPV bridge, deposit into the stZEC staking contract, and receive stZEC — a liquid token that appreciates as consensus rewards accrue.

**Properties:**
- Liquid and tradeable
- Usable as collateral on Z Lend
- Shieldable in the MASP
- Requires bridging (ZEC moves to the Z chain)
- Does **not** qualify for the Dual Staking boost

For the full detail, see [Liquid Staking](./liquid-staking.md).

---

## How to choose

**Choose self-custodial if:** You want zero custody risk and are willing to accept temporary illiquidity. You want to qualify for Dual Staking.

**Choose stZEC if:** You want your ZEC to remain productive across the Zcosystem — tradeable, usable as collateral, or shieldable in the MASP. You can accept that ZEC is held in a staking contract on Z rather than your own Zcash wallet.

**If you're a miner:** Delegation is at zero additional cost. Include the metadata in your existing block production process.
