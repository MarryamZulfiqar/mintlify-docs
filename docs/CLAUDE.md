# Z Protocol — Project Instructions

Read this file before writing, editing, or expanding any Z Protocol documentation. These rules are non-negotiable.

---

## What these docs are

This is Z Protocol's public-facing documentation. Not developer docs. Audience: investors, prospective community members, ZEC holders evaluating participation, and sophisticated crypto-native readers who want to understand the architecture.

Tone: clear, confident, technically grounded. No hype words. No em dashes. Let the design speak for itself.

---

## Hard writing rules

1. **No em dashes.** Use a comma, a period, or restructure the sentence.
2. **No hype words.** "Revolutionary," "game-changing," "groundbreaking," "unprecedented" — never.
3. **No negative counterpositioning.** Don't tear down named competitors. Lead with what Z does.
4. **No preemptive denial.** Don't introduce a negative framing just to dismiss it.
5. **Present tense for what exists. Future tense for what's planned.** Never describe planned features as if they exist.
6. **Technical accuracy first.** If a technical reader checks the claim, they should find exactly what was described.
7. **Z is a protocol, not a team.** Don't say "Z builds" or "Z decided." Use passive voice or structural framing.
8. **No unfinalized specifics.** No fee percentages, revenue splits, or distribution ratios unless confirmed final.
9. **Don't rule out future token launches** by Zcosystem apps.
10. **Don't say external teams can't access MASP.** Frame vertical integration as coordinated design, not exclusion.

---

## Mandatory privacy terminology

Z has three privacy levels. Use the correct term for each. Never conflate them.

| Term | When to use |
|------|-------------|
| **Shielded** | Inside the MASP specifically. In-pool balances and transfers only. |
| **Unlinkable** | DeFi via stealth addresses. Activity is on-chain and visible, but not traceable to an identity. |
| **Private** | General-purpose term. Acceptable for both contexts. |
| **Encrypted** | Note data transmission only (encrypted with recipient's viewing key). Never use for transactions generally. |

**Never say "encrypted transactions."** Z uses ZK proofs and commitments.

---

## Mandatory protocol terminology

| Say this | Never say this |
|----------|----------------|
| Z or Z Protocol | Z Chain |
| the Z token, Z tokens | $Z |
| Zcash Satoshi Plus or ZSP | Zatoshi Plus |
| MASP or ShieldedPool | just "the pool" in technical contexts |
| wZEC | just "ZEC" when referring to the ERC-20 on Z |

---

## What NOT to include

These things must not appear in any documentation:

- **Surplus Router** — not confirmed for public docs
- **Z Pay, Z Run** — just ideas, not products
- **Team sizes or headcount**
- **MEV resistance via stealth addresses** — not a v1 property
- **ZEC withdrawals at launch** — ZEC deposits are post-launch. ZEC withdrawals via FROST are a priority addition but not at launch.
- **Specific fee numbers** (mint/redeem fees, trading fees as percentages) — not finalized
- **4-tier staking model** — only Solo and Dual
- **stZEC qualifying for Dual Staking** — it does NOT. Only self-custodial ZEC (CLTV timelock) qualifies.
- **Pre-TGE phases or numbered roadmap phases** — roadmap is Launch / Post Launch only
- **"USDZ is the unit of account"** — wrong framing
- **"Everything runs through USDZ"** — wrong framing
- **"Z Lend only for borrowing USDZ"** — Z Lend is a general lending/borrowing protocol
- **"100% of borrow interest to protocol"** — Z Lend uses Aave-style reserve factor (majority to depositors)
- **Confirmed yield source for yUSDZ** — TBD. Say "yield sources to be determined" or list candidates.

---

## What stZEC is and isn't

stZEC does NOT qualify for Dual Staking. This is a common error to avoid.

| Property | stZEC | Self-custodial ZEC |
|----------|-------|------------------|
| Qualifies for Dual Staking boost | No | Yes |
| ZEC location | Bridged to Z, held in contract | Stays on Zcash chain |
| Liquidity | Liquid | Locked (timelocked) |

---

## Critical technical facts

- Z uses **Groth16 on BN254**. NOT Halo 2. NOT STARKs. Zcash L1 uses Halo 2 — this is a different system entirely.
- Hash function inside ZK circuits: **Poseidon**, not keccak256.
- Custom precompiles: Equihash at **0x0B**, Blake2b at **0x0C**.
- Dual Staking: only self-custodial ZEC (CLTV timelock on Zcash chain) qualifies. stZEC does not.
- Z token fixed supply: **1 billion**.
- Gas: **Z token** (ZEC accepted via Paymaster, never directly as gas).
- ZEC stakers cannot be slashed — ZEC is on the Zcash chain, outside Z's control.

---

## Source of truth

If a claim is unclear, check the source docs in this order:
1. `Docs Plan.md` — authoritative content spec and writing rules
2. Individual docs (01–13) — final approved content
3. `Docs Outline.md` — structural reference

Do not invent numbers, addresses, or mechanics not present in the source docs.
