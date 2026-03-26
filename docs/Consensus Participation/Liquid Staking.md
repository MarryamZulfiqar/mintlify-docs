---
title: "Liquid Staking (stZEC / stZ)"
sidebarTitle: "Liquid Staking"
ai_summary: "How stZEC and stZ liquid staking receipt tokens work using a non-rebasing share price model for ShieldedPool compatibility, enabling composable use as DeFi collateral, trading, and private shielding while earning staking rewards."
keywords: ["liquid staking", "stZEC", "stZ", "ZEC", "Z token", "DeFi", "ShieldedPool", "composability", "non-rebasing", "staking receipt token", "Z Lend", "Z Trade"]
---

Liquid staking turns staked assets into composable tokens that can move through the [[The Zcosystem|Zcosystem]]. Instead of locking ZEC or Z tokens and losing access, liquid staking issues a receipt token (stZEC or stZ) that appreciates in value as staking rewards accrue. The receipt token is freely transferable, usable as collateral, and compatible with the [[ShieldedPool (MASP)|ShieldedPool]].

---

## stZEC (Post-Launch)

stZEC is a liquid staking token representing staked ZEC on Z.

**How it works:**

1. Bridge ZEC to Z via the [[Z Bridge]]
2. Deposit ZEC into the stZEC staking contract
3. Receive stZEC, which appreciates as consensus rewards accrue

stZEC uses a non-rebasing share price model. Your stZEC balance stays constant. The value per token grows. This is required for [[ShieldedPool (MASP)|ShieldedPool]] compatibility.

**What stZEC unlocks:**

- Use as collateral on [[Z Lend]]
- Trade on [[Z Trade]]
- Deposit into the [[ShieldedPool (MASP)|ShieldedPool]] for privacy

### stZEC vs. ZEC Staking

| | Self-Custodial | stZEC |
|---|---|---|
| ZEC location | Zcash chain (your wallet) | Z chain (staking contract) |
| Custody | Self-custodial | Smart contract |
| Liquidity | Locked for chosen duration | Liquid and tradeable |
| DeFi composable | No | Yes |
| Bridge required | No | Yes |
| Yield currency | Z token emissions | stZEC appreciation |
| Dual Staking eligible | Yes | No |

---

## stZ

stZ is the liquid staking token for the Z token.

**How it works:**

1. Deposit Z tokens into the stZ staking contract
2. Receive stZ, which appreciates as consensus rewards accrue

The underlying Z tokens continue participating in [[Zcash Satoshi Plus (ZSP)|ZSP]] consensus (Leg 3). stZ lets holders maintain full DeFi composability while their Z tokens contribute to network security and governance.

**What stZ unlocks:**

- Use as collateral on [[Z Lend]]
- Trade on [[Z Trade]]
- Deposit into the [[ShieldedPool (MASP)|ShieldedPool]] for privacy
