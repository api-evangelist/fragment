---
name: Read account, historical, and period balances
description: Query current, historical, point-in-time, and period balances plus ledger lines from Fragment with cursor pagination.
api: graphql/fragment-schema.graphql
operations: [ledgerAccount, ledgerLine, ledgerEntryHistory]
---

# Read account, historical, and period balances

## Auth
OAuth2 client-credentials Bearer token against `https://api.fragment.dev`.

## Steps
1. `ledgerAccount` — read an account and its balances. Choose a consistency
   mode (e.g. strongly consistent parent balances) when you need read-after-
   write guarantees.
2. `ledgerLine` — list the individual debit/credit lines behind a balance;
   filter (e.g. `pathIn`, tag/metadata, `DateFilter.within`) and page with the
   cursor connection fields (`edges`/`nodes`/`pageInfo`).
3. `ledgerEntryHistory` — walk historical / point-in-time balance snapshots for
   audit and period reporting.

## Rules
- All list queries are Relay-style cursor connections: use `first`/`after` and
  read `pageInfo.hasNextPage` + `endCursor`; never assume offset paging.
- Reads may return `NotFoundError` — branch on `__typename`.
- Balances are multi-currency; respect the account's currency in amounts.
- See conventions/fragment-conventions.yml for pagination and consistency.
