---
name: Charge a purchase to a subscriber's mobile account with Carrier Billing
description: Use the CAMARA Carrier Billing API to prepare, create, validate and confirm a payment charged to the subscriber's mobile bill, cancel it when needed, and reconcile through status notifications — the payments leg of Open Gateway, and the one with no idempotency contract.
api: openapi/camara-carrier-billing-openapi.yml
api_version: 0.5.0
operations:
  - preparePayment
  - createPayment
  - validatePayment
  - confirmPayment
  - retrievePayment
  - retrievePayments
  - cancelPayment
scopes:
  - carrier-billing:payments:create
  - carrier-billing:payments:read
  - carrier-billing:payments:write
generated: '2026-07-25'
method: generated
---

# Charge a purchase to a subscriber's mobile account with Carrier Billing

Carrier Billing charges a purchase to the subscriber's mobile account instead of a card. It is the
only money-moving API in Open Gateway, it is still a v0.x initial version, and it has no idempotency
contract — so the retry discipline below is not optional.

## Before you start

- `servers[0].url` is `{apiRoot}/carrier-billing/v0.5`.
- Auth is OpenID Connect with a three-legged access token. Charging someone's bill requires their
  consent; there is no two-legged path for creating a payment.
- Send `x-correlator` on every call and persist the echoed value with your payment record. With no
  idempotency key, the correlator plus your own order reference is your reconciliation trail.
- Optional: supply `sink` and `sinkCredential` to receive CloudEvents payment status notifications.

## Steps

1. **Check feasibility first — `preparePayment`.**
   `POST {apiRoot}/carrier-billing/v0.5/payments/prepare`. Use it to confirm the subscriber can be
   charged the requested amount before you commit anything user-visible. This is the cheapest place
   to discover that the line is prepaid with insufficient balance or outside its spending limit.

2. **Create the payment — `createPayment`.**
   `POST {apiRoot}/carrier-billing/v0.5/payments` with the amount, currency, a description and your
   own reference. The response carries `paymentId` and the payment state.

   **Persist `paymentId` before doing anything else.** CAMARA defines no `Idempotency-Key`, so a
   timed-out `createPayment` that you blindly retry can charge the subscriber twice. On any
   ambiguous failure, call `retrievePayments` (below) and match on your own reference before
   retrying.

3. **Validate where the operator requires it — `validatePayment`.**
   `POST {apiRoot}/carrier-billing/v0.5/payments/{paymentId}/validate`. Some operators interpose a
   subscriber validation step; where they do, this is the call that carries it.

4. **Confirm — `confirmPayment`.**
   `POST {apiRoot}/carrier-billing/v0.5/payments/{paymentId}/confirm` commits the charge. Do not
   deliver goods before you have a confirmed state.

5. **Cancel when you must — `cancelPayment`.**
   `POST {apiRoot}/carrier-billing/v0.5/payments/{paymentId}/cancel`. Cancellation is only valid in
   the states the operator permits; a confirmed charge is a refund conversation, not a cancel.

6. **Reconcile.**
   - `GET {apiRoot}/carrier-billing/v0.5/payments/{paymentId}` — `retrievePayment` for one payment.
   - `GET {apiRoot}/carrier-billing/v0.5/payments` — `retrievePayments`, paginated with `page` and
     `perPage` (default 20, max 100), returning a `pagination` object. This is your recovery call
     after any ambiguous write.

7. **Prefer notifications to polling.** Payment status notifications arrive as CloudEvents at your
   `sink`, authenticated with `sinkCredential` (`securitySchemes.notificationsBearerAuth`). See
   `asyncapi/open-gateway-webhooks.yml`.

## Errors

CAMARA `ErrorInfo` on `application/json` — `{status, code, message}`. Carrier Billing carries its own
`CARRIER_BILLING` code family across 400, 403, 409 and 422.

- `400 INVALID_ARGUMENT` / `400 CARRIER_BILLING` — malformed amount, currency or payment body.
- `400 INVALID_SINK` / `400 INVALID_CREDENTIAL` — bad notification sink or credential.
- `401 UNAUTHENTICATED`, `403 PERMISSION_DENIED`, `403 CARRIER_BILLING` — token, scope or consent.
- `404 NOT_FOUND` — unknown `paymentId`.
- `409 ALREADY_EXISTS` / `409 CARRIER_BILLING` / `409 ABORTED` — state conflict; re-read the payment
  rather than retrying the write.
- `422 CARRIER_BILLING` — the operator cannot charge this subscriber (limit, balance, or line type).
- `429 TOO_MANY_REQUESTS`, `429 QUOTA_EXCEEDED`.

Full catalogue: `errors/open-gateway-problem-types.yml`. CAMARA publishes no decline-code registry
for Carrier Billing; the `CARRIER_BILLING` code plus the message is all the detail the contract
gives you.

## Conventions that apply

- **No idempotency.** This is the single most important thing to know about writing against Open
  Gateway. Treat every write as at-most-once-by-your-own-discipline; see
  `conventions/open-gateway-conventions.yml`.
- v0.5 is an initial version. Expect breaking changes at CAMARA meta-release boundaries; see
  `changelog/open-gateway-changelog.yml`.
