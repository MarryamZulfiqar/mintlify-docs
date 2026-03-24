---
title: "Compliance"
slug: compliance
description: "Z is a privacy protocol. It also recognizes the need for compliant privacy features. Boundary enforcement at MASP entry and exit, Privacy Pools, selective disclosure, and compliance tools for exchanges."
keywords: [compliance, OFAC, sanctions, ASP, Association Set Provider, Privacy Pools, selective disclosure, viewing key, boundary enforcement, KYC, AML, Travel Rule, exchange onboarding]
ai_summary: |
  Z's compliance model treats the MASP as a zone with controlled entry and exit. Entry: deposits screened against OFAC sanctions lists via ASPs (Association Set Providers). ASPManager iterates all active ASPs, checks isApproved(wallet) and lastUpdated within staleness threshold (default 24h). Rejects if any ASP returns unapproved or if data is stale. Exit: recipient address screened against same ASP lists at withdrawal. Privacy Pools: AssociationSetProver contract lets users generate ZK proof of membership in a compliance-approved association set — proves funds are from a screened group without revealing which specific deposit. Selective disclosure: viewing keys derived from masterKey via Poseidon(masterKey, 1). Read-only, time-scopable, user-controlled. Cannot spend funds. Cannot view others. Exchange flow: user shares scoped viewing key → exchange verifies source of funds. Travel Rule: handled at exchange layer via TRISA/Sygna/Notabene — Z does not embed Travel Rule data on-chain. Compliance oracle: user generates ZK proof against published compliance circuit → oracle issues signed attestation → exchange accepts attestation without raw viewing key. Emergency controls: 48h timelock on all contract modifications, two-step ownership transfer. Design guarantees: no master key, no asset freezing, no transaction reversal, counterparty privacy (viewing key reveals holder's activity only).
---

# Compliance

Z is a privacy protocol. The MASP severs the connection between on-chain activity and identity, and the protocol is permissionless. Anyone can use it.

Z also recognizes the need for compliant privacy features, particularly for users and institutions that require compliance assurances and must operate within regulatory frameworks. Without violating the privacy principles of the system, certain components of the protocol screen wallets against sanctions lists maintained by third-party Association Set Providers (ASPs).

Additionally, compliance features are available at the individual level. Viewing keys give users the ability to selectively disclose transaction history. Privacy Pools let users prove their funds are clean without revealing their identity. These and other compliance tools are described below.

---

## Boundary Enforcement

The compliance model treats the MASP as a zone with controlled entry and exit. The protocol does not monitor activity inside the pool. It identifies which external addresses are requesting authorization to deposit into the pool and which external addresses are specified as asset destinations.

### Entry

Before any deposit into the MASP, the source wallet is checked against OFAC sanctions lists and watchlists through Association Set Providers (ASPs). Flagged wallets are rejected and the deposit fails. The check is deterministic and protocol-enforced.

ASPs are third-party compliance services that maintain curated lists of screened wallet addresses. Multiple ASPs can be registered simultaneously. The ASPManager contract iterates through all active providers and requires approval from each one. ASP data must be current within a configurable staleness threshold (default: 24 hours). If an ASP's data is stale, the deposit is rejected until the provider refreshes.

```
_screenWallet(wallet)
  → for each active ASP:
       check isApproved(wallet)
       check lastUpdated within staleness threshold
       reject if either fails
```

**Geographic restrictions.** Wallet-level sanctions screening is protocol-enforced. Geographic restrictions (blocking users in sanctioned jurisdictions) are enforced at the application layer through IP-based methods and user attestation. These are commercially reasonable measures enforced at the application layer, not at the protocol level.

### Withdrawal

At withdrawal, the recipient address is screened against the same ASP sanctions lists used at entry. If the destination address is flagged, the withdrawal transaction is rejected by the smart contract. Withdrawals to non-flagged addresses proceed.

The system screens the destination address, not the origin of the funds within the MASP. This is an address-level compliance check. It catches withdrawals to known-bad addresses but does not trace fund provenance inside the pool.

### Privacy Pools

Z implements the Privacy Pools model for withdrawal compliance through the AssociationSetProver contract. This provides a stronger compliance signal than address screening alone.

ASPs curate sets of compliant deposits and publish a Merkle root of each set. At withdrawal, a user can generate a ZK proof of membership in an approved set, proving their deposit belongs to a screened group without revealing which specific deposit is theirs.

The contract supports multiple ASPs, each managing multiple association sets. Sets are versioned with historical root tracking (recent roots remain valid to prevent race conditions during ASP updates).

In production, membership verification uses ZK proofs. The user proves: "my deposit is a member of this association set" without revealing their position in the set. The verifier learns that the deposit passed screening. Nothing else.

---

## Selective Disclosure

Users can share a viewing key with regulators, auditors, or counterparties to provide read-only access to their shielded transaction history.

### What a Viewing Key Reveals

- Complete transaction history within its scope
- All asset types and amounts
- Current shielded balances
- Counterparty addresses for transactions within scope

