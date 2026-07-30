---
name: Open a receiving account and settle a deposit
description: Create and activate a LianLian Global account, open a global receiving account, and confirm inbound
  deposits including the ONWAY and REQUIRES_ACTION release paths.
api: openapi/lianlian-global-lppe-accounts-openapi.yml
apis:
- openapi/lianlian-global-lppe-accounts-openapi.yml
- openapi/lianlian-global-lppe-receiving-accounts-openapi.yml
- openapi/lianlian-global-lppe-deposit-openapi.yml
operations:
- create-account
- get-account
- accounts-authorize
- submit-account
- get-accounts-captchas-phone
- deposit_id
provider: LianLian Global
generated: '2026-07-19'
method: generated
---

# Open a receiving account and settle a deposit

## When to use
Use this skill to onboard a customer onto LianLian Global and start collecting funds into a global receiving account.

## Auth
OAuth 2.0 as in the payout skill. Third-party applications acting for a user must use the `authorization_code` flow (authorize at `https://global.lianlian.com/account/#/application-center/auth` with `response_type=code`, `client_id`, `redirect_uri`, `state` and `scope`, then exchange the code at `/api/v1/oauth2/token`).

## Steps
1. **Create the account.** `create-account` (`POST /accounts`).
2. **Verify the phone.** `get-accounts-captchas-phone` sends a captcha code to the account phone number.
3. **Authorize.** `accounts-authorize` (`POST /accounts/authorize`).
4. **Submit for activation.** `submit-account` (`POST /accounts/submit`), then read state with `get-account` (`GET /accounts/detail`).
5. **Wait for activation.** Consume `lppe.account.active` / `lppe.accounts.status.active`. Handle `lppe.accounts.status.require_action`, `.suspended` and `.closed` — a `SUSPENDED` account has outbound transactions rejected.
6. **Open the receiving account.** `POST /receiving_accounts` (this operation ships with an empty `operationId` in the spec — address it by path). Wait for `lppe.receiving_accounts.succeeded`; on `lppe.receiving_accounts.failed` the application must be resubmitted.
7. **Settle deposits.** On `lppe.receiving_accounts.deposit.requires_action`, call `deposit_id` (`POST /deposits/{deposit_id}`) to confirm the deposit and upload review materials. On `.onway`, call the same endpoint with a `RELEASE` request to complete settlement. `.succeeded`, `.pending` and `.refund` are the other terminal or attention states.

## Rules
- Compliance can interrupt any step: `lppe.compliance.requires_action` means you must submit material via the compliance endpoint before the flow continues; watch `failure_code` / `failure_reason` on `lppe.compliance.failed`.
- Send a unique `request_id` per call and an `Idempotency-Key` header on writes.
- Currency ISO 4217, country ISO 3166-2, timestamps epoch milliseconds.
