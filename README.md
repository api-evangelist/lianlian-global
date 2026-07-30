# LianLian Global

LianLian Global (连连国际) is the cross-border payments arm of LianLian DigiTech, providing licensed collection, disbursement, foreign exchange and card issuing rails for e-commerce sellers, marketplaces, travel platforms and B2B traders moving money between China and the rest of the world.

Its developer platform publishes 18 distinct API products on a Stoplight portal at [developer.lianlianglobal.com](https://developer.lianlianglobal.com), backed by 173 OpenAPI/Swagger specifications describing more than 550 operations.

## APIs

| Product | Operations | Docs |
| --- | --- | --- |
| B2C Services API (`llp-api`) | 47 | [docs](https://developer.lianlianglobal.com/docs/llp-api/4b402d5ab3edd-api-introduction-v1-2) |
| LPPE Platform API (`lppe`) | 73 | [docs](https://developer.lianlianglobal.com/docs/lppe/72b66de24898e-introduction) |
| B2B Cross-Border (`b2b-cross-border`) | 50 | [docs](https://developer.lianlianglobal.com/docs/b2b-cross-border/ZG9jOjQ1Mg-open-api) |
| Outbound Payout — Card (`outbound-payout-ka`) | 54 | [docs](https://developer.lianlianglobal.com/docs/outbound-payout-ka/4b402d5ab3edd-api-introduction) |
| Global Payout (`global-payout`) | 45 | [docs](https://developer.lianlianglobal.com/docs/global-payout/ZG9jOjQ1Mg-api-introduction) |
| e-Wallet OpenAPI (`e-wallet-openapi`) | 39 | [docs](https://developer.lianlianglobal.com/docs/e-wallet-openapi/72b66de24898e-introduction) |
| Outbound Payout Service | 37 | [docs](https://developer.lianlianglobal.com/docs/outbound-payout-service/4b402d5ab3edd-api-introduction) |
| B2B Inflow & Payout | 30 | [docs](https://developer.lianlianglobal.com/docs/B2B-inflow-payout/ZG9jOjQ1Mg-open-api) |
| Payout — Tuition | 29 | [docs](https://developer.lianlianglobal.com/docs/global-payout-tuition/4b402d5ab3edd-api-introduction) |
| Outbound Payout Verification | 27 | [docs](https://developer.lianlianglobal.com/docs/outbound-payout-verification/ztrytr7ruzey9-balance-api) |
| Cards Open API | 26 | [docs](https://developer.lianlianglobal.com/docs/cards-open-api/4b402d5ab3edd-introduction) |
| Outbound Payout (Bill Pay) | 26 | [docs](https://developer.lianlianglobal.com/docs/outbound-payout/4b402d5ab3edd-api-introduction) |
| Connect for OTA | 23 | [docs](https://developer.lianlianglobal.com/docs/connect-ota/4b402d5ab3edd-open-api) |
| Connect | 19 | [docs](https://developer.lianlianglobal.com/docs/connect/ydcoebflkrejd-connect-api) |
| Standard Remittance | 18 | [docs](https://developer.lianlianglobal.com/docs/standard-remittance/4b402d5ab3edd-api-introduction) |
| Payout FX | 6 | [docs](https://developer.lianlianglobal.com/docs/global-payout-fx/e13ca9b34d037-exchange) |
| LLG Payments v2 | 5 | [docs](https://developer.lianlianglobal.com/docs/llg-payments/4b402d5ab3edd-summary) |
| TripLink Balance | 4 | [docs](https://developer.lianlianglobal.com/docs/triplink/d04slm5ra253f-) |

## Authentication

Two models run side by side:

- **OAuth 2.0** on the LPPE platform surface — `client_credentials` and `authorization_code`, token at `/api/v1/oauth2/token`.
- **API key + RSA request signature** on the B2C and payout products — `Authorization: Basic base64(developerId:masterToken)` plus an `LLPAY-Signature: t=<epoch>,v=<signature>` header, SHA256WithRSA over `HTTP_METHOD&URI&REQUEST_EPOCH&REQUEST_PAYLOAD`.

Webhooks are signed the same way and carry `LLPAY-Hook-ID`, `LLPAY-Event-Name`, `LLPAY-Delivery-ID` and `LLPAY-Delivery-Time` headers, with a 16-attempt retry ladder from 10 seconds to 2 hours.

## Artifacts

| Directory | What it holds |
| --- | --- |
| `openapi/` | 173 harvested OpenAPI 3.1 / Swagger 2.0 specifications |
| `overlays/` | 18 OpenAPI Overlay 1.0.0 documents, one per API product |
| `authentication/`, `scopes/` | Security schemes and the documented OAuth scope format |
| `conventions/` | Idempotency, pagination, metadata, tracing, versioning, error envelope |
| `errors/` | Error envelope and the 13 published common error codes |
| `asyncapi/` | Webhook catalogue — 42 LPPE events, signature and retry contract |
| `skills/` | 2 provider-published Agent Skills (verbatim) + 3 generated flows |
| `packages/`, `cli/` | First-party SDKs from the LLGlobal GitHub org and the EW DevTool CLI |
| `sandbox/` | Sandbox/production host separation and credential provisioning |
| `changelog/`, `lifecycle/` | Dated change history and versioning posture |
| `conformance/`, `data-model/`, `mcp/`, `llms/`, `well-known/`, `security/`, `agentic-access/` | Standards posture, entity graph, candidate MCP surface, llms.txt, probe results |

## Links

- Website — https://www.lianlianglobal.com
- Developer portal — https://developer.lianlianglobal.com
- GitHub — https://github.com/LLGlobal
- Support — https://support.us.lianlianglobal.com/hc/en-us
- Sign up — https://account.lianlianglobal.com/signupguide

Backed by: hongshan
