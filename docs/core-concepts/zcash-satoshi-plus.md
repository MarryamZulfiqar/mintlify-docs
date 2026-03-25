---
title: "Zcash Satoshi Plus (ZSP)"
sidebarTitle: "Zcash Satoshi Plus (ZSP)"
description: "Z is secured by three groups — ZEC miners, ZEC holders, and Z token stakers — who elect validators through a hybrid scoring system. How each leg works, how validators are elected, and what ZSP creates for ZEC."
keywords: ['ZSP', 'Zcash Satoshi Plus', 'consensus', 'validator election', 'DPoW', 'self-custodial staking', 'DPoS', 'ZEC miners', 'CLTV', 'timelock', 'hybrid score', 'Equihash precompile', 'Blake2b precompile', 'Zcash Light Client', 'ZcashStake', 'slashing']
ai_summary: "ZSP (Zcash Satoshi Plus) adapts Core DAO's Satoshi Plus for ZEC. Three legs. Leg 1 (DPoW): ZEC miners embed delegation metadata (validator address + reward address) in OP_RETURN outputs of mined Zcash blocks. Z reads via Zcash Light Client: Equihash precompile (0x0B) + Blake2b precompile (0x0C), 6-block confirmation, 7-round delay on power calculations. Leg 2 (Self-custodial): ZEC holders create CLTV output + OP_RETURN on Zcash chain. ZEC never leaves wallet. ZcashStake verifies via Merkle proof against confirmed header (ZIP-244). Staking weight = amount x duration multiplier. No slashing on ZEC principal. Leg 3 (DPoS): Z token holders delegate via ZSPStake contract. Earn 2/3 of emissions. Validator election: hybrid score from normalized weights of all three legs with per-leg hardcaps. Top 21 become active set. Round = 24h. CandidateHub.turnRound() orchestrates transition. Slashing: validators only, downtime leads to jailing, double-sign leads to slash bond. Delegators never slashed. ZEC stakers cannot be slashed by definition."
---

# Zcash Satoshi Plus (ZSP)

Z is secured by a set of elected validators. Those validators are elected by three groups:

1. **ZEC miners** delegate their existing hashrate to validators by embedding a vote in the blocks they already mine. No new hardware, no new energy.
2. **ZEC holders** timelock ZEC on the Zcash chain to vote for validators. The ZEC never leaves their wallet.
3. **Z token stakers** stake Z tokens directly on Z to vote for validators.

Each round, the candidates with the most combined support across all three groups are elected to the active validator set. The elected validators produce blocks and secure Z, earning consensus rewards that flow back to themselves and their voters.

This mechanism is called Zcash Satoshi Plus (ZSP). The design is adapted from Core DAO's Satoshi Plus, which has secured Core using delegated BTC since January 2023 with no outages. Zcash and Bitcoin share the same UTXO model, the same timelock primitives (CLTV), and the same OP_RETURN metadata fields. The architecture maps to Zcash with targeted cryptographic changes rather than a ground-up redesign.

---

## The three legs

### Leg 1: ZEC miners (Delegated Proof of Work)

Zcash miners vote for Z validators by embedding delegation metadata in the blocks they already mine.

**What the miner does:**
- Includes two pieces of data in an OP_RETURN output (80-byte limit) when mining a Zcash block: the Z validator address they are voting for, and the Z address where they want to receive rewards
- No new hardware, no additional energy, no changes to existing mining operations
- For mining pools, the pool operator delegates on behalf of all participants

