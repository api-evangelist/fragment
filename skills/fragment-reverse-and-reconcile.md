---
name: Reverse an entry and reconcile an external transaction
description: Reverse a posted ledger entry and reconcile an ingested external transaction against the Fragment ledger.
api: graphql/fragment-schema.graphql
operations: [reverseLedgerEntry, reconcileTx, tx]
---

# Reverse an entry and reconcile an external transaction

## Auth
Use an OAuth2 client-credentials Bearer token against
`https://api.fragment.dev` (see authentication/fragment-authentication.yml).

## Reverse a posted entry
1. `reverseLedgerEntry` — post the balanced inverse of an existing entry.
   Pass a unique `ik`; reversals are idempotent and replay-safe
   (`isIkReplay`). Never mutate the original immutable entry.

## Reconcile an external transaction
1. `tx` — read the external transaction (from a Stripe / Increase / Unit link
   or a custom-synced source) you want to reconcile.
2. `reconcileTx` — match that external transaction to the ledger entry/entries
   that represent it, closing the reconciliation loop between Fragment and the
   external system.

## Rules
- Every write takes an `ik`; retries with the same key are safe.
- Branch on `__typename` for `ReverseLedgerEntryResponse` /
  `ReconcileTxResponse` (result vs `BadRequestError` / `InternalError`).
- See data-model/fragment-data-model.yml for the Tx <-> Link <-> Ledger graph.
