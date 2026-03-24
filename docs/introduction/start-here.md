---
title: "Start Here"
sidebarTitle: "Get Started"
---

# Start Here

Find your track below. Each path takes you to the right starting pages for your use case.

---

## I hold ZEC

Z creates yield opportunities for ZEC holders that don't require giving up custody or buying any other asset.

**What Z does for you:**

- Timelock ZEC on the Zcash chain and earn Z token rewards — ZEC never leaves your wallet, no bridge required
- Bridge ZEC to Z and stake into stZEC for liquid staking yield while keeping it composable across DeFi
- Use stZEC as collateral on Z Lend to borrow USDZ while maintaining ZEC price exposure

**Where to start:**

1. [ZEC Staking Overview](../staking/zec-staking-overview.md) — all three paths and the tradeoffs between them
2. [Self-Custodial Staking](../staking/self-custodial-staking.md) — how to timelock ZEC with no bridge
3. [Liquid Staking](../staking/liquid-staking.md) — stZEC and the DeFi composition stack

---

## I'm a DeFi user

Z provides a complete DeFi stack — trading, lending, borrowing, yield — where your positions stay private.

**What Z does for you:**

- Trade on Z Trade without revealing your strategy or wallet history
- Borrow and lend on Z Lend with private positions
- Hold USDZ as a private stablecoin, or earn yield with yUSDZ
- The ShieldedPool connects everything: assets shield in, interact with DeFi via stealth addresses, and reshield

**Where to start:**

1. [The Zcosystem](../core-concepts/the-zcosystem.md) — what the application layer is and how it composes
2. [USDZ](../products/usdz.md) — Z's private stablecoin
3. [Z Trade](../products/z-trade.md) — how private trading works
4. [Z Lend](../products/z-lend.md) — borrowing and lending with private positions

---

## I'm a developer or builder

Z is EVM-compatible. Solidity works. Existing tooling carries over. Your first Z contract requires no code changes — just a network config update.

**What Z does for you:**

- Run existing Solidity contracts on Z with no modifications
- Integrate with the ShieldedPool via the RelayAdapter pattern for private DeFi
- Build on top of enshrined primitives (Z Trade, Z Lend, USDZ) instead of discovering and auditing third-party protocols
- Access a local devnet for full-stack ZSP \+ Zcash development

**Where to start:**

1. [Quick Start](../developers/quick-start.md) — network config, deploy a contract, first shielded transfer
2. [EVM Compatibility](../developers/evm-compatibility.md) — what carries over, what's new, what's different
3. [Local Devnet](../developers/local-devnet.md) — full ZSP \+ Zcash stack running locally via Docker

---

## I'm building AI agents

Z is designed as infrastructure for AI agents. Protocol-level wallet hygiene, vertically integrated DeFi, agent-native tooling, and protocol-enforced permissions.

**What Z does for you:**

- Agents enter the ShieldedPool and exit to fresh stealth addresses per interaction — no persistent identity, no linkable history
- One trusted DeFi stack that agents can navigate reliably without evaluating third-party protocols
- MCP servers for each ZApp, SKILL.md walkthroughs, and LLM-native documentation
- ERC-4337 smart account wallets with spending caps, contract allowlists, session keys, and kill switches

**Where to start:**

1. [Z for AI Agents](../for-agents/z-for-ai-agents.md) — the scaffolding model and what Z provides
2. [AI Tooling Setup](../for-agents/ai-tooling-setup.md) — MCP installation and configuration
3. [Permissions & Session Keys](../for-agents/permissions-session-keys.md) — how to constrain agents safely

---

## I'm a Zcash miner

Embed delegation metadata in the blocks you already mine. No new hardware, no new energy, no changes to your existing operation.

**What Z does for you:**

- Earn Z token rewards as supplemental income on top of your existing ZEC block rewards
- No changes to your mining setup — include metadata in OP\_RETURN outputs when you mine
- Mining pools can delegate on behalf of all participants

**Where to start:**

1. [ZEC Staking Overview](../staking/zec-staking-overview.md) — how miner delegation fits in the broader ZEC participation picture
2. [Zcash Satoshi Plus — Leg 1](../core-concepts/zcash-satoshi-plus.md#leg-1-zec-miners-delegated-proof-of-work) — the technical detail of how miner delegation works

---

## I want to run a validator

Validators are elected through the ZSP hybrid scoring system combining ZEC miner delegation, ZEC holder delegation, and Z token delegation.

**What Z does for you:**

- Earn commissions on delegator rewards
- Participate in block production on a growing protocol
- Hybrid scoring means validators must attract diverse delegator bases — not just token holders

**Where to start:**

1. [Zcash Satoshi Plus](../core-concepts/zcash-satoshi-plus.md) — the full consensus mechanism
2. [Validator Election](../core-concepts/zcash-satoshi-plus.md#validator-election) — how hybrid scoring works and how to register as a candidate

---

## I need compliance information

Z's privacy is designed to coexist with regulatory requirements through viewing keys, selective disclosure, and entry/exit screening.

**Where to start:**

1. [Privacy & Compliance](../compliance/privacy-and-compliance.md) — the full compliance architecture
2. [Selective Disclosure](../compliance/selective-disclosure.md) — how viewing keys work and how to scope them
3. [Boundary Enforcement](../compliance/boundary-enforcement.md) — entry/exit screening via ASPs