---
title: "Liquid Staking"
slug: liquid-staking
description: "Liquid staking turns staked assets into composable tokens. stZEC represents staked ZEC on Z. stZ represents staked Z tokens. Both appreciate in value as staking rewards accrue and integrate across the Zcosystem."
keywords: [liquid staking, stZEC, stZ, yield, composable, collateral, Z Lend, Z Trade, MASP, DeFi composition, staking rewards]
ai_summary: |
  Liquid staking issues receipt tokens that appreciate as rewards accrue. stZEC: bridge ZEC to Z via SPV bridge → deposit into stZEC staking contract → receive stZEC → appreciates in value as consensus rewards accrue. stZEC is composable: trade on Z Trade, use as collateral on Z Lend, deposit into MASP. stZEC vs self-custodial: self-custodial = ZEC stays on Zcash chain, no bridge, self-custodial, locked, no DeFi composability, yields Z token emissions, qualifies for Dual Staking. stZEC = bridged to Z, held in contract, liquid, fully composable, yields stZEC appreciation, does NOT qualify for Dual Staking boost. stZ: deposit Z tokens → receive stZ → appreciates as consensus rewards accrue. Composable same as stZEC. DeFi composition stack: stake ZEC into stZEC (staking yield) → deposit stZEC as collateral on Z Lend → borrow USDZ (stablecoin access without selling) → deposit USDZ into yUSDZ (yield on stables) — all positions can be shielded in MASP.
---

# Liquid Staking

Liquid staking turns staked assets into composable tokens that can move through the rest of the Zcosystem. Instead of locking ZEC or Z tokens and losing access to them, liquid staking issues a receipt token (stZEC or stZ) that appreciates in value as staking rewards accrue. The receipt token is freely transferable, usable as collateral, and compatible with the MASP.

---

## stZEC

stZEC is a liquid staking token representing staked ZEC on Z.

**How it works:**

1. Bridge ZEC to Z via the SPV bridge
2. Deposit ZEC into the stZEC staking contract
3. Receive stZEC, which appreciates in value as consensus rewards accrue

**What stZEC unlocks:**

- Trade it on Z Trade
- Use it as collateral on Z Lend
- Deposit it into the MASP for privacy

### stZEC vs. Self-Custodial Staking

ZEC holders have two staking paths. The right choice depends on whether they prioritize custody or composability.

| | Self-Custodial | stZEC |
|-|---------------|-------|
| ZEC location | Stays on Zcash chain | Bridged to Z |
| Custody | Self-custodial (no third party) | Staking contract |
| Liquidity | Locked for chosen duration | Liquid (tradeable, composable) |
| DeFi composability | None | Full (collateral, trading, privacy) |
| Bridge required | No | Yes |
| Yield source | Z token emissions | Consensus rewards (stZEC appreciation) |
| Dual Staking eligible | Yes | No |

Self-custodial staking is for holders who want zero custody risk and are willing to accept illiquidity. stZEC is for holders who want their ZEC to remain productive across the Zcosystem.

For how self-custodial staking works, see [Consensus (ZSP) — Leg 2](./04-consensus-zsp.md#leg-2-zec-holders-self-custodial-staking).

---

## stZ

stZ is the liquid staking token for the Z token.

**How it works:**

1. Deposit Z tokens into the stZ staking contract
2. Receive stZ, which appreciates as consensus rewards accrue

**What stZ unlocks:**

- Trade it on Z Trade
- Use it as collateral on Z Lend
- Deposit it into the MASP for privacy
- The underlying Z continues participating in consensus

stZ lets holders maintain full DeFi composability while their Z tokens contribute to network security and governance.

---

## The DeFi Composition Stack

Liquid staking tokens open a layered yield strategy:

1. **Stake ZEC into stZEC.** Earn consensus rewards as stZEC appreciates.
2. **Deposit stZEC as collateral on Z Lend.** The stZEC continues earning staking yield while serving as collateral.
3. **Borrow USDZ against it.** Access private stablecoins without selling the ZEC position.
4. **Deposit USDZ into yUSDZ.** Earn yield on the borrowed stables.

The result: ZEC price exposure, staking yield on the ZEC, borrowed stablecoins, and yield on those stablecoins. All within one integrated stack. All with access to the MASP for privacy at every step.

This composition works because Z's applications are built as a coordinated system. stZEC is accepted as collateral on Z Lend because the applications are designed together. USDZ borrows generate protocol revenue because the lending protocol captures the interest.

The same composition works with stZ: stake Z, deposit stZ as collateral, borrow USDZ, earn yield on the borrowed stables.

For the consensus mechanics behind staking rewards, see [Consensus (ZSP)](./04-consensus-zsp.md). For Dual Staking, see [Dual Staking](./05-dual-staking.md).
