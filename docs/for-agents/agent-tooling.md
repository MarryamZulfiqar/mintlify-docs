---
title: Agent Tooling
keywords: ['agent tooling', 'MCP', 'LLM documentation', 'GOAT SDK', 'ElizaOS', 'OpenAI Agents SDK',' Coinbase AgentKit', 'LangChain', 'framework integrations']
ai_summary: "Z ships five agent tooling layers. (1) MCP integrations: one MCP server per ZApp + protocol-level MCP. Expose DeFi operations as tool calls, wallet state as resources, pool entry/exit as first-class tools. Install via MCP client config. (2) LLM-native docs: self-contained pages (no mid-flow links), deterministic structure (objective/prerequisites/steps/verification), real executable values (no placeholder addresses), inline references. (3) SKILL.md files: structured walkthroughs designed for LLM consumption. (4) Framework integrations: GOAT SDK (widest chain coverage), ElizaOS (largest open-source agent framework in crypto), OpenAI Agents SDK (native MCP support = near-zero integration cost), Coinbase AgentKit (custom wallet provider needed), LangChain (Python ecosystem). (5) Vibecode templates: pre-audited contracts, deployment scripts, security checks."
---

# Agent Tooling

Z ships tooling for the agent frameworks developers already use, rather than requiring developers to learn a new system.

---

## MCP integrations

Z provides Model Context Protocol (MCP) packages for each application surface: trading, lending, stablecoin operations, pool management, and wallet interactions. Any MCP-compatible agent — Claude, GPT, local models via MCP clients — can operate on Z without framework-specific plugins.

**What the MCP integrations expose:**

- Private transfers and DeFi operations as tool calls
- Wallet state, balances, and pending requests as read-only resources
- Pool entry and exit operations as first-class tools

**Installation (example for Claude Code):**

```json
{
  "mcpServers": {
    "z-protocol": { "command": "npx", "args": ["-y", "@z-protocol/mcp-server"] },
    "z-trade":    { "command": "npx", "args": ["-y", "@z-protocol/mcp-trade"] },
    "z-lend":     { "command": "npx", "args": ["-y", "@z-protocol/mcp-lend"] }
  }
}
```

> **⚠ Engineering input required:** Exact MCP package names, version tags, and installation commands must be confirmed with the engineering team before publishing. The package structure above is illustrative.

---

## LLM-native documentation

Z maintains documentation structured for LLM consumption. Standard documentation fails agents in predictable ways: links to other pages, context defined elsewhere, placeholder values, prose where a spec should be. Every cross-page dependency is a chance for the agent to hallucinate the missing piece.

Z's agent documentation eliminates these failure modes:

**Self-contained pages.** Each walkthrough is a single document. No mid-flow links. No "see also" references. Everything an agent needs to complete a task lives on one page.

**Deterministic structure.** Every walkthrough follows the same format: objective, prerequisites, step-by-step instructions, verification. An agent that has parsed one Z walkthrough can predict the shape of every other one.

**Real executable values.** All protocol-side values are real and complete: contract addresses, network parameters, and default configurations. No pseudocode. No placeholder values.

**Inline references.** Contract ABIs, deployment configs, and network parameters appear where they are used.

---

## SKILL.md files

SKILL.md files are structured agent walkthroughs designed for LLM consumption. Each file covers one operation end-to-end: objective, prerequisites, step-by-step instructions with inline references, and verification.

The four properties every Z SKILL.md must have:
1. **Self-contained** — no external references required
2. **Deterministic structure** — same format across every file
3. **Real executable values** — actual contract addresses and parameters
4. **Inline references** — ABIs and addresses appear where they are used

> **⚠ Engineering input required:** The location of Z's own SKILL.md files, how to access them programmatically, and a working template require input from the engineering team.

---

## Framework integrations

Rather than building a new agent framework, Z provides plugins and adapters for existing ones.

**GOAT SDK.** GOAT is a tool catalog that supports many chains. Z tools for GOAT (private swap, private transfer, Shell management) make Z available inside any GOAT-compatible framework. GOAT's model-agnostic design means these tools work with LangChain, Vercel AI SDK, OpenAI Agents SDK, and others.

**ElizaOS.** ElizaOS is the most popular open-source agent framework in crypto. A Z ElizaOS plugin exposes Shell management, private DeFi, and payments as ElizaOS actions. Agents can operate on Z with a single plugin install.

**OpenAI Agents SDK.** OpenAI's Agents SDK has native MCP integration, meaning the Z MCP server works inside OpenAI agents with zero adapter code. MCP integration collapses the integration path: ship the MCP server, and OpenAI Agents SDK support comes free.

**Coinbase AgentKit.** AgentKit uses a wallet-agnostic architecture. A Z wallet provider bridges AgentKit's expected interface to Z's privacy routing. This is a lightweight adapter, not a one-line config change — Z's ShieldedPool routing differs from standard EVM transaction patterns.

**LangChain.** Z tools for LangChain expose Shell management, payments, and DeFi to the Python agent ecosystem, enabling agents built with LangGraph, CrewAI, and other Python frameworks.

---

## Vibecode your own DeFi

Z ships pre-built templates with pre-audited contracts, deployment scripts, and basic security checks. Non-technical users can describe what they want to build and let their agent handle the rest — a private lending pool, a payment vault, a DCA strategy, whatever they can describe.

The goal is bringing the shipping cost for builders on Z to near zero.

---

## Agent templates

Z plans to ship ready-to-deploy agent templates covering common on-chain agent use cases. Templates demonstrate Shell management, private swaps, lending, payments, and agent hierarchies.

> **⚠ Engineering input required:** Final list of agent templates, their names, and their availability at launch require confirmation from the product team.
