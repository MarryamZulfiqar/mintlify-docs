---
title: "01 Overview"
---

```markdown
---
title: "Z Protocol — Overview"
slug: overview
description: "Z is the privacy platform for human and agentic on-chain action. Powered by Zcash, Z delivers a vertically integrated stack of privacy-focused applications for human and agentic use."
keywords: [Z Protocol, privacy, AI agents, EVM, ZEC, Zcash, ShieldedPool, MASP, Zcosystem, ZSP, stablecoin, stZEC, USDZ, DeFi, on-chain privacy]
ai_summary: "Z is an EVM-compatible privacy protocol powered by Zcash. Two structural forces drive it: (1) AI reduces the cost of on-chain mass surveillance to near zero; (2) AI agents need private financial rails. Z delivers both a privacy layer and a full DeFi stack through two architectural components: the ShieldedPool (a UTXO-based MASP where balances are shielded by Groth16 ZK proofs) and the Zcosystem (an EVM-based DeFi layer where assets exit to fresh one-time stealth addresses per interaction, then reshield). Consensus is Zcash Satoshi Plus (ZSP): ZEC miners, ZEC holders, and Z token stakers elect validators. Gas is payable in ZEC or Z token. Core products at launch: Z Trade (DEX), Z Lend (lending/borrowing), USDZ (1:1 USDC/USDT stablecoin), yUSDZ (yield-bearing USDZ), stZ (liquid staked Z tokens), LayerZero bridge. Post-launch: stZEC, ZEC SPV bridge, and deeper privacy primitives."
---

# Z Protocol

Z is the privacy platform for human and agentic on-chain action.

---

## One Minute Overview

### Two trends are converging

**1. AI collapses the cost of on-chain surveillance.**

Every transaction on a transparent blockchain is permanently recorded and queryable by anyone. AI has made mass analysis of that data cheap enough to run continuously, at scale, on any target. An agent executing thousands of transactions per day leaves a complete behavioral profile visible to any observer with an RPC endpoint.

**2. AI agents need private financial rails.**

Agents transacting at high frequency generate metadata that reveals strategy, counterparties, treasury size, and business relationships. Without private rails, an agent cannot maintain competitive advantage or operational security.

Blockchain rails are the best substrate for the future of both human and agentic finance. But only if they can be privacy-preserving. Z builds that infrastructure.

---

### What Z brings together

| Layer | What it does |
|-------|-------------|
| **Privacy** | The ShieldedPool encrypts balances and private transfers. Fresh stealth addresses preserve unlinkability when interacting with DeFi. |
| **Full DeFi stack** | Private stablecoins, trading, lending, and yield designed to work together in the Zcosystem. |
| **Agent infrastructure** | MCP servers, SKILL.md files, simulation sandboxes, and protocol-level session keys guide agents through Z's applications with full privacy at scale. |
| **Zcash-powered security** | Secured by Zcash mining delegation, staked ZEC, and staked Z tokens through ZSP consensus. |
| **EVM compatibility** | Solidity works. Existing agent skills carry over. Composable with the broader EVM ecosystem. |

---

### Dual architecture

Z operates through two interconnected environments.

**ShieldedPool (MASP)**

The home base. A private asset pool where balances and transfers are shielded by zero-knowledge proofs. Assets deposited into the ShieldedPool are stored as cryptographic commitments in a Merkle tree. Inside the pool, balances are fully private — no outside observer can see who holds what, how much, or that a specific user is present at all.

Transfers within the pool are verified by Groth16 zero-knowledge proofs, which confirm that every transaction follows the rules without revealing any underlying data.

**Zcosystem**

An EVM-based DeFi environment where assets briefly move to a fresh stealth address to interact with applications like trading or lending, then automatically reshield back into the pool. The DeFi interaction is visible on-chain, but the stealth address has no connection to the user's identity or pool balance.

---

### What you can do on Z

The Zcosystem is a complete DeFi stack of privacy-focused applications designed to work together.

**Core applications**

| Application | Function |
|-------------|----------|
| **Z Trade** | Swap assets privately |
| **Z Lend** | Borrow and lend assets privately |
| **Z Stake** | Stake ZEC and Z privately |
| **Z Bridge** | Bridge assets privately into the Z ecosystem |
| **Z Perps** | Trade perpetuals privately (post-launch) |
| **Z Vault** | Earn yield through private strategies (post-launch) |

**Core assets**

| Asset | What it is |
|-------|-----------|
| **USDZ** | Private stablecoin, 1:1 backed by USDC/USDT |
| **stZEC** | Liquid staked ZEC — earn yield while maintaining liquidity (post-launch) |
| **yUSDZ** | Yield-bearing private dollars |
| **stZ** | Liquid staked Z tokens |

All revenue from all applications is distributed as buybacks, staker yield, and an insurance fund. The protocol captures value at the application layer, not just gas fees.

---

## How Privacy Works

Z preserves privacy through two mechanisms: the ShieldedPool for private balances and transfers, and fresh stealth addresses that extend that privacy into DeFi.

When a user exits the pool to trade, lend, or borrow, they exit to a one-time, unlinkable address. After the interaction, assets reshield back into the pool.

**The Multi-Asset Shielded Pool (MASP)** is home base. Assets deposited into the pool are stored as cryptographic commitments in a Merkle tree. Inside the pool, balances are fully private. Transfers within the pool are verified by Groth16 zero-knowledge proofs, which confirm every transaction follows the rules without revealing any underlying data.

**Stealth addresses** handle DeFi interactions. When an agent or user needs to trade, lend, or borrow, they exit the pool to a fresh, one-time address generated through ECDH key exchange. The DeFi interaction is visible on-chain, but nobody can connect the stealth address back to the participant. After the interaction, assets reshield.

---

## Consensus: Zcash Satoshi Plus

Z is secured by Zcash Satoshi Plus (ZSP), where Zcash miners, ZEC holders, and Z token holders elect validators that produce blocks.

**Three groups participate in validator election:**

1. **ZEC miners** include delegation metadata in their mined Zcash blocks to vote for Z validators.
2. **ZEC holders** timelock ZEC on the Zcash blockchain to earn the right to vote. They vote for Z validators by including delegation metadata in the timelock. ZEC never leaves the holder's wallet.
3. **Z token stakers** delegate Z tokens to vote for validators directly on Z.

Validators are elected by a hybrid score combining delegation weight from all three sources. When elected validators do their job of securing Z, the validators and their voters earn consensus rewards.

---

## Designed for Agents

Agents transact thousands of times per day, generating metadata surface area that dwarfs human activity. Existing blockchains give them no structure, no simulation environment, no purpose-built execution rails. Z provides the structure that turns a capable model into a productive on-chain agent.

- **EVM compatibility.** Z runs Solidity. Agents built with any EVM-compatible framework work on Z without rewriting.
- **Protocol-level wallet hygiene.** Agents enter the MASP, then exit to fresh stealth addresses for each interaction. No observer can tie activity back to a single agent identity. The protocol handles this automatically.
- **Vertically integrated execution environment.** Z ships with a DEX, lending protocol, stablecoin, liquid staking, and more, all integrated with the privacy layer. Agents do not need to discover third-party protocols.
- **Agent-native tooling.** Each app in the Zcosystem gets its own MCP server. SKILL.md files, structured walkthroughs, inline ABIs, and copy-paste-ready code give agents everything they need to operate without guessing.
- **Simulation and preflight.** A deterministic sandbox lets agents test strategies against replayed mainnet state before deploying real capital.
- **Permissions at the protocol level.** Agent wallets support owner-controlled policies: spending caps, contract allowlists, session duration bounds, and kill switches.

---

## Gas

Z lets users pay gas in ZEC or the Z token. ZEC holders can access all core applications — trading, lending, borrowing, staking — without touching the Z token. When ZEC is used as gas, it is swapped into Z at the protocol level.

---

## What These Docs Cover

The sections that follow go deeper into each part of the architecture:

| Doc | Covers |
|-----|--------|
| [02 — Designed for Agents](./02-designed-for-agents.md) | EVM compatibility, wallet hygiene, agent tooling, simulation, permissions |
| [03 — Privacy Architecture](./03-privacy-architecture.md) | MASP, stealth addresses, ZK proofs, broadcaster, viewing keys |
| [04 — Consensus (ZSP)](./04-consensus-zsp.md) | Three-leg consensus, validator election, rewards, slashing, verification infrastructure |
| [05 — Dual Staking](./05-dual-staking.md) | How staking ZEC and Z tokens together multiplies yield |
| [06 — Liquid Staking](./06-liquid-staking.md) | stZEC, stZ, and the DeFi composition stack |
| [07 — Zcosystem](./07-zcosystem.md) | Every ZApp and how it integrates with privacy |
| [08 — USDZ and yUSDZ](./08-usdz-and-yusdz.md) | Private stablecoin system, reserve strategy, yield mechanics |
| [09 — Z Token](./09-z-token.md) | Token utility and economic model |
| [10 — Bridges](./10-bridges.md) | SPV-verified ZEC bridge and LayerZero for other assets |
| [11 — Compliance](./11-compliance.md) | How privacy and regulatory requirements coexist |
| [12 — Roadmap](./12-roadmap.md) | What ships at launch and what comes after |
| [13 — Glossary](./13-glossary.md) | Terms and definitions |
```