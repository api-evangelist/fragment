---
name: Post a balanced, idempotent ledger entry
description: Store a schema, create a ledger, and post a double-entry ledger entry to Fragment safely with an idempotency key.
api: graphql/fragment-schema.graphql
operations: [storeSchema, createLedger, addLedgerEntry, ledgerAccount]
---

# Post a balanced, idempotent ledger entry

Fragment is a GraphQL double-entry ledger API. Every posting must balance
(debits == credits) against accounts defined by a stored schema.

## Auth
1. Create an API client (client_id/client_secret) in the Fragment dashboard.
2. Exchange them for a Bearer token via client-credentials at
   `https://auth.<region>.fragment.dev/oauth2/token` (audience
   `https://api-global.fragment.dev`). Tokens last 1 hour.
3. Send `Authorization: Bearer <access_token>` to `https://api.fragment.dev`.

## Steps
1. `storeSchema` — persist your declarative chart-of-accounts schema (defines
   the accounts and entry types you can post).
2. `createLedger` — create a ledger instance from the stored schema.
3. `addLedgerEntry` — post the entry. **Always pass a unique `ik` (idempotency
   key)**; it is scoped per-Ledger. If the same `ik` is retried, Fragment
   returns the original result with `isIkReplay: true` instead of double-posting.
4. `ledgerAccount` — read the affected account to confirm the resulting balance.

## Rules
- Branch on the response `__typename`: success result vs `BadRequestError` /
  `InternalError`. Use the error `retryable` flag.
- Retry on HTTP 429 and 5XX; then handle 4XX; then inspect the GraphQL `errors`
  array; then branch on `__typename`.
- Entries are immutable — correct mistakes with a reversal, never by editing.
- See conventions/fragment-conventions.yml and errors/fragment-error-codes.yml.
