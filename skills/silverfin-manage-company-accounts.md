---
name: Manage company accounts
description: List and maintain the ledger accounts of a Silverfin company.
api: Silverfin API v4
operations: [getcompanies, getcompanyaccounts, createnewaccount, updateaccountcontent, updatebatchaccountcontent]
scopes: [administration:read, administration:write]
---

# Manage company accounts

Use this skill to read and maintain the chart of accounts for a Silverfin company.

## Auth
OAuth 2.0 access token with `administration:read` (list/read) and `administration:write` (create/update). Calls are under `/api/v4/f/{firm_id}/companies/{company_id}`.

## Steps
1. Call `getcompanies` to list companies in the firm and select a `company_id`.
2. Call `getcompanyaccounts` to list the company's accounts.
3. To add an account, call `createnewaccount`. If you supply `custom_name_locale` without `original_name_locale`, Silverfin copies the value automatically; likewise `number` copies into `original_number`. Only locales enabled for the company are honoured.
4. To edit a single account, call `updateaccountcontent`; to edit many at once, call `updatebatchaccountcontent`.

## Rules
- Locale fields are filtered to the company's enabled locales; unused ones are ignored.
- `administration:write` is required for create/update; a missing scope returns 403.
- Validation failures return 422 with a JSON error body.
