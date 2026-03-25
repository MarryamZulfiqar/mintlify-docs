---
title: "Dual Staking"
sidebarTitle: "Dual Staking"
keywords: ['Dual Staking', 'ZEC staking', 'Z token staking', 'boosted yield', 'ZSP', 'consensus rewards', 'solo staking', 'self-custodial']
ai_summary: "Dual Staking rewards simultaneous participation in ZSP Leg 2 (self-custodial ZEC, CLTV timelock on Zcash chain) and Leg 3 (Z tokens via ZSPStake contract). Two modes: Solo = staking only ZEC or only Z tokens, base rewards. Dual = both simultaneously, yield multiplier applied. Only self-custodial ZEC qualifies — stZEC does NOT. The flywheel: ZEC holders stake ZEC self-custodially → acquire Z tokens to unlock boost → more Z token staking increases network security → stronger network attracts more ZEC staking → more ZEC staking drives Z token demand."
---

# Dual Staking

Dual Staking multiplies staking yields for participants who contribute to both the ZEC and Z token legs of ZSP consensus. Staking ZEC alone earns base rewards. Staking Z tokens alongside ZEC earns boosted rewards.

---

## How it works

ZSP consensus has three legs: ZEC miners, ZEC holders, and Z token stakers. Dual Staking rewards participants who stake across both the ZEC and Z token legs simultaneously.

1. **Stake ZEC.** Timelock ZEC on the Zcash chain via ZSP Leg 2 (self-custodial staking).
2. **Stake Z tokens.** Delegate Z tokens to a validator through the ZSPStake contract.
3. **Earn boosted rewards.** The protocol detects dual participation and applies a yield multiplier to the participant's reward share.

Staking both assets together earns substantially more than the sum of staking each separately.

---

## Solo vs. Dual

| Mode | What it means | Reward level |
|------|--------------|-------------|
| **Solo** | Staking only ZEC or only Z tokens | Base rewards |
| **Dual** | Staking both ZEC and Z tokens simultaneously | Boosted rewards |

Solo stakers earn their proportional share of consensus rewards. Dual stakers earn a multiplied share because the protocol recognizes their contribution to both legs as providing greater security value.

**Important:** Only self-custodial ZEC staking (timelocked ZEC on the Zcash chain) qualifies for the Dual Staking boost. stZEC does not qualify — stZEC is bridged to Z and held in a staking contract, which is a different mechanism. Holders who want the Dual Staking boost must timelock ZEC directly on the Zcash chain.

---

## Dual Staking and the Z token

Dual Staking is one of the utility drivers for the Z token. It creates organic demand because ZEC holders who want to maximize their staking yield need Z tokens to qualify for the boosted rate.

The flywheel:

1. ZEC holders stake ZEC on Z (self-custodial timelock)
2. To earn boosted yields, they acquire and stake Z tokens
3. More Z token staking increases network security and governance participation
4. Stronger network attracts more ZEC staking
5. More ZEC staking increases demand for Z tokens to unlock the boost

Over time, Z token staking rewards accumulate for participants, building toward Dual Staking eligibility naturally without requiring an upfront Z token purchase.
