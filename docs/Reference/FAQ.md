# FAQ

Common questions about Z Protocol, organized by topic.

---

## General

**What is Z?**
Z is the privacy platform for human and agentic on-chain action. It provides privacy applications including lending, borrowing, trading, stablecoins, yield infrastructure, and agent tools.

**How is Z related to Zcash?**
Z is powered by Zcash's privacy technology and mining infrastructure through [[Zcash Satoshi Plus (ZSP)|ZSP]] consensus.

**How is Z related to Core?**
Z's consensus (ZSP) draws inspiration from Core's Satoshi Plus model, operational since January 2023. Both UTXO and timelock features made this architecture compatible with ZEC.

**How is Z architected?**
Z is an independent protocol with its own consensus mechanism (ZSP), validator set, and token. Security derives from Zcash's hashrate and staked ZEC. Z is not a rollup or layer.

**Who is behind Z?**
Z is led by Zed, an anonymous founder. Rich Rines (Core contributor) provides strategic input.

---

## Privacy

**Is everything on Z private?**
Privacy varies by use case. Within the [[ShieldedPool (MASP)|ShieldedPool]], balances and transfers are fully shielded. DeFi operations use stealth addresses, which are visible but unlinkable to identity.

**Is Z "privacy by default"?**
No. Users choose when to shield transactions. Some operations are fully private; others are unlinkable but visible.

**How does Z handle regulatory compliance?**
Through selective disclosure. Viewing keys let account holders share transaction history with auditors or regulators. Privacy Pools enable users to prove fund legitimacy without revealing identity.

**How is Z's privacy different from Monero?**
Zcash (and by extension Z) uses zero-knowledge proofs for cryptographic privacy, providing a mathematical guarantee. Monero relies on ring signatures and steganography, which can degrade under chain analysis.

**How is Z different from Aztec?**
Aztec requires developers to learn Noir (a new programming language). Z is EVM-compatible, so Solidity works. Z includes enshrined DeFi and a stablecoin system. Aztec is an L2 with bridge dependency.

---

## ZEC and Staking

**Can I use Z without buying the Z token?**
Z is designed so that ZEC holders can access core applications without acquiring the Z token. Gas payments will accept ZEC via a Paymaster contract. The [[The Z Token|Z token]] grants governance and yield enhancements through [[Dual Staking]].

**How do I earn yield on ZEC?**
Three methods:
1. Timelock ZEC on Zcash for Z token rewards ([[ZEC Staking]])
2. Bridge ZEC and stake for stZEC ([[Liquid Staking]])
3. Use stZEC as collateral on [[Z Lend]]

**Does my ZEC leave my wallet with self-custodial staking?**
No. ZEC remains on Zcash, locked via timelock transactions you control. No bridge, wrapping, or custodian.

**Can ZEC miners participate?**
Yes. Miners include delegation metadata in blocks to vote for validators, earning Z token rewards without hardware changes.

---

## USDZ

**What backs USDZ?**
Stablecoin reserves, 1:1. Every [[USDZ]] is fully backed by real stablecoins held in reserve.

**What is yUSDZ?**
The yield-bearing version of USDZ. [[yUSDZ]] uses a non-rebasing share price model where token balance remains constant while per-token value increases.

**Can USDZ lose its peg?**
USDZ is backed 1:1 by stablecoin reserves. Every USDZ in circulation has corresponding stablecoins locked in source chain vaults.

---

## Z Token

**What is the Z token for?**
Gas payments, staking (one of ZSP's three consensus legs), governance, and value capture. Revenue from the [[The Zcosystem|Zcosystem]] accrues to Z token holders.

**What is the token supply?**
1 billion, fixed.

---

## Technical

**Is Z EVM-compatible?**
Yes. Solidity smart contracts work on Z.

**What consensus does Z use?**
[[Zcash Satoshi Plus (ZSP)]]: a hybrid mechanism combining ZEC mining delegation, self-custodial ZEC staking, and Z token staking.

**How does the ZEC bridge work?**
ZEC enters Z via the [[Z Bridge]]. The goal is trust-minimized deposits directly into the ShieldedPool. Implementation details are in development.

---

## Zcosystem

**Why are ZApps built in-house instead of funded externally?**
Privacy integration requires protocol-level access to the [[ShieldedPool (MASP)|ShieldedPool]] that external teams cannot achieve with the same depth. Vertical integration ensures revenue benefits the protocol. AI has reduced development costs for quality software.

**What about AI agents?**
Z is designed as infrastructure for AI agents. Z provides agent wallets (Shells), protocol-level hygiene via the MASP and stealth addresses, MCP integrations, and simulation environments.
