---
title: "Zcash Satoshi Plus (ZSP)"
sidebarTitle: "Zcash Satoshi Plus"
ai_summary: "Technical overview of Z's hybrid consensus mechanism combining three legs — ZEC mining delegation (DPoW), self-custodial ZEC staking (CLTV timelocks), and Z token staking (DPoS) — into a hybrid validator election system with 24-hour rounds and on-chain Zcash verification."
keywords: ["ZSP", "Zcash Satoshi Plus", "consensus", "validators", "ZEC staking", "DPoW", "DPoS", "CLTV", "Equihash", "Blake2b", "relayer", "hybrid consensus"]
---

Z is secured by a set of high-performance validators, elected by three groups:

1. **ZEC miners** delegate their existing hashrate to validators by embedding a vote in the blocks they already mine. No new hardware, no new energy.
2. **ZEC holders** timelock ZEC on the Zcash chain to vote for validators. The ZEC never leaves their wallet.
3. **Z token stakers** stake Z tokens directly on the network to vote for validators.

Each round, the candidates with the most combined support across all three groups are elected to the active validator set. The elected validators produce blocks and secure Z, earning consensus rewards that flow back to themselves and their voters.

This mechanism is called Zcash Satoshi Plus (ZSP). The design is adapted from Core DAO's Satoshi Plus, which has secured Core using delegated BTC and Bitcoin mining hash power since January 2023 with no outages. Zcash and Bitcoin share the same UTXO model, the same timelock primitives (CLTV), and the same OP_RETURN metadata fields, so the architecture maps to Zcash with targeted cryptographic changes rather than a ground-up redesign.

---

## The Three Legs

### Leg 1: ZEC Miners (Delegated Proof of Work)

Zcash miners vote for Z validators by embedding delegation metadata in the blocks they already mine.

**What the miner does:**
- Includes two pieces of data in an OP_RETURN output (80-byte limit) when mining a Zcash block: the Z validator address they are voting for, and the Z address where they want to receive rewards
- No new hardware, no additional energy, no changes to existing mining operations
- For mining pools, the pool operator delegates on behalf of all participants

**How Z reads the votes:**
- A Zcash Light Client contract on Z verifies Zcash block headers on-chain
- Permissionless relayers submit headers in batches
- Each header is validated using Equihash and Blake2b precompiles before being accepted

**Safety mechanisms:**
- 6-block confirmation depth (~7.5 minutes) before miner power is counted, to handle reorgs
- Power calculations are frozen 7 rounds in the past to prevent last-minute manipulation of validator elections

### Leg 2: ZEC Holders (Self-Custodial ZEC Staking)

ZEC holders stake by timelocking their ZEC on the Zcash chain. The ZEC stays in the holder's wallet the entire time. No bridging. No wrapping. No custody transfer.

**What the holder does:**
1. Creates a CLTV (CheckLockTimeVerify) output that locks the ZEC for the chosen duration
2. Includes an OP_RETURN output containing the Z validator address and the holder's Z reward address

**How Z verifies it:**
- A relayer submits the transaction to Z's ZcashStake contract
- The contract verifies inclusion via the Zcash Light Client (Merkle proof against a confirmed header)
- Delegation metadata is parsed from the OP_RETURN payload using ZIP-244 transparent component parsing

**Properties:**
- No custody transfer. The ZEC never leaves the holder's possession.
- No trust assumptions beyond the Zcash protocol itself.
- The only cost is temporary illiquidity.

For the step-by-step staking walkthrough, see [[ZEC Staking]].

### Leg 3: Z Token Stakers (Delegated Proof of Stake)

Z token holders stake directly on Z by delegating tokens to validators through the ZSPStake contract. Standard on-chain staking.

- Staking Z alongside ZEC earns boosted yield through [[Dual Staking]].

---

## Validator Election

Every round, each validator candidate receives a hybrid score calculated from the combined weight of all three delegation sources.

```
For each candidate:
  score = f(mining_delegation, zec_staking_weight, z_token_delegation)
```

The scoring function normalizes each leg against hardcaps that prevent any single source from dominating the election. If a validator accumulates more delegation in one leg than the hardcap allows, the excess does not increase their score.

The candidates with the highest hybrid scores are elected to the active validator set.

### Becoming a Validator Candidate

Validator candidates register through the CandidateHub contract by staking a minimum deposit of Z tokens and providing their consensus key and commission rate. Candidates must maintain a node that meets the network's hardware and uptime requirements.

The commission rate determines what percentage of delegator rewards the validator keeps. Changes take effect in the next round to prevent mid-round manipulation.

---

## Round Lifecycle

A round lasts 24 hours. At the end of each round, the CandidateHub contract orchestrates the transition:

1. **Freeze state.** All delegation data for the current round is finalized.
2. **Filter candidates.** Only active candidates with sufficient minimum delegation qualify.
3. **Calculate hybrid scores.** StakeHub aggregates delegation data from all three agent contracts and computes each candidate's normalized score.
4. **Sort and elect.** Candidates are ranked by hybrid score. The top candidates become the active validator set.
5. **Distribute rewards.** The previous round's block rewards are distributed through StakeHub to delegators proportional to their delegation weight.
6. **Reset flags.** Validator and candidate state is reset for the new round.

