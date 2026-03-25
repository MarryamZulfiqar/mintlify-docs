---
title: "Local Devnet"
description: "Docker-based local ZSP devnet with Zcash regtest integration, protocol inspection, scenario runners, and full integration test wiring. Everything you need to develop against the full Z stack locally."
keywords: ['devnet', 'Docker', 'ZSP', 'local development', 'Zcash regtest', 'scenarios', 'fixtures', 'CLI', 'RPC endpoints', 'integration testing']
ai_summary: "ZSP devnet is a Docker-based local stack from github.com/protocol-z/devnet. Runs: ZSP nodes (bootnode, 4 validators, rpc0, rpc1, archive, full-sync), Zcash lane (zcashd regtest, zcash-relayer, zcashpowermirror), support services (rpc-proxy, indexer), monitoring (Prometheus, Grafana, Loki). System contracts deployed: ValidatorSet, CandidateHub, SlashIndicator, SystemReward, GovHub, StakeHub, EquihashPowerAgent, ZcashLightClient, ZcashAgent, ZcashStake, RelayerHub. Quick start: cp .env.example .env && ./cli init && ./cli start && ./cli smoke. RPC defaults: HTTP 8545/8547, WS 8546/8548, archive 8555, rpc-proxy HTTP 8580, rpc-proxy WS 8581, Zcash 8232. Full CLI surface for status, inspection, scenarios, faults, snapshots."
---

# Local Devnet

The ZSP devnet is a Docker-based local stack with full ZSP consensus, Zcash regtest integration, protocol inspection helpers, scenario runners, and integration test wiring.

**Repository:** `github.com/protocol-z/devnet`

---

## What it runs

The devnet brings up a full local stack:

| Component | What it is |
|-----------|-----------|
| ZSP nodes | bootnode, 4 validators, rpc0, rpc1, archive, full-sync |
| rpc-proxy | Method filtering and rate limiting for RPC |
| metrics-proxy | Prometheus metrics aggregation |
| indexer | Event and transaction indexing |
| zcashd regtest | Local Zcash regtest chain |
| zcash-relayer | Submits Zcash headers to ZcashLightClient |
| zcashpowermirror | Mirrors Zcash mining for devnet |
| Prometheus / Grafana / Loki | Monitoring and log aggregation |

---

## Quick start

```bash
git clone https://github.com/protocol-z/devnet
cd devnet
cp .env.example .env
./cli init
./cli start
./cli smoke
```

**With the porter lane (ZEC staking delegation):**
```bash
./cli start --with-porter
```

**Ephemeral mode (no persistent state):**
```bash
./cli start --ephemeral
```

---

## Deployed contracts

The generated contract registry (`./state/registry/addresses.json`) includes:

**System contracts:** `ValidatorSet`, `CandidateHub`, `SlashIndicator`, `SystemReward`, `GovHub`, `PledgeAgent`, `Burn`, `Foundation`, `StakeHub`, `CoreAgent`

**ZSP-specific:** `HashPowerAgent` / `EquihashPowerAgent`, `ZcashLightClient`, `RelayerHub`, `Configuration`, `Channel`, `ZcashAgent`, `ZcashStake`

Inspect deployed addresses:
```bash
./cli inspect contracts --json
```

---

## RPC endpoints

| Service | URL |
|---------|-----|
| RPC0 HTTP | `http://localhost:8545` |
| RPC0 WebSocket | `ws://localhost:8546` |
| RPC1 HTTP | `http://localhost:8547` |
| RPC1 WebSocket | `ws://localhost:8548` |
| Archive HTTP | `http://localhost:8555` |
| RPC Proxy HTTP | `http://localhost:8580` |
| RPC Proxy WebSocket | `ws://localhost:8581` |
| Zcash RPC | `http://localhost:8232` |
| Prometheus | `http://localhost:9090` |
| Grafana | `http://localhost:3000` |

The RPC proxy (8580/8581) sits in front of rpc0 and applies method filtering and rate limiting. Use the direct RPC (8545) during development; use the proxy to test production-like behavior.

---

## CLI reference

```bash
# Lifecycle
./cli init
./cli start [--with-porter] [--ephemeral]
./cli stop
./cli reset [runtime|monitoring|contracts|wallet|all]

# Status
./cli status [--json]
./cli info [--json]
./cli doctor [--json]
./cli logs <service>
./cli smoke

# RPC and testing
./cli rpc [zsp|proxy|rpc1|archive|zcash] <method> [params]
./cli faucet [evm|zec] <address> [amount]
./cli trace tx <hash> [--json]

# Inspection
./cli inspect [contracts|validators|epoch]
./cli inspect [stake|delegation] <value>

# Scenarios and testing
./cli fault [pause|resume|restart] <service>
./cli fault [zcash-rpc|relayer|porter] [down|up]
./cli scenario <name>
./cli fixture [list|load] <name>
./cli snapshot [save|load|list] <name>
./cli bugreport capture <n>
./cli test <suite>
./cli simulate-load [--requests N] [--concurrency N]
./cli zcash-mine [count]
```

---

## Built-in scenarios

| Scenario | What it tests |
|----------|--------------|
| `zcash-sync` | Mine Zcash blocks and verify relayer catch-up |
| `porter-happy` | Full porter delegation flow with on-chain evidence |
| `reorg-recovery` | Induce a Zcash reorg and confirm downstream recovery |
| `relayer-restart-recovery` | Restart the relayer and confirm catch-up |
| `porter-restart-recovery` | Restart porter and confirm resumed processing |
| `backlog-catchup` | Create lag and verify relayer and porter drain backlog |
| `validator-rotation` | Bounce a validator and confirm chain continues |

---

## Fixtures

Pre-built state snapshots for common starting points:

- `fresh` — clean genesis state
- `zcash-synced` — Zcash headers synced to ZcashLightClient
- `porter-ready` — porter lane initialized and ready
- `post-delegation` — a delegation has been submitted
- `reorg-recovered` — after a reorg recovery

Load a fixture:
```bash
./cli fixture load porter-ready
```

---

## Test suites

| Suite | Coverage |
|-------|---------|
| `smoke` | Basic health checks |
| `doctor` | Detailed diagnostics |
| `chain` | Chain progression and finality |
| `load` | Throughput under load |
| `zcash` | Zcash header sync and validation |
| `zcash-fidelity` | Zcash-specific protocol fidelity |
| `e2e-porter` | End-to-end porter delegation |
| `repo-relayer` | Relayer integration |
| `repo-porter` | Porter integration |
| `daily-loop` | Extended stability loop |

---

## Workspace layout

The devnet is designed to run against sibling repos in the same parent directory:

```
../zsp-chain             ZSP chain implementation
../rpc-proxy             RPC proxy
../zcash-relayer         Zcash header relayer
../zcashpowermirror      Zcash PoW mirror
../ZSPStakingPorter      Porter (optional, --with-porter)
../zsp-genesis-contract  Contract source and address generation
```

Integration environment files for each sibling are generated at `./state/integrations/`.

---

## State persistence

All rendered configs and runtime data live under `./state/`:

| Path | Contents |
|------|---------|
| `./state/registry/addresses.json` | Contract registry |
| `./state/registry/services.json` | Service registry |
| `./state/integrations/*.env` | Integration env files |
| `./state/snapshots/` | Named snapshots |
| `./state/fixtures/` | Fixture definitions |
| `./state/scenarios/` | Scenario definitions |
| `./state/bugreports/` | Captured bug reports |
