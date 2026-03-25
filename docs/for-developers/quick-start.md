---
title: "Developer Quick Start"
sidebarTitle: "Developer Quick Start"
description: "Connect to Z, deploy your first contract, and run a local devnet. Z is EVM-compatible — existing Solidity contracts deploy without modification."
keywords: ['quick start', 'developer', 'EVM', 'Solidity', 'deploy', 'devnet', 'Docker', 'ZSP', 'network config']
ai_summary: "Z is EVM-compatible. Existing Solidity deploys to Z without code changes, just network config update. Local development: Docker-based ZSP devnet with ./cli init, ./cli start, ./cli smoke. RPC endpoint defaults: HTTP localhost:8545, WebSocket localhost:8546. Production network details (chain ID, RPC URL, block explorer, testnet faucet) not yet finalized — requires engineering team input."
---

# Developer Quick Start

Z is EVM-compatible. Existing Solidity contracts deploy to Z without code changes. Your existing toolchain (Hardhat, Foundry, Remix, ethers.js, viem) works without modification.

---

## Prerequisites

- Solidity knowledge and an existing EVM development setup
- Docker and Docker Compose (for local devnet)
- Node.js v19+ (for CLI tooling)

---

## Local devnet (fastest path to start)

The ZSP devnet is a Docker-based local stack with full ZSP consensus, Zcash regtest integration, and protocol inspection tools.

**Setup:**

```bash
git clone https://github.com/protocol-z/devnet
cd devnet
cp .env.example .env
./cli init
./cli start
./cli smoke
```

The `smoke` command runs a basic health check confirming the stack is up and functioning.

**What the devnet runs:**

- ZSP nodes: bootnode, 4 validators, rpc0, rpc1, archive node
- Support services: rpc-proxy, metrics-proxy, indexer
- Zcash lane: zcashd regtest, zcash-relayer, zcashpowermirror
- Monitoring: Prometheus, Grafana, Loki

**Local RPC endpoints:**

| Service | URL |
|---------|-----|
| RPC0 HTTP | `http://localhost:8545` |
| RPC0 WebSocket | `ws://localhost:8546` |
| RPC1 HTTP | `http://localhost:8547` |
| RPC Proxy HTTP | `http://localhost:8580` |
| RPC Proxy WebSocket | `ws://localhost:8581` |
| Zcash RPC | `http://localhost:8232` |

**Using the faucet (local devnet):**

```bash
./cli faucet evm <your-address>
./cli faucet zec <your-zcash-address>
```

**Inspecting contracts:**

```bash
./cli inspect contracts --json
./cli inspect validators
./cli inspect epoch
```

**CLI reference:**

```bash
./cli start [--with-porter] [--ephemeral]
./cli stop
./cli reset [runtime|monitoring|contracts|wallet|all]
./cli status [--json]
./cli info [--json]
./cli logs <service>
./cli rpc [zsp|proxy|rpc1|archive|zcash] <method> [params]
./cli trace tx <hash>
./cli scenario <name>
```

For the full devnet documentation, see [Local Devnet](/docs/for-developers/local-devnet).

---

## Network config (Hardhat)

```javascript
module.exports = {
  networks: {
    zLocal: {
      url: "http://localhost:8545",
      chainId: /* ⚠ see below */,
      accounts: [process.env.PRIVATE_KEY]
    }
  }
};
```

> **⚠ Engineering input required:** Production mainnet chain ID, RPC URL, block explorer URL, and testnet (Moderato) faucet URL are not yet finalized. Update this section when infrastructure details are confirmed.

---

## Deploy your first contract

No code changes are needed for existing Solidity contracts.

**Foundry:**

```bash
forge create --rpc-url http://localhost:8545 \
  --private-key $PRIVATE_KEY \
  src/MyContract.sol:MyContract
```

**Hardhat:**

```bash
npx hardhat run scripts/deploy.js --network zLocal
```

**Verification:**

```bash
./cli rpc zsp eth_getCode <deployed-address> latest
```

---

## What's different from Ethereum

Z is EVM-compatible but has two additions developers should know about:

1. **Two custom precompiles:** Equihash (0x0B) and Blake2b (0x0C) for Zcash verification. These don't affect your contracts unless you're building ZSP infrastructure.

2. **Gas token:** The native gas token is the Z token. ZEC can be used as gas via an ERC-4337 Paymaster — users never handle the conversion directly.

For the complete list of differences, see [EVM Compatibility](/docs/for-developers/evm-compatibility).

---

## Next steps

| Goal | Page |
|------|------|
| Understand what's different from Ethereum | [EVM Compatibility](/docs/for-developers/evm-compatibility) |
| Run integration tests and scenarios | [Local Devnet](/docs/for-developers/local-devnet) |
| Integrate with the ShieldedPool | [MASP Integration Guide](/docs/for-developers/masp-integration-guide) |
| Build a private DeFi ZApp | [RelayAdapter Pattern](/docs/for-developers/relayAdapter-pattern) |
| Route transactions through a broadcaster | [Broadcaster Network](/docs/for-developers/broadcaster-network) |
| Write agent walkthroughs | [SKILL.md Reference](/docs/for-developers/skillmd-reference) |
