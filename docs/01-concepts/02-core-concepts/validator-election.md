---
id: satoshi-plus-validator-election
title: "Core Validator Election"
sidebar_label: "Validator Election"
sidebar_position: 6
description: "Core's Satoshi Plus consensus draws validator support from three delegation sources:"
keywords: ["core", "validator", "election"]
tags: ["satoshi-plus", "consensus"]
hide_table_of_contents: false
pagination_next: "concepts/core-concepts/rewards"
pagination_prev: "concepts/core-concepts/delegated-proof-of-stake"
---
# Core Validator Election

Core's validator election process determines which nodes are eligible to produce blocks in each consensus round, drawing from three independently weighted delegation sources: Bitcoin miners (DPoW), Bitcoin stakers, and CORE token stakers (DPoS). Understanding this election is key to understanding how [Satoshi Plus](/docs/07-reference/glossary#s) achieves decentralised security.

## Overview

Core's Satoshi Plus consensus draws validator support from three delegation sources:

- **Delegated Proof of Work ([DPoW](/docs/07-reference/glossary#d))** from Bitcoin miners
- **Self-Custodial [Bitcoin Staking](/docs/07-reference/glossary#b)** from Bitcoin holders
- **Delegated Proof of Stake ([DPoS](/docs/07-reference/glossary#d))** from CORE token holders

Each source contributes to a validator's **hybrid score**, which determines their election into the active validator set.

## Election Process

<p align="center">
![validator-election-flow](../../../../static/img/staoshi-plus/[Validator](/docs/07-reference/glossary#v)-Election-Workflow.png)
</p>

### 1. Delegation of Support

Validators receive support from three sources:

- **Bitcoin Holders**: Timelock Bitcoin using [CLTV](/docs/07-reference/glossary#c) and embed validator metadata in the redeem script to vote for validators.
- **Bitcoin Miners**: Delegate hash power by including validator votes in the op_return field of Bitcoin coinbase transactions.
- **CORE Token Holders**: Delegate CORE tokens directly to preferred validators.

Each form of delegation contributes to validator selection and reward eligibility.

### 2. Hybrid Score Calculation

Validators are ranked by a **hybrid score** that blends the three forms of support:

$$
 S = (\frac{rHp}{tHp})* m + (\frac{rSp}{tSp})*k + (\frac{rBp}{tBp})*l
$$

Where, $$ m + k + l = 1 $$

- $$rHp$$: Bitcoin hash power delegated to a validator.
- $$tHp$$: Total hash power on Core.
- $$rSp$$: Amount of CORE tokens staked to a validator.
- $$tSp$$: Total CORE tokens staked on Core.
- $$rBp$$: Amount of BTC tokens staked to a validator.
- $$tBp$$: Total BTC tokens staked on Core.
- $$m$$: Ratio assigned to hash power.
- $$k$$: Ratio assigned to CORE staking.
- $$l$$: Ratio assigned to BTC staking.

This formula promotes proportional and fair influence across all delegation sources.

### 3. Validator Selection and Block Production

- The **top 31 validators** with the highest hybrid scores are elected every **round (1 day)**
- Validators produce blocks in a **round-robin** fashion, with each slot lasting **3 seconds**
- The **validator set is updated every 200 blocks** to promote consistency and prevent disruption from underperforming or penalized validators
- Core maintains liveness as long as fewer than **⅓ of validators are malicious**

## Flow Summary

The validator election cycle proceeds as follows:

1. **Delegation**: Miners, CORE stakers, and Bitcoin stakers delegate support
2. **Score Calculation**: Hybrid scores are computed using the defined formula
3. **Election**: Top 31 validators are selected based on scores
4. **Block Production**: Validators take turns producing blocks
5. **Rewards**: CORE tokens are earned by validators and distributed proportionally to delegators
6. **Cycle Repeats**: Validator set updates every 200 blocks

## Related

- [Delegated Proof of Work (DPoW)](/docs/delegated-proof-of-work)
- [Delegated Proof of Stake (DPoS)](/docs/delegated-proof-of-stake)
- [Self-Custodial Bitcoin Staking](/docs/self-custodial-bitcoin-staking)
- [Consensus Rewards](/docs/rewards)
