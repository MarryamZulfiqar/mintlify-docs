---
title: "yUSDZ"
keywords: ['yUSDZ', 'yield', 'ERC-4626', 'private stablecoin', 'USDZ', 'non-rebasing', 'MASP', 'wstETH']
ai_summary: "yUSDZ is an ERC-4626 vault. Deposit USDZ → receive yUSDZ shares → exchange rate appreciates as yield accrues. Non-rebasing share price model (like wstETH): token balance stays constant, value per token grows. Non-rebasing is required for MASP compatibility — rebasing tokens change balances, breaking Poseidon commitment values stored at creation time. Yield sources TBD — do not state specific sources or percentages. yUSDZ inside MASP: first yield-bearing private dollar, shielded notes with fixed balance but appreciating exchange rate. Protocol takes performance fee on yield, remainder to depositors."
---

# yUSDZ

yUSDZ is the yield-bearing version of USDZ. Deposit USDZ and earn yield automatically. Your yUSDZ balance stays constant. The value per token grows.

---

## How the exchange rate works

yUSDZ is an ERC-4626 vault. The exchange rate between yUSDZ and USDZ increases over time as yield accrues.

**Example:**
- Deposit 1,000 USDZ when the exchange rate is 1.00. Receive 1,000 yUSDZ.
- After one year at a hypothetical 3.7% yield, the exchange rate is ~1.037.
- Your 1,000 yUSDZ is now redeemable for ~1,037 USDZ.

Your yUSDZ balance stayed constant at 1,000. The value grew through the exchange rate.

---

## Why non-rebasing

Standard rebasing tokens (where your balance changes) are incompatible with the MASP. ZK proof commitments lock in a specific balance at creation time:

```
commitment = Poseidon(Poseidon(npk, tokenHash, value), random, 0)
```

The `value` field is fixed when the commitment is created. If a rebasing token increases your balance, the commitment no longer matches your actual balance. The note becomes unspendable — you cannot generate a valid ZK proof for a balance value that doesn't match the commitment.

The share price model avoids this entirely. Your yUSDZ balance inside the commitment stays fixed. Yield accrues through the exchange rate, not through balance changes.

This makes yUSDZ the first yield-bearing private dollar: you can shield yUSDZ into the MASP and earn yield while remaining fully private.

---

## Yield sources

Yield sourcing is not yet finalized. Potential sources include Z Lend borrow interest, returns from deployed USDC/USDT backing, and trading fee revenue. The protocol takes a performance fee on yield generated for yUSDZ holders, with the remainder accruing through the rising exchange rate.

---

## Privacy

yUSDZ inside the MASP behaves like any other MASP note. When you shield yUSDZ, the exchange rate continues to appreciate — your position earns yield without any public footprint. No outside observer can see your balance, position size, or yield earned.

---

## yUSDZ in the DeFi composition stack

yUSDZ sits at the top of the yield stack:

1. Bridge ZEC → deposit into stZEC (staking yield)
2. Deposit stZEC as collateral on Z Lend
3. Borrow USDZ against it
4. Deposit borrowed USDZ into yUSDZ (yield on stables)
5. Shield everything in the MASP

The result: ZEC price exposure, staking yield, borrowed stablecoins, yield on borrowed stablecoins, and privacy over the full position.
