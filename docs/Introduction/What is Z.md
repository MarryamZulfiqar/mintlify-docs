# What is Z

Z is the programmable privacy platform for **autonomous applications** and **anonymous activity**.

It is designed to give humans peace of mind by preserving privacy and empowering their agents.

---

## Why Z Exists

**AI collapses the cost of mass surveillance.** Every transaction on a transparent blockchain is permanently recorded and queryable by anyone. AI has made mass analysis of that data cheap enough to run continuously, at scale, on any target. There is no longer a meaningful cost barrier to surveilling on-chain activity.

**AI agents are becoming the primary on-chain actors.** Agents don't operate like humans. They execute continuously, across multi-step workflows, and at scale, requiring embedded scaffolding to operate autonomously, avoid errors, and use tokens efficiently. Perhaps most importantly, at the scale AI agents operate at, they require privacy to avoid compounding into full behavioral traceability, exposing strategies, systems, and operators.

**For humans to have peace of mind, agents need purpose-built rails with embedded scaffolding, autonomous applications, and privacy by default. Z is the platform for them.**

---

## Dual Architecture

Z operates through two interconnected systems:

**[[ShieldedPool (MASP)]].** A Multi-Asset Shielded Pool where assets are stored as private notes. Balances and transfers are cryptographically shielded. No outside observer can see who holds what, how much, or which asset type is present. Transfers within the pool are verified by Groth16 zero-knowledge proofs. This is home base for Z.

**[[The Zcosystem]].** An EVM-compatible application layer where assets exit the ShieldedPool to fresh stealth addresses for app interactions, then reshield. Each interaction uses a different one-time address. An observer can see that activity occurred but cannot link it to a persistent identity.

Together, these two systems create a protocol where users and agents can hold, transfer, trade, lend, borrow, and earn yield while maintaining privacy throughout.

---

## Made for Agents

The difference between a raw AI model and a productive agent is scaffolding: the structured system around the model that guides how it operates in a specific domain. Claude Code wraps a language model in file system access, terminal execution, project context, and structured tools. The model does not change. What changes is its ability to act.

Z provides the same kind of scaffolding for on-chain agents. Three capabilities make this work.

### Tooling

Z ships tooling for the agent frameworks developers already use. MCP integrations for every application surface. SKILL.md walkthroughs structured for LLM consumption: self-contained, deterministic, real executable values. Simulation sandboxes where agents test strategies against replayed mainnet state before deploying real capital. Shells (ERC-4337 smart contract wallets) with spending caps, contract allowlists, session keys, and kill switches so agents operate within boundaries their owners define.

### Privacy

Z's [[Privacy Architecture|privacy architecture]] eliminates the metadata accumulation that plagues agents on transparent ledgers. An agent holds assets in the [[ShieldedPool (MASP)|ShieldedPool]]. When it needs to interact with an app, funds exit the pool to a fresh stealth address with no on-chain connection to prior activity. Each interaction uses a new address.

Zcash pioneered this style of cryptography. Z extends it with multi-asset support and EVM integration. The privacy foundation uses Groth16 zero-knowledge proofs on BN254, the note-and-nullifier model that has been in production since 2016, and Poseidon hashing designed for ZK circuits.

### Coordination

Z provides a full application stack integrated with the ShieldedPool at the protocol level: [[Z Trade]] (DEX with concentrated liquidity), [[Z Lend]] (lending and borrowing), [[USDZ]] (private stablecoin), [[yUSDZ]] (yield-bearing stablecoin), [[Z Stake]] (liquid staking), and more.

This is deliberate. Deep ShieldedPool integration requires coordinated design. The RelayAdapter pattern, which makes private app interactions possible, requires tight coupling between each application and the privacy layer. A single trusted stack gives agents a composable environment they can reliably coordinate across without discovering or evaluating third-party protocols.

Z is EVM-compatible. Solidity contracts deploy without modification. Standard tooling (Hardhat, Foundry, ethers.js) works out of the box.

---

## What Agents (and Humans) Can Do on Z

- **Trade privately** on [[Z Trade]], a concentrated-liquidity DEX with native ShieldedPool integration
- **Lend and borrow** on [[Z Lend]], with private collateral operations
- **Hold private stablecoins** with [[USDZ]], backed 1:1 by stablecoin reserves
- **Earn yield on stablecoins** with [[yUSDZ]], a yield-bearing ERC-4626 vault
- **Stake ZEC** without leaving your wallet via [[ZEC Staking]]
- **Liquid stake** ZEC or Z tokens through [[Z Stake]] for composable receipt tokens (stZEC, stZ)
- **Bridge assets** from Zcash, Ethereum, Arbitrum, and Base via the [[Z Bridge]]

The applications compose. An agent (or human) can move through the full stack in a single session: deposit ZEC into stZEC, deposit it as collateral on Z Lend, borrow USDZ against it, deposit USDZ into yUSDZ for yield, and shield the full position in the ShieldedPool. ZEC price exposure, borrowed stablecoins, yield on those stablecoins, and privacy over the entire position.

---

## Powered by Zcash

Z is powered by Zcash. ZEC serves as gas on Z, staked ZEC secures the network, and ZEC miners contribute hashrate to Z's consensus through [[Zcash Satoshi Plus (ZSP)]]. ZEC is early liquidity across the Zcosystem. Architecturally, Z draws deep inspiration from Zcash's cryptographic legacy and cypherpunk philosophy, building on nearly a decade of privacy research and real-world operation.

---

## Peace of Mind

Crypto has a terror problem.

Full transparency invites coercion. When your portfolio, salary, and every transaction are permanently visible, you become a target. Kidnappings, extortion, and social engineering all start with on-chain data that was never meant to be public.

Clunky interfaces create anxiety. Users second-guess every transaction, worry about sending to wrong addresses, and struggle with fragmented apps that each carry their own risks.

Agents operating without guardrails create real danger. They hallucinate, make costly mistakes, and operate on rails that were never designed for autonomous execution. Delegating capital to an agent on today's infrastructure is a leap of faith.

Fragmented ecosystems multiply risk. Every new protocol is a new due diligence exercise, a new set of assumptions, a new surface area for things to go wrong.

Z addresses each of these:

- **Privacy eliminates exposure.** The ShieldedPool and stealth addresses sever the link between identity and on-chain activity. Your financial life is not public record.
- **Agents as the interface.** Z prioritizes AI agents as the primary way users interact with the protocol. Agents handle the complexity. Users set the boundaries and the goals.
- **Scaffolding reduces agent risk.** Structured tooling, simulation environments, enforced permissions, and protocol-level wallet hygiene make agents more reliable and constrained. The rails are built for them.
- **Vertical integration builds trust.** The [[The Zcosystem|Zcosystem]] is a single coordinated stack. Applications are designed together, documented together, and secured together. One trust decision covers the full surface.

The result is a protocol where humans and agents can operate on-chain without the constant low-grade dread that defines most of crypto today.
