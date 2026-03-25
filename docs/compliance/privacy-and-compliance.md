---
title: "Privacy & Compliance"
keywords: ['compliance', 'OFAC', 'sanctions', 'ASP', 'Association Set Provider', 'Privacy Pools', 'selective disclosure', 'viewing key', 'boundary enforcement', 'exchange']
ai_summary: "Z's compliance model: private on-chain, regulated at the boundary. Entry: deposits screened against OFAC/sanctions lists via ASPs (Association Set Providers). ASPManager iterates all active ASPs, checks isApproved(wallet) and lastUpdated within staleness threshold (default 24h). Rejects if any ASP returns unapproved or stale. Withdrawal: recipient address screened against same ASP lists. Privacy Pools: AssociationSetProver — ASPs publish Merkle roots of compliant deposit sets, users generate ZK proof of membership (proves funds from screened group without revealing which deposit). Selective disclosure: viewing keys — read-only, time-scopable, user-controlled. For exchanges: scoped viewing key + Travel Rule (at exchange layer, not on-chain). Compliance oracle attestation (optional): user generates ZK proof against compliance circuit, oracle issues signed attestation, exchange accepts without raw viewing key. Emergency controls: 48h timelock on contract modifications, two-step ownership transfer."
---

# Privacy & Compliance

Z is a privacy protocol. The MASP severs the connection between on-chain activity and identity, and the protocol is permissionless.

Z also recognizes the need for compliant privacy features — particularly for users and institutions that operate within regulatory frameworks. The model: private on-chain, regulated at the boundary.

---

## The compliance model

Z's compliance model controls MASP entry and exit without monitoring activity inside the pool. The protocol identifies which external addresses are requesting deposit authorization and which external addresses are specified as withdrawal destinations.

The inside of the pool is not surveilled. Entry and exit are screened.

---

## Entry screening

Before any deposit into the MASP, the source wallet is checked against OFAC sanctions lists and watchlists through Association Set Providers (ASPs). Flagged wallets are rejected and the deposit fails. The check is deterministic and protocol-enforced.

ASPs are third-party compliance services that maintain curated lists of screened wallet addresses. Multiple ASPs can be registered simultaneously. The ASPManager contract iterates through all active providers and requires approval from each one. ASP data must be current within a configurable staleness threshold (default: 24 hours). If an ASP's data is stale, the deposit is rejected until the provider refreshes.

```
_screenWallet(wallet)
  → for each active ASP:
       check isApproved(wallet)
       check lastUpdated within staleness threshold
       reject if either fails
```

**Geographic restrictions.** Wallet-level sanctions screening is protocol-enforced. Geographic restrictions (blocking users in sanctioned jurisdictions) are enforced at the application layer through IP-based methods and user attestation — not at the protocol level.

---

## Withdrawal screening

At withdrawal, the recipient address is screened against the same ASP sanctions lists used at entry. If the destination address is flagged, the withdrawal transaction is rejected by the smart contract.

The system screens the destination address, not the origin of the funds within the MASP. This is an address-level compliance check. It catches withdrawals to known-bad addresses but does not trace fund provenance inside the pool.

---

## Privacy Pools

Z implements the Privacy Pools model for withdrawal compliance through the AssociationSetProver contract. This provides a stronger compliance signal than address screening alone.

ASPs curate sets of compliant deposits and publish a Merkle root of each set. At withdrawal, a user can generate a ZK proof of membership in an approved set — proving their deposit belongs to a screened group without revealing which specific deposit is theirs.

The contract supports multiple ASPs, each managing multiple association sets. Sets are versioned with historical root tracking to prevent race conditions during ASP updates. ASPs can create, update, and deactivate sets. Deactivating a set preserves existing proofs against it.

In production, membership verification uses ZK proofs. The user proves: "my deposit is a member of this association set" without revealing their position in the set. The verifier learns that the deposit passed screening. Nothing else.

---

## Selective disclosure

Users can share a viewing key with regulators, auditors, or counterparties to provide read-only access to their shielded transaction history.

**A viewing key reveals:** complete transaction history within its scope, all asset types and amounts, current shielded balances.

**A viewing key cannot:** spend funds, view other users' activity, modify or selectively omit transactions within scope.

Viewing keys can be time-bounded. An owner sharing a key with a tax accountant can restrict it to a specific fiscal year. An owner responding to a legal inquiry can scope the key to the transactions in question. The scoped key proves the data is authentic — it derives from on-chain state, not from a user-constructed report.

For the full viewing key reference, see [Selective Disclosure](/docs/compliance/selective-disclosure).

---

## How this works for exchanges

The practical test: can exchanges list the protocol?

Z's model — private on-chain, regulated at the exchange boundary — provides the mechanism:

**Deposit flow.** A user withdrawing from the MASP to deposit at an exchange shares a scoped viewing key covering the relevant transactions. The exchange uses this to verify the source of funds. The viewing key scope is narrow: it covers the deposit transaction and enough history to satisfy the compliance team.

**Travel Rule.** For transfers above jurisdiction-specific thresholds, the exchange attaches Travel Rule metadata (originator/beneficiary information) using standard protocols (TRISA, Sygna, Notabene). This happens at the exchange layer. Z does not embed Travel Rule data on-chain.

**Compliance oracle attestation (optional).** Instead of sharing raw viewing keys with every exchange, a user can obtain a compliance oracle attestation. The user generates a ZK proof against a published compliance circuit (such as "no interactions with sanctioned addresses"). The oracle verifies the proof cryptographically and issues a signed attestation. The exchange trusts the oracle's signing key and accepts the attestation in place of direct viewing key inspection. This scales better for active traders using multiple exchanges.

---

## Emergency controls

**Upgrade timelock.** Any modification to MASP contracts, verification rules, compliance provider addresses, or system parameters requires a mandatory 48-hour on-chain waiting period. Users have time to review changes or withdraw funds before new logic takes effect.

**Two-step ownership transfer.** All compliance-related contracts (ASPManager, DepositScreener, AssociationSetProver) use a two-step ownership transfer. The current owner initiates. The new owner must explicitly accept.

**ASP management.** ASPs can be added, removed, or have their staleness thresholds adjusted. Removing an ASP deactivates it for future screening but preserves historical data.

---

## Design properties

These cannot be overridden by protocol operators, developers, or governance.

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
| Can assets exit the MASP without screening? | No. Withdrawal addresses are screened against ASP sanctions lists. |
| Can regulators see a user's transactions? | Yes, via a user-provided viewing key (time-scopable, read-only). |
| Can regulators see all transactions? | No. No master key or backdoor exists. |
| Can the system freeze a user's funds? | No. There is no protocol-level mechanism to freeze assets inside the MASP. |
| Can transactions be reversed? | No. All transactions are final. |
| Does the system screen fund origin at withdrawal? | No. The system screens the destination address. |
