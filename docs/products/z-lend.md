---
title: Z Lend
description: Z's enshrined lending and borrowing protocol. Deposit collateral privately and borrow USDZ. Maintains ZEC price exposure while accessing private stablecoins.
keywords: [Z Lend, lending, borrowing, private lending, USDZ, stZEC, collateral, Aave, RelayAdapter, liquidation]
ai_summary: Z Lend is an Aave-model lending protocol enshrined on Z with MASP integration via RelayAdapter. Supported collateral: ZEC, stZEC, Z, stZ, USDZ, yUSDZ. Borrow USDZ against collateral. Privacy via RelayAdapter (same atomic unshield+execute+reshield as Z Trade). Revenue model: Aave-style reserve factor — majority of borrow interest to depositors, protocol captures spread. Primary use case: deposit stZEC as collateral, borrow USDZ, maintain ZEC price exposure + staking yield while accessing private stablecoins. Liquidation follows standard health factor mechanics.
---

# Z Lend

Z Lend is Z's enshrined lending and borrowing protocol. Deposit collateral and borrow against it while keeping positions private. The primary use case is maintaining ZEC price exposure while accessing private stablecoins.

---

## Supported assets

| Asset | Use as collateral | Borrow |
|-------|-----------------|--------|
| ZEC (via bridge) | Yes | No |
| stZEC | Yes | No |
| Z | Yes | No |
| stZ | Yes | No |
| USDZ | Yes | Yes |
| yUSDZ | Yes | No |

---

## How it works

Z Lend uses the same RelayAdapter pattern as Z Trade. Deposit and borrow operations can be executed from the MASP through stealth addresses, keeping the borrower's identity unlinkable. The entire sequence — unshield, deposit or borrow, reshield — executes in one atomic transaction.

---

## Revenue model

Z Lend generates protocol revenue through a reserve factor on borrow interest, following the Aave model. Depositors earn the majority of interest paid by borrowers. The protocol captures a percentage of the spread.

---

## Health factor and liquidation

Z Lend follows standard Aave-style health factor mechanics. If your collateral value drops relative to your borrowed amount, a liquidator can repay part of your debt in exchange for your collateral at a discount.

**Risk:** If you deposit stZEC as collateral and ZEC price drops significantly, your health factor may fall below 1.0 and your position may be partially liquidated. Monitor your health factor and add collateral or repay as needed.

---

## Primary use case: stZEC composition

The most common Z Lend flow:

1. Bridge ZEC to Z and deposit into stZEC (earn staking yield)
2. Deposit stZEC as collateral on Z Lend
3. Borrow USDZ against the stZEC position
4. Your stZEC continues earning staking yield while serving as collateral
5. You hold USDZ for other uses (deposit into yUSDZ, trade, transfer privately)
6. All positions can be shielded in the MASP

This lets you maintain ZEC price exposure, earn staking yield on the ZEC, and access stablecoins without selling.
