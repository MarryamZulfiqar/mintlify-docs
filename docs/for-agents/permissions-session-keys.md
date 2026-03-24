---
title: Permissions & Session Keys
description: Z's agent wallets (Shells) are ERC-4337 smart contract wallets with a built-in policy engine. Owner-controlled spending caps, allowlists, session keys, and a consensus-enforced kill switch.
keywords: [agent permissions, session keys, ERC-4337, Shells, spending caps, allowlists, kill switch, hierarchical wallets, ZK membership proof]
ai_summary: Z agent wallets are called Shells — ERC-4337 smart contract wallets with policy engine. Owner configures at deployment: spending caps (per-tx, daily, cumulative), contract allowlists (address + function selector), session duration (time-bounded keys), kill switch (consensus-enforced — validators reject txs from frozen Shells at mempool level). Session keys: ECDSA or passkey, authorized within policy bounds, master key never touches agent runtime, rotatable without redeploying Shell. Hierarchical Shells: parent deploys child Shells with strictly narrower permissions. Permissions cascade downward only. Revocation cascades to all children. Hierarchy membership committed as Merkle root on-chain, membership proved via ZK proof without revealing tree structure — adversary cannot map fleet topology.
---

# Permissions & Session Keys

Agents need constrained autonomy. Full access to a private key is dangerous — one prompt injection and the wallet drains. Manual approval for every transaction defeats the purpose of autonomy. The right model is a sandbox: the human sets the boundaries, the agent operates freely within them.

Z's agent wallets — called Shells — are ERC-4337 smart contract wallets with a built-in policy engine.

---

## What Shells provide

**Spending caps.** Configure per-transaction, daily, and cumulative limits. An agent cannot spend more than the owner specifies across any time window.

**Contract allowlists.** Specify whitelisted addresses and function selectors. The agent can only interact with approved contracts through approved functions. A trading agent allowlisted to a single DEX pair cannot spontaneously decide to interact with a lending protocol.

**Session duration.** Time-bounded session keys that expire automatically. Long-running agents rotate keys on a configurable schedule. An expired key cannot be used.

**Kill switch.** The owner can freeze the wallet at any time. Freeze is consensus-enforced: validators reject transactions from a frozen Shell at the mempool level. The agent cannot circumvent it. Freezing does not require the agent's cooperation.

---

## How session keys work

The owner holds a master key with unconditional authority: freeze, revoke session keys, reconfigure policies, or withdraw all funds at any time.

The agent holds a session key (ECDSA or passkey-based) that authorizes transactions within policy bounds. The owner's master key never touches the agent runtime.

Session keys can be rotated or revoked without redeploying the Shell. Revoking a session key immediately terminates the agent's ability to transact. The Shell address, state, and funds are unaffected.

---

## Hierarchical permissions

Shells support nesting. A parent wallet can deploy child wallets with inherited but strictly narrower permissions.

**Example:**
A portfolio management Shell (daily cap of $100K, DeFi protocols allowlisted) spawns three executor Shells:
- A trading Shell ($30K cap, single DEX only)
- A lending Shell ($50K cap, lending protocol only)
- A monitoring Shell (read-only, no spending authority)

**Rules:**
- Permissions cascade downward only. A child Shell can never exceed its parent's bounds.
- Revoking a parent cascades to all its children.
- The owner sees all activity through view keys.
- If the trading Shell is compromised, the owner revokes it without touching the others.

---

## On-chain hierarchy without revealing structure

Hierarchy membership is committed as a Merkle root stored on-chain. Agents prove group membership through a ZK proof without revealing the full tree structure.

An adversary observing the chain cannot determine:
- How many agents a principal controls
- How the fleet is organized
- Which Shells are children of which parents

The proof confirms: "this Shell is a member of the hierarchy." It reveals nothing else.

---

## EIP-7702 compatibility

Existing EOAs can delegate to Shell contract logic via EIP-7702, enabling legacy wallets to gain Shell policy enforcement without migrating assets to a new address. For persistent agent deployments, the full ERC-4337 Shell is recommended. EIP-7702 lowers the barrier for users testing agent workflows with existing wallets.
