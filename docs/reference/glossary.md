---
title: "Glossary"
sidebarTitle: "Glossary"
keywords: ['glossary', 'definitions', 'terminology', 'MASP', 'ZSP', 'Groth16', 'BN254', 'stealth address', 'viewing key', 'nullifier', 'ZEC', 'USDZ', 'stZEC', 'RelayAdapter', 'broadcaster', 'ZApp', 'Zcosystem', 'shielded', 'unlinkable']
ai_summary: "Canonical Z Protocol terminology. Protocol: Z (protocol + token when clear), Z Protocol (formal), Zcosystem (application layer), ZApp (individual app), Zed (anon founder). Privacy: MASP = ShieldedPool = UTXO note system with Poseidon commitments; note = private UTXO; commitment = Poseidon hash of note; nullifier = Poseidon(nsk, leafIndex) published on spend; stealth address = one-time ERC-5564 ECDH address; viewing key = read-only key; spending key = authorizes spending; shielded = inside MASP ONLY; unlinkable = DeFi via stealth address; private = general acceptable term. Consensus: ZSP, validator, DPoW (Leg 1), self-custodial staking (Leg 2), DPoS (Leg 3), timelock (CLTV — ZEC stays in wallet), hybrid score, round (24h), Dual Staking, jailing. Assets: Z token (1B fixed), ZEC, USDZ, yUSDZ, stZEC, stZ, wZEC. Technical: Groth16 (ZK on BN254 — NOT Halo 2), BN254, Halo 2 (Zcash L1 system — DIFFERENT), Poseidon, Merkle tree, ERC-4337, ERC-5564, Paymaster, RelayAdapter, Broadcaster, Relayer, SPV, Equihash (0x0B), Blake2b (0x0C). Agents: MCP, SKILL.md, KYA. Bridge: SPV Bridge, LayerZero, FROST, Direct-to-MASP."
---

# Glossary

Terms and definitions used across Z Protocol documentation. When terminology is in doubt, this page is the source of truth.

---

## Protocol

| Term | Definition |
|------|-----------|
| **Z** | The protocol. Privacy platform for human and agentic on-chain action. EVM-compatible. Also refers to the native gas and governance token when context is clear. |
| **Z Protocol** | Formal name for Z. Used when disambiguation from the token is needed. |
| **Zcosystem** | Z's vertically integrated application layer. Includes Z Trade, Z Lend, Z Stake, USDZ, yUSDZ, and future applications. |
| **ZApp** | An application in the Zcosystem (e.g., Z Trade, Z Lend). |
| **Zed** | Anon founder persona behind Z Protocol. |

---

## Privacy

| Term | Definition |
|------|-----------|
| **MASP** | Multi-Asset Shielded Pool. A UTXO-based note system where assets are stored as cryptographic commitments in a Merkle tree. Balances are private. Transfers are verified by ZK proofs. Also called the ShieldedPool. |
| **ShieldedPool** | Same as MASP. The private asset pool where balances and transfers are shielded by zero-knowledge proofs. |
| **Note** | A private balance entry inside the MASP. Contains an amount, asset type, and ownership information, all hidden behind a cryptographic commitment. The UTXO unit of the MASP. |
| **Commitment** | A Poseidon hash that represents a note in the MASP's Merkle tree. Formula: `Poseidon(Poseidon(npk, tokenHash, value), random, 0)`. Reveals nothing about the note's contents. |
| **Nullifier** | A unique value derived from a note that is published when the note is spent. Formula: `Poseidon(nsk, leafIndex)`. Prevents double-spending without revealing which commitment was spent. |
| **Stealth address** | A one-time address generated via ECDH key exchange (ERC-5564 standard). Used for DeFi interactions so that no observer can link the activity back to the user's identity or MASP balance. Generated fresh for each interaction. |
| **Viewing key** | A cryptographic key derived from the master key that allows a third party to see a user's MASP balances and transaction history without the ability to spend. Derived: `Poseidon(masterKey, 1)`. |
| **Spending key** | The private key that authorizes spending from the MASP. Only the owner holds this. Derived: `Poseidon(masterKey, 0)`. |
| **Shielded** | Specifically describes assets or activity inside the MASP. Do NOT use as a blanket term for all privacy on Z. In-pool balances and transfers are shielded. DeFi interactions via stealth addresses are not shielded — they are unlinkable. |
| **Unlinkable** | Describes DeFi activity from stealth addresses. The activity is on-chain and visible, but no observer can connect it to the user's identity, pool balance, or other transactions. |
| **Private** | General-purpose term for the privacy outcome Z provides. Accurate for both in-pool (shielded) and stealth address (unlinkable) contexts. |

