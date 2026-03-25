---
title: "FAQ"
sidebarTitle: "FAQs"
keywords: ['FAQ', 'frequently asked questions', 'Z Protocol', 'privacy', 'ZEC', 'staking', 'USDZ', 'Z token', 'EVM', 'ZSP', 'agents']
ai_summary: "Frequently asked questions about Z Protocol. General: what is Z, relation to Zcash (powered by, not L2), relation to Core (ZSP inspired by Satoshi Plus), architecture (independent protocol with own consensus/validator set/token). Privacy: not everything private (in-pool = shielded, DeFi via stealth = unlinkable), not privacy by default (opt-in), compliance via viewing keys + Privacy Pools, ZK proofs not encryption, difference from Monero (ZK-SNARKs vs ring signatures), difference from Aztec (EVM not Noir, not L2, enshrined DeFi). ZEC: use without Z token (yes, gas payable in ZEC), earn yield multiple ways, self-custodial staking keeps ZEC in wallet, miners participate with no changes. USDZ: 1:1 USDC/USDT backed, yUSDZ is yield-bearing version. Z token: gas + staking + governance + value accrual, 1B fixed supply. Technical: EVM compatible, ZSP consensus, SPV bridge."
---

# FAQ

Common questions about Z Protocol, organized by topic.

---

## General

**What is Z?**

Z is the privacy platform for human and agentic on-chain action. It delivers a vertically integrated stack of privacy-focused applications: private lending, borrowing, trading, stablecoins, yield, and agent infrastructure. See [What is Z](/docs/introduction/what-is-z).

**How is Z related to Zcash?**

Z is powered by Zcash's privacy tech and mining infrastructure through ZSP consensus. Zcash provides the cryptographic foundation and security; Z provides the applications and DeFi stack.

**How is Z related to Core?**

Z's consensus mechanism (ZSP) is inspired by Core's Satoshi Plus, which has proven the model works with Bitcoin since January 2023. Since Zcash and Bitcoin share common ancestry (UTXO model, CLTV timelocks, OP_RETURN), the architecture mapped well to ZEC.

**How is Z architected?**

Z has its own consensus mechanism (ZSP), its own validator set, and its own token. It is secured by Zcash's hashrate and staked ZEC. It is an independent protocol, not a rollup or layer on top of Zcash.

**Who's behind Z?**

Z is led by Zed, an anonymous founder. Rich Rines (Core contributor) provides strategic input.

---

## Privacy

**Is everything on Z private?**

The privacy level depends on what you're doing. Inside the ShieldedPool, balances and transfers are fully shielded — essentially invisible. When you access the Zcosystem (Z Trade, Z Lend, etc.), you use a fresh stealth address that can't be linked to your identity. The transaction is visible but the actor isn't (unlinkable). See [Privacy Architecture](/docs/core-concepts/privacy-architecture).

**Is Z "privacy by default"?**

No. Privacy is a native option, not forced on every transaction. Users choose when to shield and when to transact publicly. Some operations (in-pool transfers) are fully private. Others (DeFi via stealth addresses) are unlinkable but visible.

**How does Z handle regulatory compliance?**

Through selective disclosure. Viewing keys let account holders share transaction history with auditors or regulators without revealing spending authority. Privacy Pools let users prove their funds are clean without deanonymizing. See [Privacy & Compliance](/docs/compliance/privacy-and-compliance).

**How is Z's privacy different from Monero?**

Zcash (and by extension Z) uses zero-knowledge proofs for cryptographic privacy — a mathematical guarantee. Monero uses ring signatures and steganography, which can degrade with chain analysis. Z also adds a full DeFi stack and compliance tools that Monero lacks.

**How is Z different from Aztec?**

Aztec requires developers to learn Noir (a new programming language). Z is EVM-compatible — Solidity works. Aztec has no AI agent strategy. Aztec is an L2 with bridge dependency. Z has enshrined DeFi and a stablecoin system.

---

## ZEC and Staking

**Can I use Z without buying the Z token?**

Yes. Gas can be paid in ZEC. All core applications (lending, borrowing, trading, staking, privacy) work with ZEC only. The Z token unlocks governance and enhanced yield tiers, but it is optional.

**How do I earn yield on ZEC?**

Multiple ways:
1. Timelock ZEC on the Zcash chain to earn Z token rewards (self-custodial, no bridge)
2. Bridge ZEC to Z and stake into stZEC for liquid staking yield (post-launch)
3. Use stZEC as collateral on Z Lend to borrow USDZ

See [ZEC Staking Overview](/docs/staking/zec-staking-overview).

**Does my ZEC leave my wallet with self-custodial staking?**

No. Your ZEC stays on the Zcash chain, locked by a timelock transaction you control. No bridge. No wrapping. No custodian.

**Can ZEC miners participate?**

Yes. Miners include delegation metadata in their mined blocks to vote for Z validators. No new hardware, no new energy, no changes to mining operations. They earn Z token rewards as supplemental income.

---

## USDZ

**What backs USDZ?**

USDC and USDT, 1:1. Every USDZ is fully backed by real stablecoins held in reserve on the source chain.

**What is yUSDZ?**

The yield-bearing version of USDZ. Deposit USDZ, receive yUSDZ, earn yield automatically. yUSDZ uses a non-rebasing share price model — your token balance stays constant and the value per token grows. See [yUSDZ](/docs/products/yusdz).

**Can USDZ lose its peg?**

USDZ is designed with circuit breakers: dynamic fees if redemption velocity spikes, and rate limiting on large redemptions. Full 1:1 backing means every USDZ has real dollars behind it.

---

## Z Token

**What is the Z token for?**

Gas payments, staking (one of ZSP's three consensus legs), governance, and value capture. Revenue from the Zcosystem accrues to Z token holders. Distribution details are to be determined. See [The Z Token](/docs/core-concepts/the-z-token).

**What's the token supply?**

1 billion, fixed.

---

## Technical

**Is Z EVM-compatible?**

Yes. Solidity smart contracts work on Z. Any Ethereum developer can build on Z without learning a new language. See [EVM Compatibility](/docs/for-developers/evm-compatibility).

**What consensus does Z use?**

Zcash Satoshi Plus (ZSP): a hybrid mechanism combining ZEC mining delegation, self-custodial ZEC staking, and Z token staking. Adapted from Core's Satoshi Plus. See [Zcash Satoshi Plus](/docs/core-concepts/zcash-satoshi-plus).

**How does the ZEC bridge work?**

SPV-verified deposits. Your ZEC deposit on the Zcash chain is verified cryptographically — not by a custodian — and corresponding ZEC notes are minted directly into Z's ShieldedPool. See [Z Bridge](/docs/products/z-bridge).

---

## Zcosystem

**Why are ZApps built in-house instead of funded externally?**

Privacy integration requires protocol-level access to the ShieldedPool that external teams cannot achieve with the same depth. Vertical integration also means all revenue flows to the protocol rather than leaking to third-party tokens. AI has collapsed the cost of shipping quality software, making a small team building the full stack practical. See [The Zcosystem](/docs/core-concepts/the-zcosystem).

**What about AI agents?**

Z is designed as infrastructure for AI agents. Agents need privacy more than humans — thousands of transactions create massive metadata surfaces. Z provides agent wallets (Shells), protocol-level wallet hygiene via MASP + stealth addresses, MCP integrations with major frameworks, and simulation environments. See [Z for AI Agents](/docs/for-agents/z-for-ai-agents).
