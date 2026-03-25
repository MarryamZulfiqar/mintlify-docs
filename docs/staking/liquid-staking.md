---
title: "Liquid Staking (stZEC / stZ)"
sidebarTitle: "Liquid Staking"
keywords: ['liquid staking', 'stZEC', 'stZ', 'yield', 'composable', 'collateral', 'Z Lend', 'Z Trade', 'MASP', 'staking rewards', 'DeFi composition']
ai_summary: "Liquid staking issues receipt tokens that appreciate as rewards accrue. stZEC (post-launch): bridge ZEC to Z via SPV bridge → deposit into stZEC staking contract → receive stZEC. Non-rebasing share price model (balance constant, value grows). Composable: collateral on Z Lend, tradeable on Z Trade, shieldable in MASP. Does NOT qualify for Dual Staking boost. stZEC vs self-custodial: self-custodial = ZEC on Zcash, no bridge, timelocked, no composability, qualifies for Dual Staking. stZEC = bridged, liquid, composable, does not qualify. stZ: deposit Z tokens → receive stZ → appreciates with consensus rewards. DeFi composition stack: stZEC as collateral → borrow USDZ → deposit USDZ into yUSDZ → shield everything."
---

# Liquid Staking (stZEC / stZ)

Liquid staking turns staked assets into composable tokens that can move through the Zcosystem. Instead of locking ZEC or Z tokens and losing access, liquid staking issues a receipt token (stZEC or stZ) that appreciates in value as staking rewards accrue. The receipt token is freely transferable, usable as collateral, and compatible with the MASP.

---

## stZEC (post-launch)

stZEC is a liquid staking token representing staked ZEC on Z.

**How it works:**

1. Bridge ZEC to Z via the SPV bridge (post-launch)
2. Deposit ZEC into the stZEC staking contract
3. Receive stZEC, which appreciates as consensus rewards accrue

stZEC uses a non-rebasing share price model. Your stZEC balance stays constant. The value per token grows. This is required for MASP compatibility.

**What stZEC unlocks:**

- Use as collateral on Z Lend
- Trade on Z Trade
- Deposit into the MASP for privacy

### stZEC vs. Self-Custodial Staking

| | Self-Custodial | stZEC |
|-|---------------|-------|
| ZEC location | Zcash chain (your wallet) | Z chain (staking contract) |
| Custody | Self-custodial | Smart contract |
| Liquidity | Locked for chosen duration | Liquid and tradeable |
| DeFi composable | No | Yes |
| Bridge required | No | Yes |
| Yield currency | Z token emissions | stZEC appreciation |
| Dual Staking eligible | Yes | No |

Choose self-custodial if you prioritize zero custody risk and accept illiquidity. Choose stZEC if you want your ZEC to remain productive across the Zcosystem.

---

## stZ

stZ is the liquid staking token for the Z token.

**How it works:**

1. Deposit Z tokens into the stZ staking contract
2. Receive stZ, which appreciates as consensus rewards accrue

The underlying Z tokens continue participating in ZSP consensus (Leg 3). stZ lets holders maintain full DeFi composability while their Z tokens contribute to network security and governance.

**What stZ unlocks:**

- Use as collateral on Z Lend
- Trade on Z Trade
- Deposit into the MASP for privacy

---

## The DeFi composition stack

Liquid staking tokens open a layered yield strategy:

1. **Stake ZEC into stZEC.** Earn consensus rewards as stZEC appreciates.
2. **Deposit stZEC as collateral on Z Lend.** The stZEC continues earning staking yield while serving as collateral.
3. **Borrow USDZ against it.** Access private stablecoins without selling the ZEC position.
4. **Deposit USDZ into yUSDZ.** Earn yield on the borrowed stables.
5. **Shield everything in the MASP.** Full privacy over the entire multi-yield position.

The result: ZEC price exposure, staking yield on the ZEC, borrowed stablecoins, and yield on those stablecoins.

This same composition works with stZ: stake Z, deposit stZ as collateral, borrow USDZ, earn yield on the borrowed stables.
