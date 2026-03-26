# Dual Staking

Dual Staking multiplies staking yields for participants who contribute to both the ZEC and Z token legs of [[Zcash Satoshi Plus (ZSP)|ZSP]] consensus. Staking ZEC alone earns base rewards. Staking Z tokens alongside ZEC earns boosted rewards.

---

## How it works

[[Zcash Satoshi Plus (ZSP)|ZSP]] consensus has three legs: ZEC miners, ZEC holders, and Z token stakers. Dual Staking rewards participants who stake across both the ZEC and Z token legs simultaneously.

1. **Stake ZEC.** Timelock ZEC on the Zcash chain.
2. **Stake Z tokens.** Delegate Z tokens to a validator through the ZSPStake contract.
3. **Earn boosted rewards.** The protocol detects dual participation and applies a yield multiplier to the participant's reward share.

Staking both assets together earns substantially more than the sum of staking each separately.

---

## Solo vs. Dual

| Mode | What it means | Reward level |
|---|---|---|
| **Solo** | Staking only ZEC or only Z tokens | Base rewards |
| **Dual** | Staking both ZEC and Z tokens simultaneously | Boosted rewards |

Solo stakers earn their proportional share of consensus rewards. Dual stakers earn a multiplied share because the protocol recognizes their contribution to both legs as providing greater security value and network alignment.

**Important:** Only ZEC timelocked directly by the user on the Zcash chain qualifies for the Dual Staking boost. stZEC does not qualify.

---

## Dual Staking and the Z token

Dual Staking is one of the utility drivers for [[The Z Token|the Z token]]. It creates organic demand because ZEC holders who want to maximize their staking yield need Z tokens to qualify for the boosted rate.

The flywheel:

1. ZEC holders stake ZEC on Z (self-custodial timelock)
2. To earn boosted yields, they acquire and stake Z tokens
3. More Z token staking increases network security and governance participation
4. Stronger network attracts more ZEC staking
5. More ZEC staking increases demand for Z tokens to unlock the boost

Over time, Z token staking rewards accumulate for participants, building toward Dual Staking eligibility naturally without requiring an upfront Z token purchase.