**How Z reads the votes:**
- A Zcash Light Client contract on Z verifies Zcash block headers on-chain
- Permissionless relayers submit headers in batches (~1,152 headers per day given Zcash's 75-second block time)
- Each header is validated using the Equihash precompile (0x0B) and Blake2b precompile (0x0C) before being accepted

**Safety mechanisms:**
- 6-block confirmation depth (~7.5 minutes) before miner power is counted, to handle reorgs
- Power calculations are frozen 7 rounds in the past to prevent last-minute manipulation of validator elections

---

### Leg 2: ZEC holders (self-custodial staking)

ZEC holders stake by timelocking their ZEC on the Zcash chain. The ZEC stays in the holder's wallet the entire time. No bridging. No wrapping. No custody transfer.

**What the holder does:**
1. Creates a CLTV (CheckLockTimeVerify) output that locks the ZEC for the chosen duration
2. Includes an OP_RETURN output containing the Z validator address and the holder's Z reward address

**How Z verifies it:**
- A relayer submits the transaction to Z's ZcashStake contract
- The contract verifies inclusion via the Zcash Light Client (Merkle proof against a confirmed header)
- Delegation metadata is parsed from the OP_RETURN payload using ZIP-244 transparent component parsing

**Staking weight:**
- Factors in both the amount locked and the lock duration
- Longer timelocks produce higher voting power per ZEC
- When the timelock expires, the ZEC is spendable again on Zcash with no action required on Z

**Properties:**
- No slashing risk. If the staker's elected validator misbehaves, the staker receives no rewards, but their principal is unaffected.
- No custody transfer. The ZEC never leaves the holder's possession.
- No trust assumptions beyond the Zcash protocol itself.
- The only cost is temporary illiquidity.

---

### Leg 3: Z token stakers (Delegated Proof of Stake)

Z token holders stake directly on Z by delegating tokens to validators through the ZSPStake contract. Standard on-chain staking, identical in mechanism to Core's CORE token staking.

- No slashing risk on principal.
- Z stakers earn the highest proportion of consensus emissions (2/3 of total).
- Staking Z alongside ZEC earns boosted yield through [Dual Staking](/docs/staking/dual-staking).

---

## Validator election

Every round, each validator candidate receives a hybrid score calculated from the combined weight of all three delegation sources:

```
For each candidate:
  score = f(mining_delegation, zec_staking_weight, z_token_delegation)
```

The scoring function normalizes each leg against hardcaps that prevent any single source from dominating the election. If a validator accumulates more delegation in one leg than the hardcap allows, the excess does not increase their score. This forces candidates to attract balanced support across multiple legs rather than relying on one whale miner or one large ZEC staker.

The candidates with the highest hybrid scores are elected to the active validator set. The default set size is 21 validators.

### Becoming a validator candidate

Validator candidates register through the CandidateHub contract by staking a minimum deposit of Z tokens and providing their consensus key and commission rate. Candidates must maintain a node that meets the network's hardware and uptime requirements.

The commission rate determines what percentage of delegator rewards the validator keeps. Changes take effect in the next round to prevent mid-round manipulation.

---

## Round lifecycle

A round lasts 24 hours. At the end of each round, the CandidateHub contract orchestrates the transition:

1. **Freeze state.** All delegation data for the current round is finalized.
2. **Filter candidates.** Only active, non-jailed candidates with sufficient minimum delegation qualify.
3. **Calculate hybrid scores.** StakeHub aggregates delegation data from all three agent contracts (EquihashPowerAgent, ZcashAgent, ZSPStake) and computes each candidate's normalized score.
4. **Sort and elect.** Candidates are ranked by hybrid score. The top 21 become the active validator set.
5. **Distribute rewards.** The previous round's block rewards are distributed from ValidatorSet through StakeHub to the three agent contracts, then to individual delegators proportional to their delegation weight.
6. **Reset flags.** Validator and candidate state is reset for the new round.

This entire sequence executes in the `turnRound` function on CandidateHub. Any account can trigger it once the round interval has elapsed.

---

## Emissions and rewards

Consensus rewards are paid in Z tokens.

### Distribution

| Group | Share | Rationale |
|-------|-------|-----------|
| Z token stakers | 2/3 | Primary on-chain security providers |
| ZEC stakers | 1/6 | Cross-chain security from self-custodial ZEC |
| ZEC miners | 1/6 | Proof-of-work security via hashrate delegation |

These ratios are subject to change before launch.

### Reward flow

1. **ValidatorSet** receives block rewards.
2. **StakeHub** receives the round's accumulated rewards and routes them to the three leg-specific agent contracts.
3. Each agent contract distributes to individual delegators proportional to their delegation weight.

Validators take a configurable commission before delegator distribution. Transaction fees flow through the same pipeline alongside block rewards.

---

## Slashing and accountability

Z inherits Core's slashing model. Validators face penalties for poor performance. Delegators do not.

**Downtime.** Validators that miss too many blocks in a round accumulate slash indicators. After crossing a threshold, the validator is jailed and excluded from future elections until they unjail themselves.

**Double-signing.** Submitting conflicting blocks at the same height results in immediate slashing. Evidence can be submitted through the SlashIndicator contract.

**Delegator protection.** Slashing does not affect delegator principal. ZEC stakers cannot lose their timelocked ZEC under any circumstance — the ZEC is held on the Zcash chain, outside Z's reach entirely. Z token delegators may see reduced rewards if their chosen validator is penalized, but their staked tokens are not at risk.

### Why no delegator slashing

**ZEC stakers cannot be slashed by definition.** Timelocked ZEC sits on the Zcash chain in the staker's own wallet. Z has no mechanism to touch it, and no mechanism could be added without changing the Zcash protocol. This is the same property that makes Bitcoin staking on Core trustless.

**Validators are still accountable.** Validators post a Z token bond when they register. That bond can be slashed for double-signing or persistent downtime. Validators also face jailing. Delegators are accountable through reward forfeiture: electing a bad validator means earning nothing for that round.

**Principal protection is what participants value most.** The guarantee that staked ZEC and staked Z tokens cannot be taken is the foundation of participation.

---

## On-chain verification infrastructure

### Zcash Light Client

A system contract that maintains a verified chain of Zcash block headers on Z.

- Relayers submit headers in batches (~1,152 per day). Each header is validated using the Equihash precompile before being stored.
- 6-block confirmation depth (~7.5 minutes). Headers with fewer confirmations are not yet trusted.
- Reorg handling: if a new header arrives with higher cumulative score than the current chain tip, the contract walks back and updates.

### Equihash and Blake2b precompiles

| Precompile | Address | Purpose | Gas model |
|------------|---------|---------|-----------|
| Equihash(200,9) | `0x0B` | Validates Zcash proof-of-work solutions on-chain | Heavy flat-rate fee, benchmarked on minimum-spec hardware |
| Blake2b | `0x0C` | Blake2b hashing with Zcash personalization strings. Merkle root verification and ZIP-244 transaction ID computation. | Dynamic linear fee based on payload size |

The Equihash precompile is implemented in deterministic Go to prevent consensus forks from non-deterministic behavior across validator nodes.

### Staking Porter (ZcashStake)

When a relayer submits a Zcash transaction containing a CLTV timelock and OP_RETURN delegation data, the ZcashStake contract:

1. Validates the redeem script format (CLTV structure)
2. Computes the transaction ID using ZIP-244 transparent component hashing via Blake2b (0x0C)
3. Verifies transaction inclusion against the Zcash Light Client (Merkle proof against a confirmed header)
4. Parses the OP_RETURN output to extract the validator candidate address and delegator reward address
5. Registers the delegation with the appropriate staking weight and lock duration

Phase 1 handles transparent Zcash transactions only. Shielded staking requires a ZK-Prover Pipeline (Phase 2).

### Relayer network

Relayers are permissionless. Any participant can run a relayer that submits Zcash block headers and staking transactions to Z. Relayers are compensated through the SystemReward contract.

---

## Security properties

**Mining pool concentration.** The hardcap on mining delegation weight prevents any single pool from dominating validator elections.

**Hardcap normalization.** Each leg has a per-validator hardcap. Delegation beyond the cap does not increase the validator's hybrid score, forcing validators to attract diverse delegator bases.

**Confirmation depth.** The 6-block confirmation window protects against short-range reorgs on Zcash.

**Round delay.** Miner power is frozen 7 rounds in the past. A miner cannot rapidly shift hash delegation to manipulate an upcoming election.

**Inherited battle-testing.** The scoring, election, reward distribution, and slashing logic has been running on Core DAO since January 2023. Z's modifications are narrowly scoped to Zcash cryptographic primitives.

---

## Comparison to Core's Satoshi Plus

| Component | Core | Z |
|-----------|------|---|
| PoW source | Bitcoin miners (SHA-256) | Zcash miners (Equihash) |
| Self-custodial staking | BTC holders (CLTV on Bitcoin) | ZEC holders (CLTV on Zcash) |
| Token staking | CORE token holders | Z token holders |
| Header verification | SHA-256 | Equihash + Blake2b precompiles |
| Transaction format | Bitcoin | ZIP-244 (transparent, Phase 1) |
| Source chain block time | ~10 minutes | ~75 seconds |
| Headers per day | ~144 | ~1,152 |
| Confirmation depth | 6 blocks (~60 min) | 6 blocks (~7.5 min) |
| Round interval | 24 hours | 24 hours |
| Live since | January 2023 | Pre-launch |

---

## Finality

ZSP uses probabilistic finality with a 6-block confirmation depth on the Zcash source chain. On Z itself, once a supermajority of validators signs off on a block, it is considered final.

### Crosslink implications

Crosslink is a proposed Zcash protocol alteration that would layer a proof-of-stake finality gadget on top of Zcash's proof-of-work consensus. ZSP does not depend on Crosslink and requires zero changes to the Zcash protocol.

ZSP reads Zcash state through a light client using existing Zcash primitives (CLTV timelocks, OP_RETURN metadata, Equihash block headers). These primitives are unchanged by Crosslink. If Crosslink goes live, miners continue embedding delegation metadata in blocks they already mine, and ZEC holders continue timelocking on the Zcash base layer. ZSP participation is unaffected.

---

## Roadmap to private staking

### Phase 1: Transparent delegation

All three delegation legs operate on transparent Zcash data. Staked balances and delegation choices are visible on Zcash.

### Phase 2: Privacy-preserving staking

Shielded staking would allow ZEC holders to stake from Zcash's Orchard pool without revealing their balance or delegation choice. This requires a ZK-Prover Pipeline that translates Zcash's Halo 2 proofs (Pallas/Vesta curves) into EVM-verifiable BN254 proofs.

The approach is an off-chain prover network that wraps Halo 2 proofs into PLONK-KZG (BN254) proofs. This adds proof generation latency but preserves Z's EVM compatibility.

---

## What ZSP creates for ZEC

**For ZEC holders:** ZEC becomes yield-bearing for the first time. Without any changes to the Zcash protocol, holders earn staking income by timelocking ZEC they already hold. No new trust assumptions beyond Zcash's own consensus rules.

**For Zcash miners:** A second revenue stream at zero additional cost. Miners embed delegation metadata in blocks they already mine and earn Z token rewards on top of existing ZEC mining income.

**For the Zcash network:** More economic utility for ZEC without requiring protocol changes. ZSP creates demand for ZEC through staking and makes mining more profitable through dual rewards.
