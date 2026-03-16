---
id: glossary
title: "Glossary"
sidebar_label: "Glossary"
sidebar_position: 1
description: "Definitions of key terms and concepts used throughout the Core blockchain documentation."
keywords: ["glossary", "terminology", "definitions", "Core", "blockchain"]
tags: ["reference", "glossary"]
hide_table_of_contents: false
pagination_next: "network-params"
pagination_prev: "hardware-wallet-integration-faqs"
---

# Glossary

A comprehensive reference for terminology used throughout the Core blockchain documentation.

---

## A

**Archive Node**
A full node that also stores all historical state data in addition to the blockchain data. Required for querying historical balances or state at arbitrary block heights.

**ATOM (Atomic Transaction)**
An indivisible transaction unit where either all operations succeed or all fail — ensuring data consistency.

---

## B

**Bitcoin Staking**
Core's native mechanism allowing Bitcoin holders to participate in securing the Core network using CLTV (CheckLockTimeVerify) timelocks, earning CORE token rewards without moving Bitcoin off the Bitcoin network.

**Block Producer**
A validator node elected to produce blocks during a given round. On Core, 21 validators are elected per round.

**BTC Hash Delegation**
The ability for Bitcoin miners to signal their support for Core validators in their coinbase transaction, contributing to the Delegated Proof of Work component of Satoshi Plus.

---

## C

**CLTV (CheckLockTimeVerify)**
A Bitcoin Script opcode that locks outputs until a specified time or block height, used by Core's Non-Custodial Bitcoin Staking to timelock BTC.

**CORE Token**
The native utility and governance token of the Core blockchain. Used for gas fees, validator staking, governance voting, and reward distribution.

**coreBTC**
A synthetic Bitcoin asset on Core backed 1:1 by real Bitcoin, enabling BTC use within Core's EVM ecosystem.

**Core Scan**
The official block explorer for Core blockchain, available at [scan.coredao.org](https://scan.coredao.org).

---

## D

**DPoS (Delegated Proof of Stake)**
A consensus mechanism where token holders delegate their stake to validators who produce blocks. On Core, CORE token holders delegate stake to validators as part of the Satoshi Plus consensus.

**DPoW (Delegated Proof of Work)**
Core's adaptation of Bitcoin's Proof of Work into a delegated system where miners write metadata into Bitcoin coinbase transactions to vote for Core validators.

**Dual Staking**
A Core feature where users simultaneously stake both BTC and CORE tokens to receive boosted staking yields, with reward tiers based on the ratio of CORE to BTC staked.

---

## E

**EVM Compatibility**
Core's full compatibility with the Ethereum Virtual Machine, allowing any Ethereum smart contract or tool to run on Core without modification.

---

## F

**Full Node**
A node that downloads and validates all blocks and transactions. Serves as an entry point to the Core network for developers and dApp operators.

---

## G

**Gas**
The unit measuring computational effort on Core. Gas fees are paid in CORE tokens. Core uses a modified EIP-1559 fee mechanism.

**Governance**
Core's on-chain governance system allowing CORE token holders to vote on protocol parameter changes and upgrades.

---

## L

**Liquid Staking**
The ability to stake CORE tokens while receiving a liquid derivative (stCORE) that can be used in DeFi applications, maintaining liquidity while earning staking rewards.

---

## N

**Non-Custodial Bitcoin Staking**
Core's BTC staking design where users retain full custody of their Bitcoin at all times. BTC is locked via CLTV timelocks on the Bitcoin network — no bridging required.

---

## R

**Relayer**
A network participant that monitors the Bitcoin blockchain and relays relevant transaction data (BTC staking/delegation info) to the Core network for processing.

**Rev+**
Core's Revenue Sharing model that distributes a portion of transaction fees from smart contracts back to the contract developers.

**Round**
A fixed epoch on Core (currently 200 blocks / ~10 minutes) after which validator elections occur and rewards are distributed.

**RPC (Remote Procedure Call)**
The protocol used by clients to interact with Core nodes. Core is fully compatible with Ethereum JSON-RPC endpoints.

---

## S

**Satoshi Plus Consensus**
Core's proprietary consensus mechanism combining Delegated Proof of Work (DPoW) from Bitcoin miners, Non-Custodial Bitcoin Staking, and Delegated Proof of Stake (DPoS) from CORE token holders.

**Slashing**
A penalty mechanism that reduces a validator's staked CORE for misbehavior such as double-signing or prolonged downtime.

**stCORE**
The liquid staking derivative of CORE. When users stake CORE through Core's liquid staking protocol, they receive stCORE which accrues staking rewards over time.

---

## T

**Testnet2 (Chain ID 1114)**
Core's current active testnet. The previous testnet (Chain ID 1115) has been decommissioned.

**Timelocked BTC**
Bitcoin locked using CLTV opcodes for a fixed duration to participate in Core's Non-Custodial Bitcoin Staking.

**Tokenomics**
The economic model governing CORE token supply, distribution, inflation, and burn mechanics.

---

## V

**Validator**
An operator that runs a Core full node, participates in block production, and earns staking rewards. Validators must register on-chain and receive delegations from Bitcoin stakers, hash power delegators, and CORE stakers.

**Validator Election**
The process that occurs at the start of each round where the top 21 validators (by hybrid score) are selected to produce blocks.

---

## W

**Whitepaper**
Core's official technical paper describing the Satoshi Plus consensus mechanism in full detail. Available at [whitepaper.coredao.org](https://whitepaper.coredao.org).

## Related

- [Satoshi Plus Overview](/docs/satoshi-plus-overview)
- [CORE Token Overview](/docs/core-token-overview)
- [Staking Overview](/docs/staking-overview)
