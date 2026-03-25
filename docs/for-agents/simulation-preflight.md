---
title: "Simulation & Preflight"
sidebarTitle: "Simulation & Preflight"
description: "A deterministic sandbox lets agents test strategies against replayed mainnet state before deploying real capital. Pre-built scenarios, custom scenario authoring, backtesting, and multi-agent simulation."
keywords: ['simulation', pre'flight, 'sandbox', 'backtesting', 'mainnet replay', 'agent testing', 'price swings', 'liquidation cascade', 'multi-agent']
ai_summary: "Z's simulation environment is deterministic — not a testnet. Replays mainnet state against controlled scenarios. Pre-built scenarios: price swings, liquidation cascades, adversarial competing agents, gas spikes, policy violations/shell revocations. Custom scenario authoring via config files (initial state, price feeds, agent actions, failure injections). Backtesting: replay strategy against historical mainnet state. Multi-agent simulation: test behavior when competing agents are present. Run via CLI. Scenarios are deterministic so test failures are reproducible."
---

# Simulation & Preflight

Agents managing real capital need to test strategies before deploying them. Z provides a deterministic simulation environment that goes beyond standard testnets.

---

## Why not just use testnet?

Testnet has different liquidity, different pool depths, and different participants than mainnet. A strategy that works on testnet may fail on mainnet because of different slippage, different adversarial behavior, or different volatility patterns.

Z's simulation sandbox replays mainnet state against controlled scenarios. You define the conditions. The sandbox runs them deterministically. Test failures are reproducible — you can identify exactly which market condition caused the failure.

---

## Local agent sandbox

The sandbox replays mainnet state against controlled scenarios defined as config files.

**How to define a scenario:**
- **Initial state:** Starting balances, pool depths, oracle prices
- **Price feeds:** Custom price sequences for any asset
- **Agent actions:** The sequence of operations your agent will execute
- **Failure injections:** Specific conditions to trigger (policy violations, session revocations, bundler congestion)

The sandbox runs these deterministically. Test failures reproduce exactly — the same inputs produce the same outputs every run.

---

## Pre-built scenarios

These scenarios ship with the SDK. Use them directly or as a starting point for custom scenarios.

**Price swings and volatility spikes.** Test how your agent responds to rapid price movement. Does it trigger stop-losses? Does it rebalance without causing excessive slippage?

**Liquidation cascades.** Does your lending agent maintain its health factor when multiple positions are liquidated simultaneously? Can it repay debt before being liquidated?

**Adversarial competing agents.** How does your agent perform when competing agents are present? Does it lose to front-running? On Z, stealth address hygiene makes sandwich attacks against MASP-transacting agents much harder — but this scenario tests resilience regardless.

**Gas spikes and bundler congestion.** Does your agent still profit under elevated fees? Does it correctly prioritize which transactions to submit?

**Policy violations and session revocations.** What happens when the agent tries to exceed its spending cap? When the session key expires mid-operation?

---

## Custom scenario authoring

Define your own scenarios as config files:

```yaml
name: flash-crash-recovery
initial_state:
  zec_price: 50
  usdz_liquidity: 1000000
  agent_capital: 10000

events:
  - time: 0m    type: price_feed  asset: ZEC  value: 50
  - time: 5m    type: price_feed  asset: ZEC  value: 30  # flash crash
  - time: 8m    type: price_feed  asset: ZEC  value: 48  # recovery
  - time: 10m   type: price_feed  asset: ZEC  value: 50

assertions:
  - type: health_factor  min: 1.1  at: 10m
  - type: capital_preservation  min_percent: 80
```

---

## Backtesting

Replay your strategy against historical mainnet state to measure real performance:

- Test how a rebalancing agent would have performed during past volatility spikes
- Test how a lending agent would have handled a historical liquidation cascade
- Get concrete numbers before real capital is at risk

---

## Multi-agent simulation

Test how your agent behaves when competing agents are present. Simulate the full environment: multiple agents, shared liquidity, concurrent transactions. An agent that works perfectly in isolation may behave differently under competition.

---

## Running the simulator

```bash
z-sandbox run-agent ./my-agent.ts

z-sandbox run-scenario liquidation-cascade --agent ./my-agent.ts

z-sandbox run-custom ./scenarios/my-scenario.yaml --agent ./my-agent.ts
```

> **⚠ Engineering input required:** The exact CLI commands, package names, and configuration format for the simulation SDK require confirmation from the engineering team. The commands above illustrate the intended interface.
