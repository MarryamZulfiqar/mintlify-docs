---
title: "Z for AI Agents"
sidebarTitle: "Z for AI Agents"
description: "Z provides the scaffolding that turns a capable AI model into a productive on-chain agent. EVM compatibility, protocol-level wallet hygiene, vertically integrated execution, agent-native tooling, simulation, and protocol-enforced permissions."
keywords: ['AI agents', 'scaffolding', 'on-chain agents', 'EVM', 'wallet hygiene', 'MASP', 'stealth addresses', 'MCP', 'SKILL.md', 'agent permissions', 'simulation', 'ERC-4337', 'Shells']
ai_summary: "Z provides five layers of scaffolding for on-chain AI agents: (1) EVM compatibility — Solidity, existing tooling, no new language; (2) Protocol-level wallet hygiene — agents enter MASP, exit to fresh stealth addresses per interaction via ECDH (ERC-5564), no observer can link activity to persistent identity; (3) Vertically integrated DeFi — one trusted stack, agents don't evaluate third-party protocols; (4) Agent-native tooling — MCP servers per ZApp, SKILL.md walkthroughs, LLM-native docs, framework integrations (GOAT, ElizaOS, OpenAI Agents SDK, Coinbase AgentKit, LangChain); (5) Protocol-level permissions — ERC-4337 Shells with spending caps, allowlists, session keys, consensus-enforced kill switches, hierarchical parent/child architecture."
---

# Z for AI Agents

The difference between an AI agent that produces responses and one that takes actions in an environment is scaffolding — the structured system built around the agent that guides how it operates in a specific domain.

Claude Code is a clear example. Raw Claude is powerful, but without access to a development environment it is limited to text. Claude Code wraps the model in scaffolding: controlled file system access, terminal execution, project context, and structured tools. The model itself does not change. What changes is its ability to act within an environment.

Additional layers build on top. Projects like Compound Engineering from EveryInc add structured workflows for planning, spawning sub-agents, coordinating work, and executing engineering tasks reliably.

The same pattern applies to blockchains. Point a raw agent at a blockchain without scaffolding, and the results are chaotic. It guesses which protocols to use, misreads documentation, calls wrong contracts, constructs failing transactions, reuses the same address across interactions and accumulates a public transaction history that exposes strategy, relationships, and budgets through compounding metadata.

**Z provides scaffolding for agents to build and operate their own on-chain economies.**

In sum:
- Claude Code provides scaffolding for agents to act within development environments.
- Compound Engineering provides scaffolding for agents to coordinate complex engineering work.
- **Z provides scaffolding for agents to build and operate their own on-chain economies.**

---

## EVM compatibility

Z provides an EVM-compatible execution environment for Solidity smart contracts. Existing contracts, development frameworks, testing tools, libraries, and developer workflows carry over. Agents that generate, deploy, or interact with Solidity contracts can operate on Z using the same assumptions they use on other EVM networks.

Competing privacy platforms require new languages — Noir, Cairo, Leo — with tiny developer pools and immature tooling. Z's EVM compatibility is the single largest friction reducer for adoption. Any agent framework targeting EVM ecosystems works on Z without modification.

---

## Protocol-level wallet hygiene

An agent using a single wallet on a transparent chain leaves a record tied to one identity. Anyone watching the chain can link activity, map counterparties, infer strategies, and fingerprint behavior.

Z prevents this by design.

**How it works.** An agent holds assets in the MASP. When it needs to interact with DeFi, funds exit the pool to a fresh stealth address generated through ECDH key exchange (ERC-5564). That address has no on-chain link to the agent's pool balance or any previously used address. The DeFi interaction happens from that address. Resulting assets reshield back into the pool.

Each interaction uses a new stealth address. An agent may enter the MASP through many addresses and exit through many others. External observers can see that activity occurred, but cannot link those actions to a persistent identity or reconstruct the behavior of a specific agent.

**Concrete example.** An agent holds 100 ZEC in the pool and wants 100 USDZ. It exits to a fresh stealth address, swaps ZEC for USDZ on Z Trade, and the proceeds reshield back into the pool. An observer can see that a swap occurred from an address, but cannot determine which pool participant initiated it. The next swap happens from a completely different stealth address.

