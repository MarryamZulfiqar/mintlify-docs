---
title: "Designed for Agents"
slug: designed-for-agents
description: "Z provides the scaffolding for AI agents operating on-chain — EVM compatibility, protocol-level wallet hygiene, vertically integrated DeFi, agent-native tooling, simulation, and protocol-enforced permissions."
keywords: [AI agents, scaffolding, EVM, wallet hygiene, MASP, stealth addresses, MCP, SKILL.md, session keys, spending caps, agent permissions, simulation, preflight, on-chain agents]
ai_summary: |
  Z provides five layers of scaffolding for on-chain AI agents. (1) EVM compatibility: Solidity works, existing tooling carries over, no new language required — the single largest friction reducer versus competing privacy platforms. (2) Protocol-level wallet hygiene: agents enter the MASP and exit to fresh stealth addresses per interaction, generated via ECDH. No observer can link activity back to a persistent agent identity. This is automatic — agents don't manage it. (3) Vertically integrated execution environment: one trusted DeFi stack (Z Trade, Z Lend, USDZ, etc.) with shared documentation and interface patterns. Agents don't discover or evaluate third-party protocols. (4) Agent-native tooling: one MCP server per ZApp, SKILL.md walkthroughs with inline ABIs and real contract addresses, copy-paste-ready code, llms.txt, vibecode templates with pre-audited contracts. (5) Protocol-level permissions: ERC-4337 smart account wallets with spending caps (per-tx, daily, cumulative), contract allowlists, time-bounded session keys, consensus-enforced kill switches. Hierarchical permissions via parent/child wallets with Merkle-committed tree structure, ZK membership proofs.
---

# Designed for Agents

The difference between an AI agent that produces responses and one that takes actions in an environment is scaffolding — the structured system built around the agent that guides how it operates in a specific domain.

Claude Code is a clear example. Raw Claude is powerful, but without access to a development environment it is limited to text. Claude Code wraps the model in scaffolding: file system access, terminal execution, project context, structured tools. The model can now read files, modify code, run commands, and observe the results. The model itself does not change. What changes is its ability to act within an environment.

Additional layers build on top of that foundation. Projects like Compound Engineering from EveryInc add structured workflows for planning, spawning sub-agents, coordinating work, and executing engineering tasks reliably.

The same pattern applies anywhere agents are expected to take real actions. Blockchains are no different.

Point a raw agent at a blockchain, and the results are chaotic. It guesses which protocols to use, misreads documentation, calls the wrong contracts, and constructs transactions that fail. It routes assets inefficiently and retries actions that should have been simulated first. It reuses the same address across interactions, accumulating a public transaction history that exposes strategy, relationships, and budgets through metadata that compounds over time.

**Agents need scaffolding to interact reliably with blockchain systems.**

Z provides that scaffolding. It creates a structured environment where agents can plan, execute, and coordinate economic actions on blockchain rails with far greater reliability than a raw EVM chain.

In sum:
- Claude Code provides scaffolding for agents to act within development environments.
- Compound Engineering provides scaffolding for agents to coordinate complex engineering work.
- **Z provides scaffolding for agents to build and operate their own on-chain economies.**

---

## EVM Compatibility

Z provides an EVM-compatible execution environment for Solidity smart contracts. Existing contracts, development frameworks, testing tools, libraries, and developer workflows carry over with little or no modification. Agents that generate, deploy, or interact with Solidity contracts can operate on Z using the same assumptions they use on other EVM networks.

Solidity has the largest developer base, the deepest tooling ecosystem, and the most audited libraries of any smart contract language. Many agent frameworks target EVM ecosystems first. Agents that already know how to interact with EVM contracts can interact with contracts on Z using the same contract ABIs, transaction encoding, and JSON-RPC interfaces.

For agent framework developers, integrating Z typically looks like adding another EVM network rather than supporting a new execution model.

Competing privacy platforms require new languages — Noir, Cairo, Leo — with tiny developer pools and immature tooling. Z's EVM compatibility is the single largest friction reducer for adoption.

