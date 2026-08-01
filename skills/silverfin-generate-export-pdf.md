---
name: Generate an export PDF from a period
description: Create and download an export PDF instance asynchronously for a Silverfin period.
api: Silverfin API v4
operations: [getexportpdfslist, createexportpdfinstance, getexportpdfinstancecontent, moveexportpdfinstancetodocuments]
scopes: [financials:read, financials:write]
---

# Generate an export PDF from a period

Use this skill to render a Silverfin export PDF (e.g. a financial report) and retrieve the file.

## Auth
OAuth 2.0 access token with `financials:read` (list/read) and `financials:write` (create). Calls are under `/api/v4/f/{firm_id}/companies/{company_id}/periods/{period_id}`.

## Steps
1. Call `getexportpdfslist` to find the available export PDF styles/templates and choose an `export_pdf_id`.
2. Call `createexportpdfinstance` with the `export_pdf_id`. This is asynchronous: the response returns an `id` and a `state` of `pending`.
3. Poll `getexportpdfinstancecontent` until `state` becomes `created` (or `error`). On `created`, the response includes a `download_url` — a direct, time-limited link to the PDF file.
4. Optionally call `moveexportpdfinstancetodocuments` to file the generated PDF into the company documents.

## Rules
- The `download_url` expires after a short time — fetch promptly.
- Treat `error` state as a failed render; surface the reason and retry.
- Do not busy-poll; back off between polls.
