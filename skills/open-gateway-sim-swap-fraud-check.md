---
name: Check a phone number for a recent SIM swap
description: Use the CAMARA SIM Swap API certified under GSMA Open Gateway to detect account-takeover risk before allowing a high-value action, either by asking whether a swap happened inside a window or by retrieving the last SIM change date.
api: openapi/camara-sim-swap-openapi.yml
api_version: 2.1.0
operations:
  - checkSimSwap
  - retrieveSimSwapDate
scopes:
  - sim-swap:check
  - sim-swap:retrieve-date
generated: '2026-07-25'
method: generated
---

# Check a phone number for a recent SIM swap

SIM Swap is the most commercially launched Open Gateway API. It answers one question: has the SIM
behind this mobile number been changed recently? A recent swap is a strong signal that an attacker
has taken over the number and that SMS-based recovery or one-time passwords cannot be trusted.

## Before you start

- There is no GSMA-hosted endpoint. You call an `apiRoot` supplied by a certified operator,
  aggregator or channel partner. The spec's `servers[0].url` is `{apiRoot}/sim-swap/v2`.
- Auth is OpenID Connect (`securitySchemes.openId`). Use a three-legged access token obtained by
  authorization code or CIBA, carrying `sim-swap:check` or `sim-swap:retrieve-date`.
- With a three-legged token the subscriber is implied by the token. Do **not** also send
  `phoneNumber` in the body — that returns `422 UNNECESSARY_IDENTIFIER`.
- Send an `x-correlator` header (UUID recommended). The provider must echo it on the response.

## Steps

1. **Decide which question you are asking.**
   - Risk gate with a fixed window: use `checkSimSwap`.
   - You need the actual timestamp for scoring or an audit trail: use `retrieveSimSwapDate`.

2. **Ask the yes/no question — `checkSimSwap`.**
   `POST {apiRoot}/sim-swap/v2/check` with a body carrying `maxAge` in hours (the lookback window,
   for example 240 for ten days) and, only when using a two-legged token, `phoneNumber` in E.164
   form with a leading `+`. The response returns `swapped` as a boolean.

3. **Or retrieve the date — `retrieveSimSwapDate`.**
   `POST {apiRoot}/sim-swap/v2/retrieve-date`. The response carries `latestSimChange` as an
   RFC 3339 timestamp. A null or absent value means the operator has no recorded swap.

4. **Act on the answer, do not leak it.**
   Treat `swapped: true` (or a `latestSimChange` inside your risk window) as a reason to step up
   verification or block the action. Never echo the swap date back to the end user — it is
   operator-held personal data disclosed to you under the subscriber's consent.

5. **Combine with a second signal for high-value flows.**
   Device Swap (`checkDeviceSwap`, `retrieveDeviceSwapDate` in
   `openapi/camara-device-swap-openapi.yml`) and Call Forwarding Signal
   (`retrieveUnconditionalCallForwarding`) are the usual companions. A SIM swap plus active
   unconditional call forwarding is the classic diversion-fraud pattern.

## Errors

Errors are CAMARA `ErrorInfo` objects on `application/json` — `{status, code, message}` — not
RFC 9457 problem documents. Handle at minimum:

- `400 INVALID_ARGUMENT` — malformed `phoneNumber` or an out-of-range `maxAge`.
- `401 UNAUTHENTICATED` — missing or expired access token.
- `403 PERMISSION_DENIED` — token lacks `sim-swap:check` / `sim-swap:retrieve-date`.
- `404 IDENTIFIER_NOT_FOUND` — the operator does not serve this number.
- `422 UNNECESSARY_IDENTIFIER` — you sent `phoneNumber` alongside a three-legged token.
- `422 SERVICE_NOT_APPLICABLE` — the operator cannot answer for this subscriber.
- `429 TOO_MANY_REQUESTS` / `429 QUOTA_EXCEEDED` — back off; there are no `RateLimit-*` headers to
  read, so use your own backoff schedule.

Full catalogue: `errors/open-gateway-problem-types.yml`.

## Conventions that apply

- No idempotency contract exists in CAMARA. These are read operations, so retry is safe, but do not
  assume a retry contract on any write operation. See `conventions/open-gateway-conventions.yml`.
- Portability is the point: the same request works against any certified operator once you change
  `apiRoot` and credentials.
