# Z Trade

Z Trade is Z's enshrined DEX. Swap assets privately using the [[ShieldedPool (MASP)|ShieldedPool]] and the RelayAdapter pattern. A private swap is one transaction: unshield, execute, reshield.

---

## How Private Trading Works

Standard DeFi swaps reveal the trader's address and amounts on-chain. Z Trade uses the RelayAdapter pattern to make swaps unlinkable.

**Step 1: Assets in the ShieldedPool.** Your tokens sit as private notes. Nobody outside can see your balance or that you're present at all.

**Step 2: Generate a ZK proof.** Your wallet generates a Groth16 proof authorizing the swap. The proof commits to the specific input notes being spent, the expected output, and an `adaptParams` binding:

```
adaptParams = keccak256(nullifiers, commitments, actionData)
```

This binding is included in the proof's public inputs. It locks the broadcaster to the exact parameters you specified.

**Step 3: Submit to broadcaster.** You send the signed proof to a broadcaster, not directly to the blockchain. The broadcaster submits the transaction on-chain, so your address never appears as `msg.sender`.

**Step 4: RelayAdapter executes atomically.** In one atomic transaction, the RelayAdapter:

- Unshields input tokens from the ShieldedPool to the adapter contract
- Executes the swap on Z Trade's AMM (approve, swap, collect output)
- Reshields output tokens back into the ShieldedPool as new private notes

**Step 5: Result.** An observer sees a transaction at the RelayAdapter contract. They cannot see who initiated it, link it to any ShieldedPool balance, or connect it to other activity. Your output tokens land in the ShieldedPool as fresh private notes.

The `adaptParams` binding prevents the broadcaster from changing the recipient, altering the fee, or substituting a different trade. If any bound parameter changes, the ZK proof is invalid and the transaction reverts.

---

## Liquidity Provision

Add liquidity to Z Trade pools to earn trading fees. Liquidity provision uses standard Uniswap v3 position mechanics.

Private liquidity provision (adding and removing liquidity from the ShieldedPool without revealing your identity) is a post-launch feature.