### What a Viewing Key Cannot Do

- Spend funds
- View other users' activity
- Modify or selectively omit transactions within its scope
- Reveal activity outside the scoped time period

A viewing key is derived from the master key through a separate derivation path than the spending key (see [Privacy Architecture — Key Hierarchy](./03-privacy-architecture.md#key-hierarchy)). Sharing it gives read-only access without any spending authority.

This is functionally equivalent to a bank providing account statements to a regulator: per-user visibility without requiring the entire system's records to be public.

### Scoping

Viewing keys can be time-bounded. An owner sharing a key with a tax accountant can restrict it to a specific fiscal year. An owner responding to a legal process can scope the key to the transactions in question. The scoped key proves the data is authentic because it derives from the on-chain state, not from a user-constructed report.

### User-Controlled Disclosure

Disclosure is always initiated by the user. Only the owner holds the viewing key, and the protocol has no backdoor or master key that bypasses this. This is the same architectural property that prevents blanket surveillance.

---

## How This Works for Exchanges

The practical test of any compliance framework: can exchanges list the token?

Z's model: private on-chain, regulated at the exchange boundary. Users who want exchange access provide the disclosures exchanges require. Users who stay peer-to-peer retain full privacy. The same model as cash.

**Deposit flow.** A user withdrawing from the MASP to deposit at an exchange shares a scoped viewing key covering the relevant transactions. The exchange uses this to verify the source of funds for its transaction monitoring obligations. The viewing key scope is narrow: it covers the deposit transaction and enough history to satisfy the exchange's compliance team.

**Travel Rule.** For transfers above jurisdiction-specific thresholds, the exchange attaches Travel Rule metadata (originator/beneficiary information) using standard protocols (TRISA, Sygna, Notabene). This happens at the exchange layer. Z does not embed Travel Rule data on-chain. The exchange handles it as they would for any other protocol.

**Compliance oracle attestation (optional).** Instead of sharing raw viewing keys with every exchange, a user can obtain a compliance oracle attestation. The user generates a ZK proof against a published compliance circuit (such as "no interactions with sanctioned addresses"). The oracle verifies the proof cryptographically and issues a signed attestation. The exchange trusts the oracle's signing key and accepts the attestation in place of direct viewing key inspection. This scales better for active traders who use multiple exchanges. The oracle never sees the underlying transaction data.

---

## Emergency Controls

**Upgrade timelock.** Any modification to MASP contracts, verification rules, compliance provider addresses, or system parameters requires a mandatory 48-hour on-chain waiting period. This gives users time to review changes or withdraw funds before new logic takes effect.

**Two-step ownership transfer.** All compliance-related contracts (ASPManager, DepositScreener, AssociationSetProver) use a two-step ownership transfer pattern. The current owner initiates the transfer. The new owner must explicitly accept. This prevents accidental or malicious ownership changes.

**ASP management.** ASPs can be added, removed, or have their staleness thresholds adjusted by the contract owner. Removing an ASP deactivates it but does not delete its historical data. Deactivating an association set prevents further updates to that set but preserves existing proofs against it.

---

## Design Properties

These properties are enforced by the architecture.

**No surveillance backdoor.** No master key exists that can decrypt all MASP transactions. Even protocol developers cannot view shielded transactions without a user's viewing key.

**No asset freezing inside the MASP.** The protocol has no mechanism to freeze individual addresses or balances within the pool. Individual token contracts may include issuer-controlled freeze functions (USDT, for example), but this is a property of the token, not Z.

**Transaction finality.** All transactions are final. No undo or clawback mechanism exists.

**Counterparty privacy.** A viewing key reveals the holder's own activity but does not identify their counterparties. Both parties must independently provide viewing keys for a complete picture of any transaction.

**Forward-only compliance.** Entry screening checks wallets at the time of deposit. ASP updates block withdrawals to subsequently-sanctioned addresses.

---

## Summary

| Question | Answer |
|----------|--------|
| How does Z handle privacy? | Privacy is a native option via the MASP. Users choose when to shield assets. |
| Can anyone deposit into the MASP? | No. Source wallets are screened against sanctions lists through ASPs. Flagged wallets are rejected. |
| Can assets exit the MASP without screening? | No. Withdrawal addresses are screened against ASP sanctions lists. Flagged addresses are rejected. |
| Can regulators see a user's transactions? | Yes, via a user-provided viewing key (time-scopable, read-only). |
| Can regulators see all transactions? | No. No master key or backdoor exists. Visibility is per-user via viewing keys. |
| Can the system freeze a user's funds? | No. There is no protocol-level mechanism to freeze individual assets inside the MASP. |
| Can transactions be reversed? | No. All transactions are final. |
| Does the system screen fund origin at withdrawal? | No. The system screens the destination address. |

For how the privacy mechanisms work at a technical level, see [Privacy Architecture](./03-privacy-architecture.md). For the consensus mechanism that secures Z, see [Consensus (ZSP)](./04-consensus-zsp.md).