---

## Protocol-Level Wallet Hygiene

An agent using a single wallet on a transparent ledger quickly leaves a record tied to one identity. Anyone watching the chain can link activity to its operator, map counterparties, infer strategies, and fingerprint behavior.

Z's privacy architecture prevents this by design.

**How it works in practice.** An agent holds assets in the MASP. When it needs to interact with DeFi, funds exit the pool to a fresh stealth address generated through ECDH key exchange. That address has no on-chain link to the agent's pool balance or any previously used address. The DeFi interaction happens from that address, and the resulting assets reshield back into the pool.

Each interaction uses a new stealth address. An agent may enter the MASP through one set of addresses and exit through many others. External observers can see that activity occurred, but they cannot link those actions to a persistent identity or reconstruct the behavior of a specific agent.

**Concrete example.** An agent brings 100 ZEC into the pool and wants 100 USDZ. It exits to a fresh stealth address, swaps ZEC for USDZ on Z Trade, and the proceeds re-enter the pool. An observer can see that a swap occurred from an address, but cannot determine which participant in the pool initiated it. The next swap happens from a completely different stealth address.

**The agent does not manage this.** Stealth address generation, pool entry and exit, and reshielding are handled by the protocol. Agents interact through standard interfaces while wallet hygiene happens automatically at the protocol layer.

Many operations can occur entirely inside the pool without surfacing to the DeFi layer. For example, a ZEC transfer executed within the MASP does not reveal the participants, the amounts, or even that the transfer occurred. This creates a two-tier execution environment: interactions outside the pool record that activity occurred but not who initiated it, while in-pool interactions remain comprehensively shielded.

---

## Vertically Integrated Execution Environment

Z provides a complete, coordinated stack of flagship applications including Z Trade, Z Lend, USDZ, and more. These form the canonical execution stack on Z — a trusted base layer of primitives designed to work together out of the box.

For agents, this solves a real operational problem. Without an integrated application stack, an agent that needs to swap, then lend, then make a payment must navigate several independent protocols built by different teams with different objectives. Every interaction becomes a potential failure point, forcing the agent to make assumptions about how protocols behave rather than operating within scaffolding designed for coordinated execution.

On Z, the applications are designed to work together from the start. SKILL.md files, MCP integrations, and developer guidance are written for coordinated use across applications. Rather than guessing how separate protocols behave, agents operate within scaffolding designed for reliable execution across the ecosystem.

---

## Agent-Native Tooling

Z ships tooling for the agent frameworks developers already use, rather than requiring developers to learn a new system.

### MCP Integrations

Z provides MCP (Model Context Protocol) packages for each application surface: trading, lending, stablecoin operations, pool management, and wallet interactions. Developers install the ones their agent needs and add them to their MCP server config. Any MCP-compatible agent can operate on Z without framework-specific plugins.

Examples of what the integrations surface:
- Private transfers and DeFi operations as tool calls
- Wallet state, balances, and pending requests as read-only resources
- Pool entry and exit operations as first-class tools

### LLM-Native Documentation

Z maintains two documentation sets: one for agents, one for humans.

Human docs explain concepts, walk through decisions, and provide context. Agent docs skip all of that. They are structured for consumption by LLMs that will parse them, extract parameters, and execute.

Most documentation fails agents in predictable ways: links to other pages, references to context defined elsewhere, placeholder values, prose where a spec should be. These are failure modes. Every cross-page dependency is a chance for the agent to hallucinate the missing piece. Every placeholder value is a chance to fabricate an address or submit a malformed transaction.

Z's agent docs are built to eliminate these failure modes:

- **Self-contained pages.** Each walkthrough is a single document. No mid-flow links. No "see also" references. Everything an agent needs to complete a task lives on one page.
- **Deterministic structure.** Every walkthrough follows the same format: objective, prerequisites, step-by-step instructions, deploy command, verification. An agent that has parsed one Z walkthrough can predict the shape of every other one.
- **Executable code blocks.** All protocol-side values are real and complete: contract addresses, ABIs, network parameters, default configs. No pseudocode. No placeholder values.
- **Inline references.** Contract ABIs, deployment configs, and network parameters appear where they are used, not on a separate reference page the agent would need to locate and cross-reference.

