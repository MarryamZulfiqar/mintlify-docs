---
title: "What is Z"
sidebarTitle: "What is Z"
keywords: ['Z Protocol', 'privacy platform', 'EVM', 'ZEC', 'Zcash', 'ShieldedPool', 'MASP', 'Zcosystem', 'ZSP', 'ZEC staking', 'USDZ', 'private DeFi', 'AI agents']
---

# What is Z

Z is the privacy platform for human and agentic on-chain action.

---

## Why Z exists

Two structural forces are converging.

**AI collapses the cost of on-chain mass surveillance.** Every transaction on a transparent blockchain is permanently recorded and queryable by anyone. AI has made mass analysis of that data cheap enough to run continuously, at scale, on any target. There is no longer a meaningful cost barrier to surveilling on-chain activity.

**AI agents need private financial rails.** Agents transact at orders-of-magnitude higher frequency than humans. Every transaction leaves a permanent record. A procurement agent's activity reveals its budget, vendors, and negotiation patterns. A trading agent's history reveals its strategy. Cross-domain leakage lets observers reconstruct an entire organization's operations by watching its agents.

Blockchain rails are the best substrate for the future of human and agentic finance. But only if they can be privacy-preserving. Z builds that infrastructure.

---

## Dual architecture

Z operates through two interconnected environments.

**The ShieldedPool (MASP)**

Z's home base. A private asset pool where balances and transfers are shielded by zero-knowledge proofs. Assets deposited into the ShieldedPool are stored as cryptographic commitments in a Merkle tree. Inside the pool, balances are fully private. No outside observer can see who holds what, how much, or that a specific user is present at all. Transfers within the pool are verified by Groth16 zero-knowledge proofs.

**The Zcosystem**

An EVM-based DeFi environment where assets briefly move to a fresh stealth address to interact with applications like trading or lending, then automatically reshield back into the pool. The DeFi interaction is visible on-chain, but the stealth address has no connection to the user's identity or pool balance.

---

## What you can do on Z

**Core applications:**

| Application | Function |
|-------------|----------|
| Z Trade | Swap assets privately |
| Z Lend | Borrow and lend assets privately |
| Z Stake | Stake ZEC and Z privately |
| Z Bridge | Bridge assets privately into the Z ecosystem |
| Z Perps | Trade perpetuals privately (post-launch) |
| Z Vault | Earn yield through private strategies (post-launch) |

**Core assets:**

| Asset | What it is |
|-------|-----------|
| USDZ | Private stablecoin, 1:1 backed by USDC/USDT |
| yUSDZ | Yield-bearing private dollars |
| stZEC | Liquid staked ZEC — productive, composable (post-launch) |
| stZ | Liquid staked Z tokens |

---

## Powered by Zcash, inspired by Core

Z leverages Zcash's hashrate for security through ZSP consensus, and uses Zcash's zero-knowledge proof architecture as the foundation for the ShieldedPool. Zcash provides the cryptographic foundation; Z provides the applications.

Z's consensus mechanism adapts Core DAO's Satoshi Plus model, which has secured Core using delegated BTC since January 2023 with no outages.

Z is not an L2, a fork of Zcash, or a fork of Core. It is an independent protocol with its own consensus, validator set, and token.

---

## Gas

Z lets users pay gas in ZEC or the Z token. ZEC holders can access all core applications without touching the Z token. When ZEC is used as gas, it is swapped into Z at the protocol level.

---

## Designed for agents

Agents generate more transactions, more metadata, and more attack surface than any human user. Z provides the scaffolding that turns a capable model into a productive on-chain agent: EVM compatibility so existing tools carry over, protocol-level wallet hygiene so agents don't accumulate linkable transaction histories, vertically integrated DeFi so agents don't discover third-party protocols, agent-native tooling for reliable operation, and protocol-enforced permissions so agents can be constrained without being useless.

For the full agent infrastructure, see [Z for AI Agents](/docs/for-agents/z-for-ai-agents).

---

## How this documentation is organized

| Section | Covers |
|---------|--------|
| [Core Concepts](/docs/core-concepts/privacy-architecture) | Privacy architecture, ZSP consensus, the Zcosystem, the Z token |
| [Products](/docs/products/shieldedpool) | ShieldedPool, Z Trade, Z Lend, Z Stake, USDZ, yUSDZ, Z Bridge |
| [Staking](/docs/staking/zec-staking-overview) | ZEC staking paths, self-custodial staking, liquid staking, Dual Staking |
| [For Agents](/docs/for-agents/z-for-ai-agents) | Agent infrastructure, wallet hygiene, permissions, tooling, simulation |
| [Developers](/docs/for-developers/quick-start) | Quick start, EVM compatibility, devnet, MASP integration, RelayAdapter |
| [Compliance](/docs/compliance/privacy-and-compliance) | How privacy and regulation coexist |
| [Reference](/docs/reference/roadmap) | Roadmap, security properties, glossary, FAQ |
