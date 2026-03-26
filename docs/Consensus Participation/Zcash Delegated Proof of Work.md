# Zcash Delegated Proof of Work

Zcash miners participate in [[Zcash Satoshi Plus (ZSP)|ZSP]] consensus by delegating their existing hashrate to Z validators. The miner embeds delegation metadata in the Zcash blocks they already mine. No new hardware, no additional energy, no changes to existing mining operations. In return, miners earn Z token rewards on top of their existing ZEC mining income.

---

## How it works

When a Zcash miner mines a block, they include an OP_RETURN output (within the 80-byte limit) containing two pieces of data:

- The Z validator address they are voting for
- The Z address where they want to receive rewards

That is the entire miner-side operation. The miner continues mining Zcash exactly as before. The only change is the inclusion of delegation metadata in blocks they are already producing.

For mining pools, the pool operator embeds the delegation metadata on behalf of all pool participants.

---

## How Z reads the votes

A Zcash Light Client contract on Z maintains a verified chain of Zcash block headers. Permissionless relayers submit these headers in batches. Each header is validated on-chain using the Equihash and Blake2b precompiles before being accepted.

Once a header is confirmed, Z can read the delegation metadata embedded in the corresponding block's OP_RETURN outputs. The miner's hashrate contribution is attributed to their chosen validator for use in the hybrid score calculation that determines the active validator set each round.

---

## Safety mechanisms

**Confirmation depth.** Miner delegation power is not counted until 6 Zcash block confirmations have passed (approximately 7.5 minutes). This handles Zcash chain reorgs. If a block containing delegation data is orphaned, the delegation is discarded.

**Frozen power calculations.** Power calculations for validator elections use delegation data frozen 7 rounds in the past. This prevents miners from manipulating validator elections by switching delegation at the last minute before a round boundary.

**Reorg handling.** If the Zcash Light Client receives a new header with a higher cumulative difficulty score than the current chain tip, the contract walks back and updates. Delegations from orphaned blocks are invalidated.

---

## Verification infrastructure

### Zcash Light Client

A system contract on Z that stores and verifies Zcash block headers. Relayers submit headers in batches. Each header is validated using the Equihash precompile (address 0x0B) before being stored. The contract maintains a chain of verified headers that other contracts (including the staking and mining delegation contracts) use as a source of truth for Zcash chain state.

### Equihash and Blake2b precompiles

| Precompile | Address | Purpose | Gas model |
|---|---|---|---|
| Equihash(200,9) | 0x0B | Validates Zcash proof-of-work solutions | Heavy flat-rate fee, benchmarked on minimum-spec hardware |
| Blake2b | 0x0C | Blake2b hashing with Zcash personalization strings. Used for Merkle root verification and ZIP-244 transaction ID computation. | Dynamic linear fee based on payload size |

These precompiles allow the Zcash Light Client to verify Zcash block headers natively on-chain without relying on off-chain oracles.

### Relayer network

Relayers are permissionless. Anyone can run a relayer that submits Zcash block headers to Z. Relayers are compensated through the SystemReward contract. Because relaying is permissionless and economically incentivized, no single party controls the flow of Zcash data into Z.

---

## What miners earn

Miners earn Z token rewards each round proportional to their hashrate contribution relative to all other mining delegators. Rewards are distributed at the end of each 24-hour round through the StakeHub contract, after the elected validator takes their configurable commission.

Mining delegation is one of three legs in the [[Zcash Satoshi Plus (ZSP)|ZSP]] hybrid score. The scoring function normalizes each leg against hardcaps that prevent any single source from dominating validator elections.

---

## Properties

**Zero additional cost.** Miners embed delegation metadata in blocks they are already producing. There is no marginal cost in hardware, energy, or operational overhead.

**No custody transfer.** There is no ZEC at stake. The miner is delegating hashrate influence, not depositing tokens.

**No changes to Zcash.** Delegated Proof of Work uses existing Zcash primitives: OP_RETURN outputs for metadata and standard block headers for verification. ZSP requires zero changes to the Zcash protocol.

**Pool compatible.** Mining pools embed delegation metadata on behalf of their participants. Individual miners in a pool do not need to take any action beyond choosing a pool that delegates to a Z validator.

**Permissionless verification.** The entire pipeline from Zcash block to Z delegation is trustless. Relayers submit headers, the Zcash Light Client verifies them using on-chain precompiles, and delegation data is parsed deterministically.

---

## For mining pools

Pool operators who want to participate:

1. Choose a Z validator to support
2. Configure the pool's block template to include an OP_RETURN output with the validator address and the pool's Z reward address
3. Distribute Z token rewards to pool participants according to the pool's own reward policy
