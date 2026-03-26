# ZEC Staking

Z offers self-custodial staking for ZEC holders. ZEC holders earn Z token rewards by timelocking ZEC on the Zcash chain. The ZEC never leaves your wallet. No bridge. No wrapping. No custody transfer.

---

## How It Works

You create two outputs on the Zcash chain in a single transaction:

- **A CLTV output** that locks your ZEC for a chosen duration using CheckLockTimeVerify
- **An OP_RETURN output** that contains your delegation metadata: the Z validator address you are voting for, and the Z reward address where you want to receive Z token rewards

Z's ZcashStake contract reads your delegation through a relayer that watches the Zcash chain. After 6 Zcash block confirmations (~7.5 minutes), your delegation weight is counted in the next full 24-hour round.

---

## Step-by-Step

**Step 1: Choose a validator.** Review the active validator set and their commission rates on the Z staking portal. Choose a validator with a strong hybrid score and reasonable commission.

**Step 2: Choose a lock duration.** Longer timelocks produce higher voting power per ZEC. The minimum lock duration is subject to protocol parameters.

**Step 3: Create the timelock.** Using a Zcash wallet that supports custom script outputs, create a transaction with:

```
Output 1: CLTV timelock:
Amount: [ZEC to stake]
Script: OP_[locktime] OP_CHECKLOCKTIMEVERIFY OP_DROP [your_pubkey] OP_CHECKSIG

Output 2: OP_RETURN delegation:
Script: OP_RETURN [magic_bytes][validator_address][reward_address]
```

Most users will use the Z staking portal UI, which generates this transaction automatically.

**Step 4: Receive rewards.** At the end of each 24-hour round, Z token rewards are distributed to your reward address. Rewards accumulate automatically. No claiming required.

---

## Staking Weight

Your voting power is determined by:

- Amount of ZEC locked
- Duration of the lock (longer = higher multiplier per ZEC)
- Time remaining (weight decreases linearly as the lock approaches expiry)

---

## Properties

**No bridge required.** Your ZEC stays on the Zcash chain. No wZEC. No Z address required for the staking itself. You only provide a Z address for receiving rewards.

**No trust assumptions beyond Zcash.** Your ZEC is locked by Zcash's own CLTV mechanism. Z reads the delegation metadata but cannot modify or access the ZEC.

**[[Dual Staking]] eligibility.** ZEC staking qualifies for the [[Dual Staking]] yield boost when combined with Z token staking. See [[Dual Staking]].

---

## When the Timelock Expires

The CLTV output becomes spendable automatically when the Zcash block height passes the locktime you specified. You spend it using standard Zcash wallet mechanics. Z automatically detects that your timelock has expired and stops counting it toward delegation weight.

You can re-lock the same ZEC for another period to continue participating.

---

## Privacy

At launch, delegation data is transparent on Zcash. Your staked balance and validator choice are visible on the Zcash chain. Private staking, where holders can stake without revealing balance or delegation choice, is a future upgrade.