---

## Consensus

| Term | Definition |
|------|-----------|
| **ZSP** | Zcash Satoshi Plus. Z's consensus mechanism where Zcash miners, ZEC holders, and Z token stakers elect validators. |
| **Satoshi Plus** | The consensus model originated by Core DAO. ZSP adapts it for Zcash's cryptographic primitives. |
| **Validator** | A node operator elected to the active set through ZSP. Validators produce blocks and earn consensus rewards. |
| **Delegated Proof of Work (DPoW)** | Leg 1 of ZSP. Zcash miners embed delegation metadata in mined blocks to vote for validators. |
| **Self-custodial staking** | Leg 2 of ZSP. ZEC holders timelock ZEC on the Zcash chain to vote for validators. ZEC never leaves the holder's wallet. |
| **Delegated Proof of Stake (DPoS)** | Leg 3 of ZSP. Z token holders stake tokens on Z to vote for validators. |
| **Timelock** | A CLTV (CheckLockTimeVerify) output that locks ZEC for a chosen duration. Used for self-custodial staking. Not a transaction in the sense of sending ZEC anywhere — the ZEC stays in the holder's wallet. |
| **Hybrid score** | The combined delegation weight from all three ZSP legs that determines which validators are elected to the active set. Normalized with hardcaps per leg to prevent domination. |
| **Round** | A 24-hour cycle in ZSP. At the end of each round, validators are elected and rewards are distributed. |
| **Dual Staking** | Staking both ZEC (self-custodial) and Z tokens simultaneously to earn boosted consensus rewards. Only self-custodial ZEC qualifies — stZEC does not. |
| **Jailing** | Exclusion of a validator from the active set due to downtime or misbehavior. Jailed validators stop earning rewards until they unjail themselves. |

---

## Assets and Tokens

| Term | Definition |
|------|-----------|
| **Z token** | The governance and value-capture token for Z Protocol. Fixed supply of 1 billion. Used for gas, staking (Leg 3 of ZSP), governance, and value accrual from Zcosystem revenue. |
| **ZEC** | The native currency of the Zcash blockchain. Works as gas on Z (via Paymaster), participates in ZSP consensus (Legs 1 and 2), and is the primary asset across the Zcosystem. |
| **USDZ** | Privacy-preserving stablecoin backed 1:1 by USDC and USDT. |
| **yUSDZ** | Yield-bearing version of USDZ. An ERC-4626 vault where the exchange rate appreciates as yield accrues. Non-rebasing. |
| **stZEC** | Liquid staking token representing staked ZEC on Z. Appreciates as consensus rewards accrue. Post-launch. |
| **stZ** | Liquid staking token representing staked Z tokens. Appreciates as consensus rewards accrue. |
| **wZEC** | Wrapped ZEC on Z. The ERC-20 representation of ZEC used in smart contract interactions on the EVM layer. |

---

## Technical

