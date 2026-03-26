# Z Stake

Z Stake issues liquid staking tokens for ZEC and Z tokens. Staked assets remain fully composable. Use them as collateral, trade them, or shield them, all while earning staking rewards.

---

## stZEC (Post-Launch)

stZEC is a liquid staking token representing staked ZEC on Z.

**How it works:**

1. Bridge ZEC to Z via the [[Z Bridge]]
2. Deposit ZEC into the stZEC staking contract
3. Receive stZEC, which appreciates in value as consensus rewards accrue

stZEC uses a non-rebasing share price model: your token balance stays constant, and the value per token grows as rewards accrue.

**What stZEC unlocks:**

- Use as collateral on [[Z Lend]]
- Trade on [[Z Trade]] (stZEC/USDZ pair)
- Shield in the [[ShieldedPool (MASP)|ShieldedPool]] as private notes
- Maintain liquidity. Unlike timelocked ZEC, stZEC is transferable

**Note:** stZEC does not qualify for the [[Dual Staking]] yield boost. Only self-custodial ZEC (CLTV timelock on the Zcash chain) qualifies. See [[Dual Staking]].

### stZEC vs. ZEC Staking

| | Self-Custodial | stZEC |
|---|---|---|
| ZEC location | Zcash chain (your wallet) | Z chain (staking contract) |
| Custody | Self-custodial | Smart contract |
| Bridge required | No | Yes |
| Liquidity | Locked (timelocked) | Liquid and tradeable |
| DeFi composable | No | Yes |
| Dual Staking eligible | Yes | No |
| Yield currency | Z token emissions | stZEC appreciation |

[[ZEC Staking]] is for holders who can accept illiquidity and want full self-custody preserved by Zcash's consensus. stZEC is for holders who want their ZEC to remain productive across the [[The Zcosystem|Zcosystem]].

---

## stZ

stZ is the liquid staking token for the Z token.

**How it works:**

1. Deposit Z tokens into the stZ staking contract
2. Receive stZ, which appreciates as consensus rewards accrue

The underlying Z tokens continue participating in [[Zcash Satoshi Plus (ZSP)|ZSP]] consensus. stZ lets holders maintain full DeFi composability while their Z tokens contribute to network security.

**What stZ unlocks:**

- Use as collateral on [[Z Lend]]
- Trade on [[Z Trade]]
- Shield in the [[ShieldedPool (MASP)|ShieldedPool]]
- Earn consensus rewards passively
