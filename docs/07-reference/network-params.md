---
id: network-params
title: "Network Parameters"
sidebar_label: "Network Parameters"
sidebar_position: 2
description: "Complete reference for Core blockchain network parameters, chain IDs, RPC endpoints, and on-chain configuration values."
keywords: ["network", "parameters", "chain-id", "RPC", "mainnet", "testnet", "configuration"]
tags: ["reference", "network", "configuration"]
hide_table_of_contents: false
pagination_next: null
pagination_prev: "glossary"
---

# Network Parameters

Complete reference for Core's mainnet and testnet configuration values.

---

## Mainnet

| Parameter | Value |
|-----------|-------|
| **Network Name** | Core Blockchain Mainnet |
| **Chain ID** | 1116 |
| **Currency Symbol** | CORE |
| **Currency Decimals** | 18 |
| **[RPC](/docs/07-reference/glossary#r) URL (Primary)** | `https://rpc.coredao.org` |
| **RPC URL (Secondary)** | `https://rpc-core.icecreamswap.com` |
| **Block Explorer** | `https://scan.coredao.org` |
| **WebSocket** | `wss://ws.coredao.org` |
| **Block Time** | ~3 seconds |
| **Epoch / Round** | 200 blocks |
| **[Validator](/docs/07-reference/glossary#v) Set Size** | 21 active validators |

### Mainnet Contract Addresses

| Contract | Address |
|----------|---------|
| Validator Set | `0x0000000000000000000000000000000000001000` |
| Slash Indicator | `0x0000000000000000000000000000000000001001` |
| System Reward | `0x0000000000000000000000000000000000001002` |
| Bitcoin Light Client | `0x0000000000000000000000000000000000001003` |
| BTC Stake | `0x0000000000000000000000000000000000001004` |
| [Governance](/docs/07-reference/glossary#g) | `0x0000000000000000000000000000000000001005` |
| Relay Hub | `0x0000000000000000000000000000000000001006` |
| Candidate Hub | `0x0000000000000000000000000000000000001007` |
| [stCORE](/docs/07-reference/glossary#s) | `0xD3B5B60020504bc3489D6949d545B44F0F3e6f95` |

---

## Testnet2 (Current)

> ⚠️ **Note:** Testnet1 (Chain ID 1115) has been decommissioned. Use Testnet2 (Chain ID 1114) for all new development.

| Parameter | Value |
|-----------|-------|
| **Network Name** | Core Blockchain Testnet2 |
| **Chain ID** | 1114 |
| **Currency Symbol** | tCORE2 |
| **Currency Decimals** | 18 |
| **RPC URL (Primary)** | `https://rpc.test2.btcs.network` |
| **Block Explorer** | `https://scan.test2.btcs.network` |
| **WebSocket** | `wss://ws.test2.btcs.network` |
| **Faucet** | `https://scan.test2.btcs.network/faucet` |
| **Block Time** | ~3 seconds |

### Testnet2 Contract Addresses

| Contract | Address |
|----------|---------|
| Validator Set | `0x0000000000000000000000000000000000001000` |
| Slash Indicator | `0x0000000000000000000000000000000000001001` |
| System Reward | `0x0000000000000000000000000000000000001002` |
| Bitcoin Light Client | `0x0000000000000000000000000000000000001003` |
| BTC Stake | `0x0000000000000000000000000000000000001004` |
| Governance | `0x0000000000000000000000000000000000001005` |

---

## Consensus Parameters

| Parameter | Value |
|-----------|-------|
| **Max Validators** | 21 |
| **[Validator Election](/docs/07-reference/glossary#v) Interval** | Every 200 blocks (1 round) |
| **Minimum Validator Self-Delegation** | 10,000 CORE |
| **[Slashing](/docs/07-reference/glossary#s): Unavailability Threshold** | Missing 50 of last 150 blocks |
| **Slashing: Unavailability Penalty** | 10 CORE |
| **Slashing: Double-Sign Penalty** | 1,000 CORE + jail |
| **Reward Epoch** | 1 round (200 blocks) |
| **Block Gas Limit** | 40,000,000 |
| **Base Fee** | Dynamic (EIP-1559) |

---

## Staking Parameters

| Parameter | Value |
|-----------|-------|
| **Minimum CORE Stake** | 1 CORE |
| **Minimum BTC Stake** | 0.01 BTC |
| **Minimum BTC Lock Period** | 10 days |
| **Maximum BTC Lock Period** | 1,200 days |
| **stCORE Redemption Period** | ~1–2 rounds |

---

## Dual Staking Reward Tiers

| Tier | CORE:BTC Ratio (per validator) | BTC APR Boost |
|------|-------------------------------|---------------|
| Tier 0 (Base) | < 1,000 CORE per BTC | 1× base rate |
| Tier 1 | ≥ 1,000 CORE per BTC | 1.5× |
| Tier 2 | ≥ 3,000 CORE per BTC | 2× |
| Tier 3 (Max) | ≥ 8,000 CORE per BTC | 3× |

> Tier thresholds are governance-adjustable and may change over time.

---

## EVM Precompile Addresses

| Precompile | Address | Description |
|-----------|---------|-------------|
| ECRecover | `0x0000000000000000000000000000000000000001` | ECDSA signature recovery |
| SHA256 | `0x0000000000000000000000000000000000000002` | SHA-256 hash |
| RIPEMD-160 | `0x0000000000000000000000000000000000000003` | RIPEMD-160 hash |
| Identity | `0x0000000000000000000000000000000000000004` | Data copy |
| ModExp | `0x0000000000000000000000000000000000000005` | Modular exponentiation |
| BN256Add | `0x0000000000000000000000000000000000000006` | Elliptic curve addition |
| BN256Mul | `0x0000000000000000000000000000000000000007` | Elliptic curve multiplication |
| BN256Pairing | `0x0000000000000000000000000000000000000008` | Elliptic curve pairing |
| Blake2F | `0x0000000000000000000000000000000000000009` | BLAKE2 compression |

---

## Resources

- [Core Mainnet Explorer](https://scan.coredao.org)
- [Core Testnet2 Explorer](https://scan.test2.btcs.network)
- [Core GitHub](https://github.com/coredao-org)
- [Whitepaper](https://whitepaper.coredao.org)

## Related

- [Network Configuration](/docs/network-configuration)
- [RPC Providers](/docs/rpc-providers)
- [Full Node Configuration](/docs/full-node)
