---
title: "Self-Custodial Staking"
sidebarTitle: "Self-Custodial Staking"
description: "ZEC holders earn Z token rewards by timelocking ZEC on the Zcash chain. No bridge. No custody transfer. ZEC stays in your wallet."
keywords: ['self-custodial staking', 'ZEC', 'CLTV', 'timelock', 'OP_RETURN', 'ZSP', 'Zcash', 'Leg 2', 'staking', 'yield', 'Z token rewards']
ai_summary: "Self-custodial staking is ZSP Leg 2. ZEC holders create a CLTV (CheckLockTimeVerify) output on the Zcash chain that locks ZEC for a chosen duration, plus an OP_RETURN output with Z validator address and Z reward address. ZEC never leaves the holder's wallet. Z's ZcashStake contract verifies inclusion via Zcash Light Client (Merkle proof against confirmed header) and parses OP_RETURN via ZIP-244. Staking weight factors both amount locked and lock duration — longer timelocks = higher voting power per ZEC. No slashing risk on ZEC principal. Qualifies for Dual Staking yield boost when combined with Z token staking. Timelock expiry: ZEC becomes spendable automatically on Zcash when the lock period ends."
---

# Self-Custodial Staking

ZEC holders earn Z token rewards by timelocking ZEC on the Zcash chain. The ZEC never leaves your wallet. No bridge. No wrapping. No custody transfer.

---

## How it works

You create two outputs on the Zcash chain in a single transaction:

1. **A CLTV output** — locks your ZEC for a chosen duration using CheckLockTimeVerify
2. **An OP_RETURN output** — contains your delegation metadata: the Z validator address you are voting for, and the Z reward address where you want to receive Z token rewards

Z's ZcashStake contract reads your delegation through a relayer that watches the Zcash chain. After 6 Zcash block confirmations (~7.5 minutes), your delegation weight is counted in the next full 24-hour round.

---

## Step-by-step

**Step 1: Choose a validator.**
Review the active validator set and their commission rates on the Z staking portal. Choose a validator with a strong hybrid score and reasonable commission.

**Step 2: Choose a lock duration.**
Longer timelocks produce higher voting power per ZEC. The minimum lock duration is subject to protocol parameters.

**Step 3: Create the timelock.**
Using a Zcash wallet that supports custom script outputs, create a transaction with:

```
Output 1 — CLTV timelock:
  Amount: [ZEC to stake]
  Script: OP_[locktime] OP_CHECKLOCKTIMEVERIFY OP_DROP [your_pubkey] OP_CHECKSIG

Output 2 — OP_RETURN delegation:
  Script: OP_RETURN [magic_bytes][validator_address][reward_address]
```

Most users will use the Z staking portal UI, which generates this transaction automatically.

**Step 4: Receive rewards.**
At the end of each 24-hour round, Z token rewards are distributed to your reward address. Rewards accumulate automatically — no claiming required.

---

## Staking weight

Your voting power is determined by:
- Amount of ZEC locked
- Duration of the lock (longer = higher multiplier per ZEC)
- Time remaining (weight decreases linearly as the lock approaches expiry)

---

## Properties

**No slashing risk.** If your elected validator misbehaves, the validator is penalized but your principal ZEC is completely unaffected. The ZEC is locked on the Zcash chain, which Z cannot touch.

**No bridge required.** Your ZEC stays on the Zcash chain. No wZEC. No Z address required for the staking itself — you only provide a Z address for receiving rewards.

**No trust assumptions beyond Zcash.** Your ZEC is locked by Zcash's own CLTV mechanism. Z reads the delegation metadata but cannot modify or access the ZEC.

**Dual Staking eligibility.** Self-custodial ZEC staking qualifies for the Dual Staking yield boost when combined with Z token staking. See [Dual Staking](/docs/staking/dual-staking).

---

## When the timelock expires

The CLTV output becomes spendable automatically when the Zcash block height passes the locktime you specified. You spend it using standard Zcash wallet mechanics. Z automatically detects that your timelock has expired and stops counting it toward delegation weight.

You can re-lock the same ZEC for another period to continue participating.

---

## Phase 1 and Phase 2

**Phase 1 (current):** All delegation data is transparent on Zcash. Your staked balance and validator choice are visible on the Zcash chain.

**Phase 2 (future):** Shielded staking would allow staking from Zcash's Orchard shielded pool without revealing balance or validator choice. This requires a ZK-Prover Pipeline that translates Zcash's Halo 2 proofs into EVM-verifiable BN254 proofs. It is under research, not yet implemented.
