---
title: "Z Stake"
description: "Z Stake issues liquid staking tokens — stZEC (post-launch) for ZEC and stZ for Z tokens. Staked assets remain composable across the Zcosystem while earning consensus rewards."
keywords: ['Z Stake', 'stZEC', 'stZ', 'liquid staking', 'yield', 'composable', 'collateral', 'Z Lend', 'Z Trade', 'MASP']
ai_summary: "Z Stake issues two liquid staking tokens. stZEC (post-launch): bridge ZEC to Z → deposit into stZEC staking contract → receive stZEC → appreciation as consensus rewards accrue. Non-rebasing: balance stays constant, value per token grows. Composable: collateral on Z Lend, tradeable on Z Trade, shieldable in MASP. stZEC does NOT qualify for Dual Staking boost. stZ: deposit Z tokens → receive stZ → appreciation as consensus rewards accrue. Same composability as stZEC. Both are non-rebasing for MASP compatibility."
---

# Z Stake

Z Stake issues liquid staking tokens for ZEC and Z tokens. Staked assets remain fully composable — use them as collateral, trade them, or shield them, all while earning staking rewards.

---

## stZEC (post-launch)

stZEC is a liquid staking token representing staked ZEC on Z.

**How it works:**

1. Bridge ZEC to Z via the SPV bridge (post-launch)
2. Deposit ZEC into the stZEC staking contract
3. Receive stZEC, which appreciates in value as consensus rewards accrue

stZEC uses a non-rebasing share price model: your token balance stays constant, and the value per token grows as rewards accrue. This is required for MASP compatibility — rebasing tokens change balances, which would break ZK proof commitments that lock in a specific balance value at creation.

**What stZEC unlocks:**

- Use as collateral on Z Lend
- Trade on Z Trade (stZEC/USDZ pair)
- Shield in the MASP as private notes
- Maintain liquidity — unlike timelocked ZEC, stZEC is transferable

**Note:** stZEC does not qualify for the Dual Staking yield boost. Only self-custodial ZEC (CLTV timelock on the Zcash chain) qualifies. See [Dual Staking](/docs/staking/dual-staking).

### stZEC vs. self-custodial staking

| | Self-Custodial | stZEC |
|-|---------------|-------|
| ZEC location | Zcash chain (your wallet) | Z chain (staking contract) |
| Custody | Self-custodial | Smart contract |
| Bridge required | No | Yes |
| Liquidity | Locked (timelocked) | Liquid and tradeable |
| DeFi composable | No | Yes |
| Dual Staking eligible | Yes | No |
| Yield currency | Z token emissions | stZEC appreciation |

Self-custodial staking is for holders who want zero custody risk and accept illiquidity. stZEC is for holders who want their ZEC to remain productive across the Zcosystem.

---

## stZ

stZ is the liquid staking token for the Z token.

**How it works:**

1. Deposit Z tokens into the stZ staking contract
2. Receive stZ, which appreciates as consensus rewards accrue

The underlying Z tokens continue participating in ZSP consensus (Leg 3). stZ lets holders maintain full DeFi composability while their Z tokens contribute to network security.

**What stZ unlocks:**

- Use as collateral on Z Lend
- Trade on Z Trade
- Shield in the MASP
- Earn consensus rewards passively
