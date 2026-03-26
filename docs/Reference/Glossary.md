---
title: "Glossary"
sidebarTitle: "Glossary"
ai_summary: "Authoritative definitions for all Z Protocol terminology organized by category: protocol, privacy, consensus, assets and tokens, technical, agent infrastructure, and bridge terms."
keywords: ["glossary", "definitions", "MASP", "ShieldedPool", "ZSP", "Z token", "ZEC", "staking", "Groth16", "MCP", "Shell", "RelayAdapter", "nullifier", "commitment"]
---
Authoritative definitions for all Z Protocol terms. When terminology is in doubt, this page is the source of truth.

---

## Protocol

| Term | Definition |
|---|---|
| Z | Privacy platform for human and agentic on-chain action. EVM-compatible. |
| Z Protocol | Formal name for the protocol, distinguishing it from the Z token. |
| [[The Zcosystem\|Zcosystem]] | The application layer: [[Z Trade]], [[Z Lend]], [[Z Stake]], [[USDZ]], [[yUSDZ]], and future apps. |
| ZApp | An individual application within the Zcosystem. |
| Zed | The anonymous founder persona. |

---

## Privacy

| Term | Definition |
|---|---|
| MASP | Multi-Asset Shielded Pool. The core privacy primitive. See [[ShieldedPool (MASP)]]. |
| ShieldedPool | Common name for the MASP contract system. |
| Note | A UTXO-based commitment representing a private balance inside the MASP. |
| Commitment | The Poseidon hash of a note's contents, stored in the Merkle tree. |
| Nullifier | A one-time value published when a note is spent. Prevents double-spending without revealing which note was consumed. |
| Stealth address | A one-time address used for unlinkable app interactions. |
| Viewing key | A derived key granting read-only access to shielded transaction history. See [[Privacy and Compliance]]. |
| Spending key | The key that authorizes spending from shielded notes. Never shared. |

**Precision note:** "Shielded" describes in-pool activity. "Unlinkable" describes stealth address DeFi interactions. "Private" is the general umbrella term.

---

## Consensus

| Term | Definition |
|---|---|
| [[Zcash Satoshi Plus (ZSP)\|ZSP]] | Zcash Satoshi Plus. Z's hybrid consensus mechanism combining three staking legs. |
| Validator | A node operator elected through the ZSP scoring system. |
| DPoW (Leg 1) | Delegated Proof of Work. ZEC miners delegate hashrate to validators. |
| Self-custodial staking (Leg 2) | ZEC holders timelock ZEC on the Zcash chain to vote for validators. See [[ZEC Staking]]. |
| DPoS (Leg 3) | Delegated Proof of Stake. Z token holders stake to validators. |
| [[Dual Staking]] | Boosted yield for participants staking both ZEC and Z tokens. |
| Round | A 24-hour validator election and reward cycle. |

---

## Assets and Tokens

| Term | Definition |
|---|---|
| [[The Z Token\|Z token]] | Governance and value-capture token. Used for gas, staking, and governance. |
| ZEC | Zcash's native token. Participates in ZSP consensus and can pay gas via Paymaster. |
| [[USDZ]] | Z's stablecoin. Pegged 1:1 to USD, backed by stablecoin reserves. |
| [[yUSDZ]] | Yield-bearing USDZ. ERC-4626 vault with non-rebasing share price. |
| stZEC | Liquid staking receipt token for staked ZEC. See [[Liquid Staking]]. |
| stZ | Liquid staking receipt token for staked Z tokens. |
| wZEC | Wrapped ZEC on the Z chain, received after bridging via the [[Z Bridge]]. |

---

## Technical

| Term | Definition |
|---|---|
| Groth16 | The zero-knowledge proof system used by the MASP. |
| BN254 | The elliptic curve used for ZK proof verification (ecPairing precompile). |
| Poseidon | The hash function used for note commitments and nullifier derivation. |
| ERC-4337 | Account abstraction standard. Basis for Shells. |
| ERC-5564 | Stealth address standard used for unlinkable DeFi interactions. |
| Paymaster | ERC-4337 contract that allows ZEC holders to pay gas without acquiring Z tokens. |
| RelayAdapter | Contract pattern for atomic unshield-execute-reshield DeFi operations. |

---

## Agent Infrastructure

| Term | Definition |
|---|---|
| MCP | Model Context Protocol. Standardized interface for AI agent tool integration. |
| SKILL.md | Structured walkthrough format designed for LLM consumption. |
| KYA | Know Your Agent. Agent identity and capability attestation. |
| Shell | An ERC-4337 smart contract wallet with a policy engine for agent operations. |

---

## Bridge

| Term | Definition |
|---|---|
| [[Z Bridge]] | Bridge infrastructure for moving ZEC onto Z. Implementation details in development. |
| LayerZero | Cross-chain messaging protocol used for non-ZEC asset bridging. |
| Direct-to-MASP | A deposit path where bridged assets enter the ShieldedPool directly, skipping public balance. |
