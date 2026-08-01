---
name: Sync bookkeeping transactions into Silverfin
description: Push bookkeeping transactions from a third-party ledger into Silverfin using the SyncAPI incremental model.
api: Silverfin API v4 (SyncAPI)
operations: [getsyncsettings, getsynccompanies, setsyncreferences, setsynccompanysettings, submitsynctransactions, resetsyncbookkeepingdata]
scopes: [financials:transactions:sync]
---

# Sync bookkeeping transactions into Silverfin

Use this skill to synchronise transactions from a bookkeeping system into Silverfin.

## Auth
Obtain an OAuth 2.0 access token (Authorization Code grant) with the `financials:transactions:sync` scope. Tokens expire after 2 hours; refresh with the single-use refresh token. All calls are namespaced under `/api/v4/f/{firm_id}`.

## Steps
1. Call `getsyncsettings` to read platform limits — the maximum analytical dimensions per transaction, supported locales, and the maximum actions per request (500).
2. Call `getsynccompanies` to list the Silverfin companies configured for this sync app (paginated).
3. For a target company, call `setsyncreferences` to configure which third-party references map to the company, then `setsynccompanysettings` to configure financial years and whether Silverfin computes opening balances / retained earnings.
4. Call `submitsynctransactions` to add, update, or delete transactions. Send at most 500 actions per request. Submit one request at a time per company (the API enforces a per-company concurrency limit of 1 in-progress call) and use an incremental approach.
5. If data becomes inconsistent, call `resetsyncbookkeepingdata` with `after_date` set to the oldest changed date to re-sync from that point (omit the date to reset all data).

## Rules
- Serialize calls per `company_id`; never run two sync calls concurrently for the same company (429 otherwise).
- Respect the 500-actions-per-request cap; chunk larger batches.
- Handle 422 validation errors per action; 403 means the token lacks `financials:transactions:sync`.
