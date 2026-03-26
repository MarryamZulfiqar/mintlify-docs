---
title: "Z Lend"
sidebarTitle: "Z Lend"
ai_summary: "Z's privacy-preserving enshrined lending and borrowing protocol — atomic unshield-deposit-reshield via the RelayAdapter pattern, Aave-style health factor and liquidation mechanics, and a reserve factor revenue model."
keywords: ["Z Lend", "lending", "borrowing", "collateral", "ShieldedPool", "RelayAdapter", "health factor", "liquidation", "USDZ", "stZEC", "Aave"]
---

Z Lend is Z's enshrined lending and borrowing protocol. Deposit collateral and borrow against it while maintaining anonymity.

---

## How It Works

Z Lend uses the same RelayAdapter pattern as [[Z Trade]]. Deposit and borrow operations can be executed from the [[ShieldedPool (MASP)|ShieldedPool]] through stealth addresses, keeping the borrower's identity unlinkable. The entire sequence (unshield, deposit or borrow, reshield) executes in one atomic transaction.

---

## Revenue Model

Z Lend generates protocol revenue through a reserve factor on borrow interest, following the Aave model. Depositors earn the majority of interest paid by borrowers. The protocol captures a percentage of the spread.

---

## Health Factor and Liquidation

Z Lend follows standard Aave-style health factor mechanics. If your collateral value drops relative to your borrowed amount, a liquidator can repay part of your debt in exchange for your collateral at a discount.

**Risk:** If your collateral value drops significantly, your health factor may fall below 1.0 and your position may be partially liquidated. Monitor your health factor and add collateral or repay as needed.

---

## Sample Use Case

A possible Z Lend flow:

1. Deposit collateral on Z Lend
2. Borrow [[USDZ]] against it
3. Use USDZ for other purposes (deposit into [[yUSDZ]], trade on [[Z Trade]], transfer privately)
4. All positions can be shielded in the [[ShieldedPool (MASP)|ShieldedPool]]

This lets you maintain price exposure on your collateral while accessing stablecoins without selling.
