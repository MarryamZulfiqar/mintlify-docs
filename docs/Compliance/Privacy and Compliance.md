# Privacy & Compliance

Z is a privacy protocol. The ShieldedPool severs the connection between on-chain activity and identity, and the protocol is permissionless.

Z also recognizes the need for compliant privacy features, particularly for users and institutions that require compliance assurances and must operate within regulatory frameworks. Without violating the privacy principles of the system, certain components of the protocol, like ShieldedPool entry and exit, screen wallets against sanctions lists maintained by third-party Association Set Providers (ASPs).

Additionally, compliance features are available at the individual level. Viewing keys give users the ability to selectively disclose transaction history. Privacy Pools let users prove their funds are clean without revealing their identity. These and other compliance tools are illustrated below.

---

## Entry screening

Before any deposit into the ShieldedPool, the source wallet is checked against OFAC sanctions lists and watchlists through Association Set Providers (ASPs). Flagged wallets are rejected and the deposit fails. The check is deterministic and protocol-enforced.

ASPs are third-party compliance services that maintain curated lists of screened wallet addresses. Multiple ASPs can be registered simultaneously. The ASPManager contract iterates through all active providers and requires approval from each one. ASP data must be current within a configurable staleness threshold (default: 24 hours). If an ASP's data is stale, the deposit is rejected until the provider refreshes.

```
_screenWallet(wallet)
→ for each active ASP:
    check isApproved(wallet)
    check lastUpdated within staleness threshold
    reject if either fails
```

**For standard deposits:** The depositing wallet address is screened before tokens enter the ShieldedPool.

**For shielded minting (commitment-based deposits):** The screener operates on the source chain address, the address that locked stablecoins in the bridge contract. Only the commitment crosses the bridge to Z, but the screener has access to the source chain deposit address via the LayerZero message payload.

**Geographic restrictions.** Wallet-level sanctions screening is protocol-enforced. Geographic restrictions (blocking users in sanctioned jurisdictions) are enforced at the application layer through IP-based methods and user attestation, not at the protocol level.

---

## Withdrawal screening

At withdrawal, the recipient address is screened against the same ASP sanctions lists used at entry. If the destination address is flagged, the withdrawal transaction is rejected by the smart contract.

The system screens the destination address, not the origin of the funds within the ShieldedPool. This is an address-level compliance check. It catches withdrawals to known-bad addresses but does not trace fund provenance inside the pool.

---

## Privacy Pools

Z implements the Privacy Pools model for withdrawal compliance through the AssociationSetProver contract. This provides a stronger compliance signal than address screening alone.

ASPs curate sets of compliant deposits and publish a Merkle root of each set. At withdrawal, a user can generate a ZK proof of membership in an approved set, proving their deposit belongs to a screened group without revealing which specific deposit is theirs.

The contract supports multiple ASPs, each managing multiple association sets. Sets are versioned with historical root tracking to prevent race conditions during ASP updates. ASPs can create, update, and deactivate sets. Deactivating a set preserves existing proofs against it.

In production, membership verification uses ZK proofs. The user proves: "my deposit is a member of this association set" without revealing their position in the set. The verifier learns that the deposit passed screening. Nothing else.

An exchange or counterparty receives a ZK proof of Merkle membership, the association set ID being referenced, and the ASP that manages the set. They verify on-chain. The verification result is binary: the deposit is a member of the specified screened set, or it isn't.

---

## Selective disclosure (viewing keys)

Users can share a viewing key with regulators, auditors, or counterparties to provide read-only access to their shielded transaction history. Disclosure is always initiated by the user. The protocol has no backdoor.

A viewing key is derived from the master key through a separate derivation path from the spending key:

```
masterKey (random 256-bit)
  |
  +-- spendingKey = Poseidon(masterKey, 0) ← authorizes spending
  |
  +-- viewingKey = Poseidon(masterKey, 1) ← read-only access
```

Sharing your viewing key gives the recipient read-only access to your transaction history within the scoped period. It does not give them spending authority. It does not expose the master key.

**A viewing key reveals (within its scope):**

- Complete list of ShieldedPool transactions (sends and receives)
- Asset type and amount for each transaction
- Timestamp and block height for each transaction
- Current shielded balance for each asset
- Counterparty addresses for transactions in scope

**A viewing key cannot:**

- Spend funds (spending requires the spending key, which derives from a separate path)
- View other users' activity (the viewing key decrypts only transactions involving your notes)
- Modify or selectively omit transactions within scope (the output derives from on-chain state, it cannot be edited)
- Reveal activity outside the scoped time period

### Scoping

Viewing keys can be time-bounded to specific periods or transaction sets.

**Fiscal year (for tax reporting):** Generate a viewing key scoped to January 1 – December 31 of the relevant year. Share it with your accountant. They can verify your full transaction history for that year without seeing anything outside it.

