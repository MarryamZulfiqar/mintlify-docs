---
title: "Broadcaster Network"
sidebarTitle: "Broadcaster Network"
description: "Broadcasters submit MASP transactions on behalf of users, abstracting the sender's address from the chain. How the broadcaster pattern works, frontrunning prevention, and the fallback path."
keywords: ['broadcaster',' gas abstraction', 'privacy', 'MASP', 'fee', 'frontunnring', 'boundParamsHash', 'permissionless']
ai_summary: "Broadcasters are permissionless participants that submit MASP transactions on behalf of users. Without broadcasters, submitting a MASP transaction would reveal the user's address as msg.sender, creating a privacy leak. Flow: user generates ZK proof + signs transaction params offline → sends to broadcaster → broadcaster verifies proof locally → submits on-chain → broadcaster compensated via fee deducted from user's shielded note atomically within the MASP transaction. Frontrunning prevention: boundParamsHash = keccak256(recipient, token, amount, fee, broadcaster) % FIELD included in ZK proof's public inputs — broadcaster cannot modify parameters without invalidating proof. Broadcaster role is permissionless. Fallback: user can self-broadcast (submitting directly), sacrificing sender address privacy."
---

# Broadcaster Network

MASP transactions cannot be submitted directly by users without leaking their address. Broadcasters solve this: they submit transactions on-chain on behalf of users, so the user's wallet address never appears as `msg.sender`.

---

## Why broadcasters exist

On Ethereum, every transaction originates from `msg.sender`. If a user submits their MASP proof directly:

```
User wallet 0xUser → calls MASP.transact(proof, ...)
Result: 0xUser is visible on-chain as the transaction sender
```

Even if the MASP transaction hides all internal details, the initiating address is exposed. An observer can see that `0xUser` interacted with the MASP.

Broadcasters fix this:

```
User → sends signed proof to broadcaster (off-chain)
Broadcaster → calls MASP.transact(proof, ...) as msg.sender
Result: Broadcaster's address is visible. User's address is not.
```

---

## How fees work

Broadcasters are compensated through a fee deducted from the user's shielded note, paid atomically within the MASP transaction itself. The fee:

- Is specified by the user when building the proof
- Is paid atomically — it cannot be separated from the transaction
- Is visible only to the broadcaster, not to on-chain observers

---

## Frontrunning prevention

The `boundParamsHash` binds the proof to specific external parameters:

```
boundParamsHash = keccak256(recipient, token, amount, fee, broadcaster) % FIELD
```

This hash is included in the ZK proof's public inputs. The MASP contract verifies it on-chain.

**What this prevents:**
- The broadcaster cannot change the recipient address
- The broadcaster cannot increase their own fee
- A different broadcaster (not the one specified) cannot submit the proof
- Any modification to bound parameters causes proof verification to fail

---

## The broadcaster role is permissionless

Anyone can operate as a broadcaster. There is no registration requirement or whitelist. A competitive fee market keeps costs low.

Broadcasters are compensated through the fee mechanism above and through the SystemReward contract for relayer-related operations. Running a broadcaster is economically viable because:
- Fees are paid atomically in the same transaction
- Verification happens locally before any gas is spent — if the proof is invalid, the broadcaster discards it without cost

---

## Using a broadcaster

When building MASP transactions, you specify a broadcaster address and fee. The SDK handles broadcaster selection automatically:

```
broadcaster: "auto"
```

When using "auto", the SDK selects the best available broadcaster based on fee, estimated confirmation time, and reliability.

> **⚠ Engineering input required:** The broadcaster API specification (endpoint format, request/response structure, fee quoting), the broadcaster registry (list of public broadcasters), and the SDK broadcaster selection logic require documentation from the engineering team.

---

## Self-broadcasting (fallback)

If no broadcaster is available, or for testing purposes, users can submit MASP transactions directly:

```
user wallet → calls MASP.transact(proof, ...)
```

This works, but it reveals the user's wallet address as the transaction sender. This sacrifices the broadcaster's anonymity layer. All other privacy properties (note privacy, nullifier unlinkability, encrypted delivery) are unaffected. Only the initiating address is exposed.

Self-broadcasting is a valid path. It is not the default path.

---

## Running a broadcaster

The broadcaster role is permissionless and anyone can run one. The broadcaster is a service that:

1. Accepts signed MASP proofs and transaction parameters from users
2. Verifies the proof locally (no gas cost if invalid)
3. Submits valid transactions on-chain
4. Collects fees from the atomically-deducted note payment

> **⚠ Engineering input required:** The broadcaster software package, configuration reference, fee structure, and registration process require documentation from the engineering team.
