---
title: "Selective Disclosure (Viewing Keys)"
keywords: ['viewing key', 'selective disclosure', 'compliance', 'audit', 'tax', 'regulator', 'privacy', 'MASP', 'key derivation']
ai_summary: "Viewing keys provide read-only access to a user's MASP transaction history without spending authority. Derived: viewingKey = Poseidon(masterKey, 1). Viewing key reveals: complete transaction history in scope, all asset types and amounts, current shielded balances, counterparty addresses for transactions in scope. Viewing key cannot: spend funds, view other users' activity, modify or omit transactions in scope, reveal activity outside scoped period. Scoping: keys can be time-bounded (fiscal year, specific date range). Protocol has no master key — no one can produce a viewing key for a user who hasn't generated one. Counterparty privacy: a viewing key reveals holder's activity only, not counterparties' — both parties must independently provide keys for a complete bilateral picture."
---

# Selective Disclosure (Viewing Keys)

Users can share a viewing key with regulators, auditors, or counterparties to provide read-only access to their shielded transaction history. Disclosure is always initiated by the user. The protocol has no backdoor.

---

## What a viewing key is

A viewing key is derived from the master key through a separate derivation path from the spending key:

```
masterKey (random 256-bit)
  |
  +-- spendingKey = Poseidon(masterKey, 0)   ← authorizes spending
  |
  +-- viewingKey  = Poseidon(masterKey, 1)   ← read-only access
```

Sharing your viewing key gives the recipient read-only access to your transaction history within the scoped period. It does not give them spending authority. It does not expose the master key.

---

## What a viewing key reveals

Within its scope:

- Complete list of MASP transactions (sends and receives)
- Asset type for each transaction
- Amount for each transaction
- Timestamp and block height for each transaction
- Current shielded balance for each asset
- Counterparty addresses for transactions in scope

---

## What a viewing key cannot do

- Spend funds — spending requires the spending key, which derives from a separate path
- View other users' activity — the viewing key decrypts only transactions involving your notes
- Modify or selectively omit transactions within scope — the output derives from on-chain state, it cannot be edited
- Reveal activity outside the scoped time period

---

## Scoping

Viewing keys can be time-bounded to specific periods or transaction sets.

**Fiscal year (for tax reporting):**
Generate a viewing key scoped to January 1 – December 31 of the relevant year. Share it with your accountant. They can verify your full transaction history for that year without seeing anything outside it.

**Specific transactions (for legal compliance):**
Scope the key to the relevant transaction window. The scoped key proves the data is authentic — it derives from on-chain state, not from a user-constructed report.

**Full history (for institutional auditors):**
No time restriction. Reveals complete activity from wallet creation to present.

---

## User-controlled disclosure

Disclosure is always initiated by the user. Only the owner holds the viewing key. The protocol has no backdoor and no master key that bypasses this.

This means:
- Z developers cannot view any user's shielded transactions
- Regulators cannot compel Z to produce viewing keys — the protocol has no such capability
- The only path to transaction history is through a viewing key provided by the user

This is not a policy choice. It is an architectural property enforced by the key derivation scheme.

---

## Counterparty privacy

A viewing key reveals your own activity — sends and receives involving your notes. It does not reveal the identity of your counterparties.

If you sent 100 USDZ to another user, your viewing key shows: "I sent 100 USDZ." It does not show who the recipient is. The recipient would need to share their own viewing key for the recipient side of that transaction to be visible.

This is by design. Privacy is bilateral. You control your disclosures. Your counterparty controls theirs.

---

## Compliance use cases

| Use case | What to scope | Who receives the key |
|----------|--------------|---------------------|
| Annual tax reporting | Fiscal year | Accountant |
| Exchange deposit verification | Period covering the deposit | Exchange compliance team |
| Regulatory inquiry | Transactions in question | Regulator |
| Institutional audit | Full history | Internal compliance team |
| Proof of payment | Single transaction | Counterparty |

For how exchanges use viewing keys in the deposit verification flow, see [Privacy & Compliance](/docs/compliance/privacy-and-compliance#how-this-works-for-exchanges).
