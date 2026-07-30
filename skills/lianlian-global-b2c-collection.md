---
name: Collect marketplace settlements and withdraw (B2C Services API)
description: Apply for an overseas receiving account, query inbound settlements and fund flows, and withdraw to
  a bank account using the signature-authenticated B2C Services API.
api: openapi/lianlian-global-llp-api-userapi-regular-openapi.yml
apis:
- openapi/lianlian-global-llp-api-userapi-regular-openapi.yml
operations: []
provider: LianLian Global
generated: '2026-07-19'
method: generated
---

# Collect marketplace settlements and withdraw (B2C Services API)

## When to use
Use this skill for e-commerce sellers collecting marketplace payouts (Amazon and similar) through LianLian Global and withdrawing them.

## Auth — this API does NOT use OAuth
The B2C Services API uses an API key **plus** a request signature.

1. Generate an RSA 2048 key pair and send `developer_name`, `developer_public_key` and an optional `webhook_url` to `dev_support@lianlianpay.com`. LianLian returns `developer_id`, `master_token` and its own `llp_public_key`.
2. Every request carries both headers:

```
Authorization: Basic <base64(developerId:masterToken)>
LLPAY-Signature: t=<epoch_seconds>,v=<base64(RSA_SHA256(payload, your_private_key))>
```

3. The signature payload is `HTTP_METHOD&URI&REQUEST_EPOCH&REQUEST_PAYLOAD`, e.g.
   `POST&/api/mkt/balance&1533715688&{"currency":"USD"}`.
4. Verify LianLian's response and webhook signatures with `llp_public_key`.

Hosts: sandbox `https://gtest-open-api.lianlianpay-inc.com`, production `https://global-open-api.lianlianpay.com`.

## Steps
Ground every call in the operations published in `openapi/lianlian-global-llp-api-userapi-regular-openapi.yml` — this Swagger 2.0 document does not declare `operationId`s, so address operations by method and path:

1. **Apply for an overseas receiving account** — `POST /vcard/apply` and the account-application operations under the regular user API. Supported regions are enumerated in the spec (`US`, `CA`, `EU`, `UK`, `JP`, `AU`, `SG`, `AE`, `ID`, `IN`).
2. **Check KYC** with the user KYC query operation before expecting money movement.
3. **Query settlements and fund flows** — the fund-flow and settlement query operations return Amazon settlement sub-site and original-currency amounts.
4. **Withdraw** — the user withdrawal and currency-account withdrawal operations. Both enforce a per-transaction upper limit and return a dedicated error code when exceeded.

## Rules
- Read the operation list from the spec before calling — never invent a path.
- HTTPS only; unsigned or unauthenticated requests fail.
- Webhook callbacks are signed the same way and must be verified with `llp_public_key`.
- Consult `conventions/lianlian-global-conventions.yml` for pagination (`page_num` / `page_size`) and `errors/lianlian-global-problem-types.yml` for the error envelope.