| Term | Definition |
|------|-----------|
| **Groth16** | The zero-knowledge proof system used by Z for MASP operations. Runs on the BN254 elliptic curve. Requires a trusted setup ceremony. Produces constant-size proofs (~200 bytes). Verification costs ~250,000 gas. |
| **BN254** | The elliptic curve (also called alt_bn128) used for Z's ZK proof verification. Standard in EVM ecosystems. The EVM's `ecPairing` precompile operates on BN254. |
| **Halo 2** | The zero-knowledge proof system used by Zcash L1. Trustless (no setup ceremony). Uses Pallas/Vesta curves. Different from Z's Groth16 on BN254. Do not use Halo 2 tooling for Z operations. |
| **Poseidon** | A hash function optimized for ZK circuits. Operates natively over the BN254 scalar field. Used for commitments, nullifiers, and key derivation in the MASP. More efficient than keccak256 inside ZK proofs on BN254. |
| **Merkle tree** | The data structure that stores all note commitments in the MASP. Depth: 16 levels (65,536 leaves per tree). Parent nodes: `Poseidon(left_child, right_child)`. |
| **ERC-4337** | Account abstraction standard. Z uses it for Shells (agent wallets with policy enforcement) and Paymaster contracts that accept ZEC for gas. |
| **ERC-5564** | Stealth address standard. Defines how one-time addresses are generated via ECDH key exchange and discovered by recipients. |
| **Paymaster** | An ERC-4337 contract that accepts ZEC for gas, swaps it for the Z token, and pays the EVM gas fee. Users never interact with the conversion directly. |
| **RelayAdapter** | The contract pattern that enables private DeFi on Z. Atomically unshields assets from the MASP, executes a DeFi interaction (swap, lend, borrow), and reshields the output — all in one transaction. |
| **Broadcaster** | A permissionless participant who submits MASP transactions on behalf of users, abstracting the sender's address from the transaction. Compensated via a fee from the user's shielded note. |
| **Relayer** | A permissionless participant who submits Zcash block headers and staking transactions to Z's on-chain verification contracts (ZcashLightClient, ZcashStake). Different from a Broadcaster. |
| **SPV** | Simplified Payment Verification. The bridge architecture Z uses for ZEC. Verifies transactions cryptographically (Merkle proof against a verified block header) without trusting a custodian. |
| **Equihash** | The memory-hard proof-of-work algorithm used by Zcash. Z includes an Equihash precompile at address 0x0B for on-chain verification of Zcash block headers. |
| **Blake2b** | Hash function used by Zcash for Merkle roots and transaction IDs (ZIP-244). Z includes a Blake2b precompile at address 0x0C. |

---

## Agent

| Term | Definition |
|------|-----------|
| **MCP** | Model Context Protocol. The open standard for connecting AI agents to external tools. Z ships one MCP server per ZApp and a protocol-level MCP for cross-app operations. Compatible with Claude, GPT, and any MCP-compatible client. |
| **SKILL.md** | A structured documentation file designed for LLM and agent consumption. Contains walkthroughs, ABIs, addresses, and code examples. Self-contained — no external references needed to complete the described operation. |
| **KYA** | Know Your Agent. A framework for verifying agent identity using W3C Verifiable Credentials and DIDs, implemented as ZK proofs so owner identity is not revealed. |
| **Shell** | Z's term for an ERC-4337 smart contract agent wallet. Shells have owner-controlled policy enforcement: spending caps, contract allowlists, session keys, and a consensus-enforced kill switch. Shells support hierarchical nesting. |

---

## Bridge

| Term | Definition |
|------|-----------|
| **SPV Bridge** | The trust-minimized bridge for ZEC. Uses cryptographic verification (Merkle proofs against verified Zcash block headers) instead of a custodian. ZEC arrives in the MASP directly as private notes. |
| **LayerZero** | The cross-chain messaging protocol used for non-ZEC assets (USDC, USDT, and other ERC-20 tokens). Uses an oracle and relayer model. |
| **FROST** | Flexible Round-Optimized Schnorr Threshold signatures. The planned mechanism for ZEC withdrawals from Z — Z's validator set participates as threshold signers in a t-of-n scheme. Not available at launch. |
| **Direct-to-MASP deposit** | A bridge deposit where ZEC or stablecoins enter the MASP directly as private notes. No public address on Z receives the assets. The user submits a Poseidon commitment with the deposit. |
