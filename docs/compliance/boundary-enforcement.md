---
title: Boundary Enforcement
description: Technical reference for Z's MASP entry and exit screening — the ASPManager contract, deposit screening logic, withdrawal screening, and the Privacy Pools (AssociationSetProver) compliance mechanism.
keywords: [boundary enforcement, ASP, ASPManager, DepositScreener, AssociationSetProver, Privacy Pools, OFAC, sanctions, compliance]
ai_summary: Z boundary enforcement at the contract level. ASPManager: registers ASPs, staleness threshold default 24h, iterates all active ASPs for each screening check. Screening logic: for each active ASP check isApproved(wallet) AND check lastUpdated within staleness threshold, reject if either fails. DepositScreener: integrates ASPManager into MASP deposit flow. AssociationSetProver: Privacy Pools. ASPs publish Merkle roots of compliant deposit sets. Users generate ZK proof of membership — proves deposit is in a compliant group without revealing which deposit. Supports multiple ASPs, each with multiple association sets. Sets versioned with historical root tracking. Deactivating a set preserves existing proofs. 48h timelock on all contract modifications, two-step ownership transfer.
---

# Boundary Enforcement

Z controls access to the MASP at its boundaries — deposit (entry) and withdrawal (exit) — without monitoring activity inside the pool. This page covers the technical architecture.

---

## ASPManager

The ASPManager contract maintains the registry of approved Association Set Providers (ASPs). ASPs are third-party compliance services that maintain curated lists of screened wallet addresses.

### Screening logic

When a deposit or withdrawal is requested, ASPManager iterates through all active ASPs:

```
_screenWallet(wallet)
  → for each active ASP:
       if NOT asp.isApproved(wallet):
           reject
       if block.timestamp - asp.lastUpdated() > stalenessThreshold:
           reject
  → all ASPs approved AND all data fresh → proceed
```

**All registered ASPs must approve.** Any single rejection blocks the deposit or withdrawal.

**Stale data causes rejection.** If an ASP hasn't updated within the staleness threshold (default: 24 hours), deposits and withdrawals are blocked until the provider refreshes. This is a protective default — outdated compliance data is treated as potentially incomplete.

**Multiple ASPs provide defense in depth.** Registering multiple ASPs means a single provider failure doesn't create a compliance gap.

### ASP management

- **Adding an ASP:** Owner registers a new provider with a name and staleness threshold
- **Removing an ASP:** Deactivates the provider for future screening; historical data is preserved
- **Updating staleness threshold:** Adjustable per provider

---

## DepositScreener

The DepositScreener integrates ASPManager into the MASP deposit flow. Every deposit call passes through the screener before the MASP accepts it.

**For standard deposits:** The depositing wallet address is screened before tokens enter the MASP.

**For shielded minting (commitment-based deposits):** The screener operates on the source chain address — the address that locked USDC or USDT in the bridge contract. Only the commitment crosses the bridge to Z, but the screener has access to the source chain deposit address via the LayerZero message payload.

---

## Withdrawal screening

At withdrawal, the recipient address is screened against the same ASP lists used at entry. If the destination address is flagged, the withdrawal transaction is rejected.

The system screens the destination address. It does not attempt to trace the provenance of funds inside the MASP — the privacy model makes this impossible and by design.

---

## AssociationSetProver (Privacy Pools)

The AssociationSetProver implements the Privacy Pools compliance model, providing a stronger compliance signal than address screening alone.

### How association sets work

1. An ASP publishes a Merkle tree of deposit commitments it has screened and approved
2. The Merkle root of this tree is stored on-chain in the AssociationSetProver
3. A user wanting to demonstrate clean funds generates a ZK proof: "my deposit is a member of this association set"
4. The proof is verified on-chain — the recipient or exchange sees "verified clean" without seeing which specific deposit

The ZK proof establishes: "this deposit is in association set X." It reveals nothing else — not the deposit amount, not the depositor's identity, not the deposit's position in the set.

### Contract capabilities

- Supports multiple ASPs, each managing multiple association sets
- Sets are versioned with historical root tracking — recent roots remain valid to prevent race conditions during ASP updates
- ASPs can create, update, and deactivate sets
- Deactivating a set prevents further updates but preserves existing proofs against it

### Using association set proofs

Exchange or counterparty receives:
- A ZK proof of Merkle membership
- The association set ID being referenced
- The ASP that manages the set

They verify on-chain. The verification result is binary: the deposit is a member of the specified screened set, or it isn't.

---

## Geographic restrictions

Wallet-level sanctions screening (ASPManager) is protocol-enforced. Geographic restrictions — blocking users in sanctioned jurisdictions — are enforced at the application layer through IP-based methods and user attestation.

This distinction matters: the protocol cannot determine jurisdiction from a wallet address alone. Geographic enforcement is commercially reasonable at the application layer; it is not a protocol-level guarantee.

---

## Upgrade controls

**48-hour timelock on all changes.** Any modification to ASPManager, DepositScreener, AssociationSetProver, or related compliance infrastructure requires a mandatory 48-hour on-chain waiting period before taking effect. This gives users time to review changes or withdraw funds.

**Two-step ownership transfer.** All compliance contracts use a two-step ownership transfer pattern. The current owner initiates. The new owner must explicitly accept. This prevents accidental or malicious ownership changes.