**The agent doesn't manage this.** Stealth address generation, pool entry and exit, and reshielding are handled by the protocol. Agents interact through standard interfaces while wallet hygiene happens automatically at the protocol layer.

---

## Vertically integrated execution environment

Z provides a complete, coordinated stack of flagship applications: Z Trade, Z Lend, USDZ, Z Stake, and more. These form the canonical execution stack — a trusted base layer of primitives designed to work together.

For agents, this solves a real operational problem. Without an integrated application stack, an agent that needs to swap, then lend, then make a payment must navigate several independent protocols built by different teams. Every interaction becomes a potential failure point. On Z, the applications are designed to work together from the start. SKILL.md files, MCP integrations, and documentation are written for coordinated use across applications.

---

## Agent-native tooling

Z ships tooling for the agent frameworks developers already use.

**MCP integrations.** Z provides MCP (Model Context Protocol) packages for each application surface: trading, lending, stablecoin operations, pool management, and wallet interactions. Any MCP-compatible agent can operate on Z. The integrations expose private transfers and DeFi operations as tool calls, wallet state and balances as read-only resources, and pool entry and exit as first-class tools.

**LLM-native documentation.** Z maintains documentation structured for LLM consumption. The four failure modes of standard docs — links to other pages, context defined elsewhere, placeholder values, prose where a spec should be — are eliminated. Every walkthrough is self-contained, follows the same deterministic format, uses real executable values (no placeholder addresses), and places contract references inline where they are used.

**SKILL.md files.** Structured agent walkthroughs that provide objectives, prerequisites, step-by-step instructions, and verification. Designed for agents to parse, extract parameters, and execute — not for human reading.

**Framework integrations.** Z supports integration with GOAT SDK (widest chain coverage), ElizaOS (largest open-source agent framework), OpenAI Agents SDK (native MCP support), Coinbase AgentKit, and LangChain.

**Vibecode your own DeFi.** Pre-built templates with pre-audited contracts, deployment scripts, and basic security checks. Agents can deploy custom DeFi applications from a description.

---

## Simulation and preflight

Agents managing real capital need to test strategies before deploying them. Z provides a deterministic simulation environment that goes beyond standard testnets.

- **Local agent sandbox.** Replays mainnet state against controlled scenarios. Developers define scenarios as config files: initial state, price feeds, agent actions, failure injections. The sandbox runs them deterministically, so test failures are reproducible.
- **Pre-built scenarios.** Price swings, liquidation cascades, adversarial competing agents, gas spikes, policy violations.
- **Custom scenario authoring.** Developers define their own scenarios for specific edge cases.
- **Backtesting.** Replay a strategy against historical mainnet state.
- **Multi-agent simulation.** Full environment with competing agents and shared liquidity.

For the full simulation reference, see [Simulation & Preflight](/docs/for-agents/simulation-preflight).

---

## Protocol-level permissions

Agents need constrained autonomy. Full access to a private key is dangerous — one prompt injection and the wallet drains. Manual approval for every transaction defeats the purpose of autonomy. The right model is a sandbox: the human sets the boundaries, the agent operates freely within them.

Z's agent wallets are ERC-4337 smart contract wallets (called Shells) with a built-in policy engine:

- **Spending caps.** Per-transaction, daily, and cumulative limits.
- **Contract allowlists.** Whitelisted addresses and function selectors. The agent can only interact with approved contracts through approved functions.
- **Session duration.** Time-bounded keys that expire automatically.
- **Kill switch.** The owner can freeze the wallet at any time. Freeze is consensus-enforced: validators reject transactions from a frozen wallet at the mempool level.

Shells support nesting. A parent wallet deploys child wallets with inherited but strictly narrower permissions. Permissions cascade downward only — a child wallet can never exceed its parent's bounds. Revoking a parent cascades to all children. Hierarchy membership is committed as a Merkle root on-chain, proved by ZK proof without revealing the full tree structure.

For the full permissions reference, see [Permissions & Session Keys](/docs/for-agents/permissions-session-keys).

---

## The gap

No platform today combines EVM compatibility, native transaction privacy, agent-native tooling, vertically integrated DeFi, and protocol-level permissions in one system.

Every layer described above was designed with agents as first-class participants. Agents generate more transactions, more metadata, and more attack surface than any human user. Z is built for that reality.