### Vibecode Your Own DeFi

Z ships pre-built templates with pre-audited contracts, deployment scripts, and basic security checks. Non-technical users can describe what they want to build and let their agent handle the rest — a private NFT marketplace, an on-chain game, a custom lending pool, whatever they can imagine.

The goal is to bring shipping costs for builders on Z to near zero. If you can describe it, you can deploy it.

---

## Simulation and Preflight

Agents managing real capital need to test strategies before deploying them. Z provides a deterministic simulation environment that goes beyond standard testnets.

**Local agent sandbox.** Replays mainnet state against controlled scenarios. Developers define scenarios as config files: initial state, price feeds, agent actions, and failure injections. The sandbox runs them deterministically, so test failures are reproducible.

**Pre-built scenarios** ship with the SDK:
- Price swings and volatility spikes
- Liquidation cascades
- Adversarial agents competing for the same opportunities
- Gas spikes and bundler congestion
- Policy violations and session revocations

Developers can author custom scenarios for their specific edge cases. The pre-built scenarios get you started. Custom scenarios are where serious builders test what matters to their strategy.

**Backtesting against mainnet history.** Replay a strategy against historical mainnet state to measure performance across real market conditions. Test how a rebalancing agent would have performed during a volatility spike, or how a lending agent would have handled a liquidation cascade. Concrete numbers before real capital is at risk.

**Multi-agent simulation.** Test how an agent behaves when competing agents are present. Simulate the full environment: multiple agents, shared liquidity, concurrent transactions. An agent that works perfectly in isolation may behave differently under competition.

---

## Protocol-Level Permissions

Agents need constrained autonomy. Full access to a private key is dangerous — one prompt injection and the wallet drains. Manual approval for every transaction defeats the purpose. The right model is a sandbox: the human sets the boundaries, and the agent operates freely within them.

Z's agent wallets are ERC-4337 smart contract wallets with a built-in policy engine. The owner configures constraints at deployment:

- **Spending caps.** Per-transaction, daily, and cumulative limits.
- **Contract allowlists.** Whitelisted addresses and function selectors. The agent can only interact with approved contracts through approved functions.
- **Session duration.** Time-bounded keys that automatically expire. Long-running agents rotate keys on a configurable schedule.
- **Kill switch.** The owner can freeze the wallet at any time. Freeze is consensus-enforced: validators reject transactions from a frozen wallet at the mempool level. The agent cannot circumvent it.

### Hierarchical Permissions

Agent wallets support nesting. A parent wallet deploys child wallets with inherited but strictly narrower permissions.

**Example:** a portfolio management agent with a daily cap of $100K and DeFi protocols allowlisted spawns three executor agents:
- A trading agent ($30K cap, single DEX only)
- A lending agent ($50K cap, lending protocol only)
- A monitoring agent (read-only, no spending authority)

Permissions cascade downward only. A child wallet can never exceed its parent's bounds. Revoking a parent cascades to all its children. The owner sees everything through view keys. If the trading agent is compromised, the owner revokes it without touching the others.

Hierarchy membership is committed as a Merkle root on-chain. Agents prove group membership through a ZK proof without revealing the full tree structure. An adversary observing the chain cannot map out how many agents a principal controls or how the fleet is organized.

---

## The Gap

No platform today combines EVM compatibility, native transaction privacy, agent-native tooling, vertically integrated DeFi, and protocol-level permissions in one system.

Every layer described in this doc was designed with agents as first-class participants. Agents generate more transactions, more metadata, and more attack surface than any human user. Z is built for that reality.

For the full privacy architecture, see [Privacy Architecture](./03-privacy-architecture.md). For how compliance works alongside privacy, see [Compliance](./11-compliance.md).
