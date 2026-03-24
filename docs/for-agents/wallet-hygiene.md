---
title: Protocol-Level Wallet Hygiene
description: How Z's MASP and stealth addresses eliminate metadata accumulation for AI agents. Agents enter the pool as N addresses and exit to M different stealth addresses automatically — no management required.
keywords: [wallet hygiene, stealth addresses, MASP, AI agents, metadata, privacy, ERC-5564, ECDH, unlinkable]
ai_summary: On transparent blockchains, each agent interaction ties to one wallet, accumulating a complete behavioral fingerprint. Z eliminates this through protocol-level hygiene. Agents hold assets in the MASP. When an agent needs to interact with DeFi, funds exit to a fresh stealth address generated via ECDH (ERC-5564). The interaction executes from the stealth address. Assets reshield. Each interaction uses a different stealth address. An agent may enter the MASP through addresses A, B, C and exit to addresses X, Y, Z — no observer can link these. The agent does not manage stealth address generation — the protocol handles it automatically. Two-tier execution: on-chain via stealth address (unlinkable), in-pool (fully shielded, future).
---

# Protocol-Level Wallet Hygiene

On a transparent blockchain, each agent transaction creates a data point. Thousands of transactions create a complete behavioral fingerprint. An adversary who observes an agent's on-chain activity for 30 days knows its strategy, counterparties, treasury size, and rebalancing triggers.

Z eliminates this problem at the protocol level.

---

## The problem on transparent chains

A standard EVM agent uses one wallet. That wallet accumulates a complete transaction history tied to one identity. At high transaction frequency, this becomes worse:

```
Hour 1:  Agent makes 50 swaps
Hour 2:  Agent makes 47 swaps  
Hour 3:  Agent makes 53 swaps
Pattern: Activity timing, frequency, and rhythm are now fingerprinted
```

A procurement agent's transactions reveal budget, vendors, and contract terms. A trading agent's history reveals its strategy. Cross-domain leakage lets observers reconstruct entire operational profiles by watching agent activity over time.

There is no way to avoid this on a transparent chain. Multiple wallets help but add coordination complexity and don't eliminate the pattern.

---

## How Z solves it

Z agents hold assets in the MASP. The MASP is the agent's home base — private, shielded, no outside visibility.

When the agent needs to execute a DeFi action:

**Step 1 — Assets in the MASP.**
```
MASP balance: 100 USDZ (private, invisible to outside observers)
```

**Step 2 — Exit to fresh stealth address.**
The protocol generates a one-time stealth address via ECDH (ERC-5564). This address has zero on-chain connection to the agent's MASP balance or any previous address.

**Step 3 — Interact from stealth address.**
The swap, loan, or other DeFi action executes from the stealth address. It is visible on-chain, but the stealth address is a dead end — no observer can trace it backward to the MASP.

**Step 4 — Reshield.**
Output tokens enter the MASP as new private notes. The stealth address has no further activity and is never reused.

---

## What an observer sees

**Agent's actual activity:**
```
Interaction 1: Swap 50 USDZ for ZEC (via stealth address A)
Interaction 2: Deposit ZEC as collateral (via stealth address B)
Interaction 3: Borrow USDZ (via stealth address C)
Interaction 4: Swap USDZ for stZEC (via stealth address D)
```

**What an outside observer sees:**
```
Some address A made a swap at Z Trade
Some address B deposited at Z Lend           ← no connection to A
Some address C borrowed at Z Lend            ← no connection to B
Some address D made a swap at Z Trade        ← no connection to A, B, or C
```

Each interaction appears to be a different anonymous user. The agent's full activity, strategy, counterparties, and treasury movements are invisible.

---

## The agent does not manage this

Stealth address generation, MASP entry and exit, and reshielding are handled by the protocol and application layer. The agent calls the same interfaces it would use on any EVM chain:

```
ZTrade.swap(tokenIn: USDZ, tokenOut: ZEC, amount: 50)
```

Under the hood, the protocol generates a stealth address, routes the exit through it, executes the swap, and reshields the output. The agent never sees or manages any of this.

---

## Two-tier execution environment

Z provides two privacy levels for agent execution:

**Tier 1 — Stealth address DeFi (current):** Activity is on-chain and visible (amounts, contract calls) but not linkable to identity or prior activity. Use for: trades, loans, borrows, payments.

**Tier 2 — In-pool operations (future):** Activity is fully shielded — invisible to outside observers. No public footprint at all. Use for: note-level transfers between agents, in-pool swaps.

The architecture progresses from Tier 1 to Tier 2 over time.

---

## Impact

**Before Z:** After 30 days, an observer has a complete behavioral profile. Competitors can front-run strategies. Service providers can price-discriminate based on visible treasury size. Adversaries can target the principal by mapping the agent's business relationships.

**With Z:** After 30 days, no persistent identity, no linkable history, no observable pattern. Strategy remains confidential. Treasury size is invisible. Business relationships are private.

Same throughput. Same DeFi access. Zero metadata accumulation.
