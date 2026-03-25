---
title: "RelayAdapter Pattern"
sidebarTitle: "RelayAdapter Pattern"
description: "The RelayAdapter is the canonical contract pattern for private DeFi on Z. Atomic: unshield from MASP → execute DeFi action → reshield output. One transaction. Unlinkable."
keywords: ['RelayAdapter', 'private DeFi', 'atomic', 'unshield', 'reshield', 'MASP', 'adaptParams', 'broadcaster', 'ZApp', 'private swap', 'private lending']
ai_summary: "RelayAdapter enables atomic private DeFi execution. Flow: user generates ZK proof with adaptParams binding = keccak256(nullifiers, commitments, actionData) in proof's public inputs → submits to broadcaster → broadcaster calls RelayAdapter → adapter atomically (1) calls MASP to spend input notes, (2) executes DeFi action (swap/lend/borrow), (3) shields output tokens back into MASP. Everything in one transaction. adaptParams binding prevents broadcaster from modifying swap parameters, recipients, or fees — if any bound parameter changes, proof is invalid, transaction reverts. Z ships RelayAdapters for Z Trade, Z Lend, USDZ. External teams can implement IRelayAdapter for their own ZApps."
---

# RelayAdapter Pattern

The RelayAdapter is the contract pattern that makes private DeFi possible on Z. Any DeFi action — a swap, a loan, a borrow — can be wrapped in a RelayAdapter to make it atomic with MASP entry and exit: funds unshield, the action executes, and the output reshields, all in one transaction.

---

## The problem RelayAdapter solves

Without RelayAdapter, a private DeFi interaction would require multiple separate transactions:

```
Transaction 1: Unshield from MASP to address 0xStealth
Transaction 2: Approve DeFi protocol to spend 0xStealth's tokens
Transaction 3: Execute swap from 0xStealth  
Transaction 4: Shield output from 0xStealth back into MASP
```

Four transactions. The stealth address accumulates a transaction history. An observer can link all four and infer the source and destination.

**With RelayAdapter:** One transaction. The RelayAdapter is the actor on-chain, not any user-controlled address. Observers see a transaction at the RelayAdapter. Nothing else.

---

## How it works

### The adaptParams binding

The critical security property. When a user builds their ZK proof, they commit to the specific DeFi action that will execute:

```solidity
bytes32 adaptParams = keccak256(abi.encodePacked(
    nullifiers,    // Which MASP notes are being spent
    commitments,   // Which new MASP notes will be created
    actionData     // The specific DeFi action (swap params, lending params)
));
```

This hash is included in the ZK proof's public inputs. If the broadcaster or any intermediate party tries to:
- Change the swap parameters
- Redirect the output to a different address
- Substitute a different DeFi action
- Extract a higher fee

...the `adaptParams` hash will no longer match what's in the proof, and the transaction will revert. The proof cryptographically commits to the exact action.

### Atomic execution flow

```
User off-chain:
  1. Builds ZK proof (includes adaptParams)
  2. Sends signed proof to broadcaster

Broadcaster → RelayAdapter (one atomic transaction):
  3. RelayAdapter calls MASP — spends input notes, output commitments registered
  4. MASP releases tokens to RelayAdapter (briefly, not to a user address)
  5. RelayAdapter executes DeFi action
  6. DeFi protocol returns output tokens to RelayAdapter
  7. RelayAdapter shields output tokens back into MASP
  8. Output note commitments inserted into Merkle tree

Result: input notes spent → DeFi action executed → output notes created
```

If any step fails (slippage exceeded, insufficient collateral, etc.), the entire transaction reverts. No partial execution. No stuck tokens.

---

## Implementing a RelayAdapter

The IRelayAdapter interface:

```solidity
interface IRelayAdapter {
    function executePrivateAction(
        bytes calldata proof,
        bytes32[] calldata nullifiers,
        bytes32[] calldata commitments,
        bytes calldata encryptedNotes,
        bytes calldata actionData      // ABI-encoded action-specific parameters
    ) external;
}
```

> **⚠ Engineering input required:** The IMASP interface, the exact method signatures for MASP calls within a RelayAdapter, and working Solidity examples require the actual contract ABI from the engineering team. The pattern description above is sourced from the v1 documentation. Implementation requires team review.

---

## Z's built-in RelayAdapters

Z ships RelayAdapters for all enshrined ZApps. You do not need to implement your own unless you are integrating a custom protocol.

| Adapter | Actions |
|---------|---------|
| RelayAdapter for Z Trade | Private swap (all Z Trade pairs) |
| RelayAdapter for Z Lend | Private deposit, borrow, repay, withdraw |
| RelayAdapter for USDZ | Private USDZ mint and redemption |

> **⚠ Engineering input required:** Deployed addresses for these RelayAdapters require confirmation from the engineering team.

---

## Security considerations

**Never approve tokens directly to a RelayAdapter for user funds.** RelayAdapters hold tokens only transiently during the atomic transaction. They should not have standing approvals from user wallets.

**Verify the RelayAdapter address before including it in a proof.** A malicious RelayAdapter could execute arbitrary actions during step 5. The adaptParams binding ensures it executes only the action committed in the proof — but only if you're calling a legitimate RelayAdapter.

**Always include slippage protection.** If a swap reverts due to slippage, the entire transaction reverts and the MASP notes remain unspent. This is correct behavior — but it means the user must retry with adjusted parameters.
