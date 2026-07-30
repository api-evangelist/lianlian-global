---
name: Create and track a cross-border payout
description: Create a beneficiary, quote and book an FX conversion, create a payout on the LianLian Global LPPE
  platform API, then reconcile it from the payout webhooks.
api: openapi/lianlian-global-lppe-payouts-openapi.yml
apis:
- openapi/lianlian-global-lppe-payouts-openapi.yml
- openapi/lianlian-global-lppe-conversion-2-openapi.yml
operations:
- create_beneficiary
- listof_beneficiary
- update_beneficiary
- post_get_reference_rate
- post-quotes-get_conversion_rate
- post-conversions
- get-conversions
- create_payout
- get_payout
- get-payouts
- cancel_payout
provider: LianLian Global
generated: '2026-07-19'
method: generated
---

# Create and track a cross-border payout

## When to use
Use this skill to move money out of a LianLian Global account to an external bank beneficiary.

## Auth
LPPE uses OAuth 2.0. Get a token with `client_credentials`:

```
POST /api/v1/oauth2/token
{"grant_type":"client_credentials","client_id":"...","client_secret":"..."}
```

Send it as `Authorization: Bearer <access_token>`. Tokens are returned with `expires_in` (about 7 days); refresh by calling the same endpoint again. Base URL is `https://global-api.lianlian.com/api/v1` (production). All calls are HTTPS only.

## Steps
1. **Register the beneficiary.** `create_beneficiary` (`POST /beneficiaries`). Country and currency use ISO 3166-2 and ISO 4217 codes. Check existing beneficiaries first with `listof_beneficiary`; correct a rejected one with `update_beneficiary`.
2. **Wait for beneficiary approval.** The beneficiary is asynchronous — do not payout until the `lppe.beneficiary.succeeded` webhook arrives. `lppe.beneficiary.failed` and `lppe.beneficiary.blocked` are terminal for that attempt.
3. **Quote the FX.** `post_get_reference_rate` for an indicative rate, then `post-quotes-get_conversion_rate` for a bookable rate.
4. **Book the conversion.** `post-conversions` (`POST /conversions`), then poll `get-conversions` or wait for `lppe.conversion.result` (`status` becomes `SUCCEEDED` or `FAILED`).
5. **Create the payout.** `create_payout` (`POST /payouts`). Send a unique `request_id` in the body and an `Idempotency-Key` header so retries do not double-pay.
6. **Track it.** `get_payout` / `get-payouts`, or consume `lppe.payout.succeeded`, `lppe.payout.failed` and `lppe.payout.returned`. `RETURNED` happens *after* success when funds bounce back — handle it.
7. **Cancel if needed.** `cancel_payout` while the payout is still cancellable.

## Rules
- Attach your own identifiers with `metadata` (max 20 keys, key <=40 chars, value <=500 chars). Never put bank or card numbers in `metadata`.
- Timestamps are Unix epoch **milliseconds**.
- On any non-200, read `code`, `message`, `param` and `trace_id` from the body — see `errors/lianlian-global-problem-types.yml`. `trace_id` is valid for 15 days when contacting support.
- `LIMIT_ERROR` / HTTP 429 means back off; no rate-limit headers are published.
- Webhooks are at-least-once and unordered — de-duplicate on `LLPAY-Delivery-ID` and verify `LLPAY-Signature`.
