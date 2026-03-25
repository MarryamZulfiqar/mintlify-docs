---
title: "Z Trade"
description: "Z's enshrined DEX with concentrated liquidity. Private swaps execute atomically through the MASP via the RelayAdapter pattern — unshield, execute, reshield in one transaction."
keywords: ['Z Trade', 'DEX', 'private swap', 'concentrated liquidity', 'RelayAdapter', 'MASP', 'stealth address', 'Uniswap v3', 'USDZ', 'ZEC']
ai_summary: "Z Trade is Z's enshrined DEX built on Uniswap v3 concentrated liquidity. Private trading uses the RelayAdapter pattern: user generates ZK proof with adaptParams binding = keccak256(nullifiers, commitments, actionData), submits to broadcaster, broadcaster calls RelayAdapter which atomically (1) unshields input tokens from MASP, (2) executes swap on AMM, (3) reshields output tokens back into MASP. All in one transaction. adaptParams binding prevents broadcaster from modifying swap parameters. Primary pairs: ZEC/USDZ, Z/USDZ, stZEC/USDZ, stZ/USDZ. 50% protocol fee on USDZ pairs, 50% to LPs."
---

# Z Trade

Z Trade is Z's enshrined DEX. Swap assets privately using the MASP and RelayAdapter pattern. A private swap is one transaction: unshield, execute, reshield — atomic and unlinkable.

---

## How private trading works

Standard DeFi swaps reveal the trader's address and amounts on-chain. Z Trade uses the RelayAdapter pattern to make swaps unlinkable.

**Step 1 — Assets in the MASP.**
Your tokens sit as private notes. Nobody outside can see your balance or that you're present at all.

**Step 2 — Generate a ZK proof.**
Your wallet generates a Groth16 proof authorizing the swap. The proof commits to the specific input notes being spent, the expected output, and an `adaptParams` binding:

```
adaptParams = keccak256(nullifiers, commitments, actionData)
```

This binding is included in the proof's public inputs. It locks the broadcaster to the exact parameters you specified.

**Step 3 — Submit to broadcaster.**
You send the signed proof to a broadcaster, not directly to the blockchain. The broadcaster submits the transaction on-chain, so your address never appears as `msg.sender`.

**Step 4 — RelayAdapter executes atomically.**
In one atomic transaction, the RelayAdapter:
- Unshields input tokens from the MASP to the adapter contract
- Executes the swap on Z Trade's AMM (approve → swap → collect output)
- Reshields output tokens back into the MASP as new private notes

**Step 5 — Result.**
An observer sees a transaction at the RelayAdapter contract. They cannot see who initiated it, link it to any MASP balance, or connect it to other activity. Your output tokens land in the MASP as fresh private notes.

The `adaptParams` binding prevents the broadcaster from changing the recipient, altering the fee, or substituting a different trade. If any bound parameter changes, the ZK proof is invalid and the transaction reverts.

---

## Trading characteristics

- Standard AMM with concentrated liquidity (Uniswap v3 mechanics)
- Primary pairs: ZEC/USDZ, Z/USDZ, stZEC/USDZ, stZ/USDZ
- 50% protocol fee on USDZ trading pairs (50% to liquidity providers)

---

## Liquidity provision

Add liquidity to Z Trade pools to earn trading fees. Liquidity provision uses standard Uniswap v3 position mechanics.

Private liquidity provision (adding and removing liquidity from the MASP without revealing your identity) is a post-launch feature.

---

## For developers

The RelayAdapter pattern that powers Z Trade's private execution is the same pattern available to any ZApp developer who wants to integrate with the MASP. For the implementation specification, see [RelayAdapter Pattern](/docs/for-developers/relayAdapter-pattern).