This entire sequence executes in the `turnRound` function on CandidateHub. Any account can trigger it once the round interval has elapsed.

---

## Emissions and Rewards

Consensus rewards are paid in Z tokens.

### Reward Flow

1. **ValidatorSet** receives block rewards (minted at genesis and stored in the contract).
2. **StakeHub** receives the round's accumulated rewards and routes them to the three leg-specific agent contracts.
3. Each agent contract distributes to individual delegators proportional to their delegation weight within that leg.

Validators take a configurable commission before delegator distribution. Transaction fees flow through the same pipeline alongside block rewards.

---

## On-Chain Verification Infrastructure

### Zcash Light Client

A system contract that maintains a verified chain of Zcash block headers on Z.
- Relayers submit headers in batches. Each header is validated using the Equihash precompile before being stored.
- 6-block confirmation depth (~7.5 minutes). Headers with fewer confirmations are not yet trusted.
- Reorg handling: if a new header arrives with higher cumulative score than the current chain tip, the contract walks back and updates.

### Equihash and Blake2b Precompiles

| Precompile      | Address | Purpose                                                                                                              | Gas model                                                 |
| --------------- | ------- | -------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------- |
| Equihash(200,9) | 0x0B    | Validates Zcash proof-of-work solutions                                                                              | Heavy flat-rate fee, benchmarked on minimum-spec hardware |
| Blake2b         | 0x0C    | Blake2b hashing with Zcash personalization strings. Merkle root verification and ZIP-244 transaction ID computation. | Dynamic linear fee based on payload size                  |

### Staking Porter (ZcashStake)

When a relayer submits a Zcash transaction containing a CLTV timelock and OP_RETURN delegation data, the ZcashStake contract:

1. Validates the redeem script format (CLTV structure)
2. Computes the transaction ID using ZIP-244 transparent component hashing via Blake2b (0x0C)
3. Verifies transaction inclusion against the Zcash Light Client (Merkle proof against a confirmed header)
4. Parses the OP_RETURN output to extract the validator candidate address and delegator reward address
5. Registers the delegation with the appropriate staking weight and lock duration

Phase 1 handles transparent Zcash transactions only. Private staking is a future upgrade.

### Relayer Network

Relayers are permissionless. Any participant can run a relayer that submits Zcash block headers and staking transactions to Z. Relayers are compensated through the SystemReward contract.

---

## Comparison to Core's Satoshi Plus

| Component               | Core                          | Z                              |
| ----------------------- | ----------------------------- | ------------------------------ |
| PoW source              | Bitcoin miners (SHA-256)      | Zcash miners (Equihash)        |
| Self-custodial staking  | BTC holders (CLTV on Bitcoin) | ZEC holders (CLTV on Zcash)    |
| Token staking           | CORE token holders            | Z token holders                |
| Header verification     | SHA-256                       | Equihash + Blake2b precompiles |
| Transaction format      | Bitcoin                       | ZIP-244 (transparent, Phase 1) |
| Source chain block time | ~10 minutes                   | ~75 seconds                    |
| Confirmation depth      | 6 blocks (~60 min)            | 6 blocks (~7.5 min)            |
| Round interval          | 24 hours                      | 24 hours                       |
| Live since              | January 2023                  | Pre-launch                     |

---

## Finality

ZSP uses probabilistic finality with a 6-block confirmation depth on the Zcash source chain. On Z itself, once a supermajority of validators signs off on a block, it is considered final.

### Crosslink Implications

Crosslink is a proposed Zcash protocol alteration that would layer a proof-of-stake finality gadget on top of Zcash's proof-of-work consensus. ZSP does not depend on Crosslink and requires zero changes to the Zcash protocol.

ZSP reads Zcash state through a light client using existing Zcash primitives (CLTV timelocks, OP_RETURN metadata, Equihash block headers). These primitives are unchanged by Crosslink. If Crosslink goes live, miners continue embedding delegation metadata in blocks they already mine, and ZEC holders continue timelocking on the Zcash base layer. ZSP participation is unaffected.

---

## Private Staking

At launch, all three delegation legs operate on transparent Zcash data. Staked balances and delegation choices are visible on Zcash.

Private staking is a future goal. The vision is for ZEC holders to stake without revealing their balance or delegation choice. This is a post-launch upgrade.

---

## What ZSP Creates for ZEC

**For ZEC holders:** ZEC becomes yield-bearing for the first time. Without any changes to the Zcash protocol, holders earn staking income by timelocking ZEC they already hold. No new trust assumptions beyond Zcash's own consensus rules.

**For Zcash miners:** A second revenue stream at zero additional cost. Miners embed delegation metadata in blocks they already mine and earn Z token rewards on top of existing ZEC mining income.

**For the Zcash network:** More economic utility for ZEC without requiring protocol changes. ZSP creates demand for ZEC through staking and makes mining more profitable through dual rewards.

For liquid staking, see [[Liquid Staking]]. For the Z token's role in emissions, see [[The Z Token]].