**Specific transactions (for legal compliance):** Scope the key to the relevant transaction window. The scoped key proves the data is authentic because it derives from on-chain state, not from a user-constructed report.

**Full history (for institutional auditors):** No time restriction. Reveals complete activity from wallet creation to present.

### User-controlled disclosure

Only the owner holds the viewing key. The protocol has no backdoor and no master key that bypasses this. Z developers cannot view any user's shielded transactions. Regulators cannot compel Z to produce viewing keys, because the protocol has no such capability. The only path to transaction history is through a viewing key provided by the user. This is not a policy choice. It is an architectural property enforced by the key derivation scheme.

### Counterparty privacy

A viewing key reveals your own activity (sends and receives involving your notes). It does not reveal the identity of your counterparties. If you sent 100 USDZ to another user, your viewing key shows: "I sent 100 USDZ." It does not show who the recipient is. The recipient would need to share their own viewing key for the recipient side of that transaction to be visible. Privacy is bilateral. You control your disclosures. Your counterparty controls theirs.

### Compliance use cases

| Use case | What to scope | Who receives the key |
|---|---|---|
| Annual tax reporting | Fiscal year | Accountant |
| Exchange deposit verification | Period covering the deposit | Exchange compliance team |
| Regulatory inquiry | Transactions in question | Regulator |
| Institutional audit | Full history | Internal compliance team |
| Proof of payment | Single transaction | Counterparty |

---

## How this works for exchanges

The practical test: can exchanges list the protocol?

Z's model (private on-chain, regulated at the exchange boundary) provides the mechanism:

**Deposit flow.** A user withdrawing from the ShieldedPool to deposit at an exchange shares a scoped viewing key covering the relevant transactions. The exchange uses this to verify the source of funds. The viewing key scope is narrow: it covers the deposit transaction and enough history to satisfy the compliance team.

**Travel Rule.** For transfers above jurisdiction-specific thresholds, the exchange attaches Travel Rule metadata (originator/beneficiary information) using standard protocols (TRISA, Sygna, Notabene). This happens at the exchange layer. Z does not embed Travel Rule data on-chain.

**Compliance oracle attestation (optional).** Instead of sharing raw viewing keys with every exchange, a user can obtain a compliance oracle attestation. The user generates a ZK proof against a published compliance circuit (such as "no interactions with sanctioned addresses"). The oracle verifies the proof cryptographically and issues a signed attestation. The exchange trusts the oracle's signing key and accepts the attestation in place of direct viewing key inspection. This scales better for active traders using multiple exchanges.

---

## Emergency controls

**Upgrade timelock.** Any modification to ShieldedPool contracts, verification rules, compliance provider addresses, or system parameters requires a mandatory 48-hour on-chain waiting period. Users have time to review changes or withdraw funds before new logic takes effect.

**Two-step ownership transfer.** All compliance-related contracts (ASPManager, DepositScreener, AssociationSetProver) use a two-step ownership transfer. The current owner initiates. The new owner must explicitly accept.

**ASP management.** ASPs can be added, removed, or have their staleness thresholds adjusted. Removing an ASP deactivates it for future screening but preserves historical data.

---

## Design properties

These cannot be overridden by protocol operators, developers, or governance.

**No surveillance backdoor.** No master key exists that can decrypt all ShieldedPool transactions. Even protocol developers cannot view shielded transactions without a user's viewing key.

**No asset freezing inside the ShieldedPool.** The protocol has no mechanism to freeze individual addresses or balances within the pool. Individual token contracts may include issuer-controlled freeze functions (USDT, for example), but this is a property of the token, not Z.

**Transaction finality.** All transactions are final. No undo or clawback mechanism exists.

**Counterparty privacy.** A viewing key reveals the holder's own activity but does not identify their counterparties. Both parties must independently provide viewing keys for a complete picture of any transaction.

**Forward-only compliance.** Entry screening checks wallets at the time of deposit. ASP updates block withdrawals to subsequently-sanctioned addresses.

---

## Summary

| Question                                            | Answer                                                                                              |
| --------------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| How does Z handle privacy?                          | Privacy is a default option via the ShieldedPool. Users choose when to shield assets.               |
| Can anyone deposit into the ShieldedPool?           | No. Source wallets are screened against sanctions lists through ASPs. Flagged wallets are rejected. |
| Can assets exit the ShieldedPool without screening? | No. Withdrawal addresses are screened against ASP sanctions lists.                                  |
| Can regulators see a user's transactions?           | Yes, via a user-provided viewing key (time-scopable, read-only).                                    |
| Can regulators see all transactions?                | No. No master key or backdoor exists.                                                               |
| Can the system freeze a user's funds?               | No. There is no protocol-level mechanism to freeze assets inside the ShieldedPool.                  |
| Can transactions be reversed?                       | No. All transactions are final.                                                                     |
| Does the system screen fund origin at withdrawal?   | No. The system screens the destination address.                                                     |
