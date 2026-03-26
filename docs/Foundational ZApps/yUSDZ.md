---
title: "yUSDZ"
sidebarTitle: "yUSDZ"
ai_summary: "yUSDZ is a non-rebasing ERC-4626 yield-bearing vault for USDZ — using a share price appreciation model for ShieldedPool compatibility, enabling the first yield-bearing private dollar where users earn yield while fully shielded."
keywords: ["yUSDZ", "USDZ", "yield", "ERC-4626", "ShieldedPool", "non-rebasing", "stablecoin", "private yield", "share price", "DeFi composition"]
---

yUSDZ is the yield-bearing version of [[USDZ]]. Deposit USDZ and earn yield automatically. Your yUSDZ balance stays constant. The value per token grows.

---

## How the Exchange Rate Works

yUSDZ is an ERC-4626 vault. The exchange rate between yUSDZ and USDZ increases over time as yield accrues.

**Example:**

- Deposit 1,000 USDZ when the exchange rate is 1.00. Receive 1,000 yUSDZ.
- After one year at a hypothetical 3.7% yield, the exchange rate is ~1.037.
- Your 1,000 yUSDZ is now redeemable for ~1,037 USDZ.

Your yUSDZ balance stayed constant at 1,000. The value grew through the exchange rate.

---

## Why Non-Rebasing

Standard rebasing tokens (where your balance changes) are incompatible with the [[ShieldedPool (MASP)|ShieldedPool]]. ZK proof commitments lock in a specific balance at creation time:

```
commitment = Poseidon(Poseidon(npk, tokenHash, value), random, 0)
```

The `value` field is fixed when the commitment is created. If a rebasing token increases your balance, the commitment no longer matches your actual balance. The note becomes unspendable. You cannot generate a valid ZK proof for a balance value that doesn't match the commitment.

The share price model avoids this entirely. Your yUSDZ balance inside the commitment stays fixed. Yield accrues through the exchange rate, not through balance changes.

This makes yUSDZ the first yield-bearing private dollar: you can shield yUSDZ into the [[ShieldedPool (MASP)|ShieldedPool]] and earn yield while remaining fully private.

---

## Yield Sources

Yield sources will be detailed closer to launch.

---

## Privacy

yUSDZ inside the [[ShieldedPool (MASP)|ShieldedPool]] behaves like any other ShieldedPool note. When you shield yUSDZ, the exchange rate continues to appreciate. Your position earns yield without any public footprint. No outside observer can see your balance, position size, or yield earned.

---

## yUSDZ in the DeFi Composition Stack

yUSDZ sits at the top of the yield stack:

1. Bridge ZEC, deposit into stZEC (staking yield)
2. Deposit stZEC as collateral on [[Z Lend]]
3. Borrow [[USDZ]] against it
4. Spend or deploy USDZ as needed
5. Deposit idle USDZ into yUSDZ to offset borrow costs while you hold
6. Shield everything in the [[ShieldedPool (MASP)|ShieldedPool]]

The result: ZEC price exposure, staking yield, borrowed stablecoins, yield on idle capital, and privacy over the full position.
