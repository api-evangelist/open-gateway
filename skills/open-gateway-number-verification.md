---
name: Verify a phone number against the network without an SMS
description: Use the CAMARA Number Verification API certified under GSMA Open Gateway to confirm that the number a user claims is the number of the SIM on the current mobile data connection, or to retrieve the network-asserted number, with no one-time password.
api: openapi/camara-number-verification-openapi.yml
api_version: 2.1.0
operations:
  - phoneNumberVerify
  - phoneNumberShare
scopes:
  - number-verification:verify
  - number-verification:device-phone-number:read
generated: '2026-07-25'
method: generated
---

# Verify a phone number against the network without an SMS

Number Verification replaces SMS one-time passwords with a silent check against the operator. The
network already knows which number the SIM on this data connection belongs to; this API lets you ask
whether it matches what the user typed, or lets the network tell you the number outright.

## Before you start

- `servers[0].url` is `{apiRoot}/number-verification/v2`; `apiRoot` comes from your operator,
  aggregator or channel partner.
- This API only works over the **mobile data connection** of the device being verified. Wi-Fi
  breaks the network binding. Drive the OIDC authorization request over cellular.
- Auth is OpenID Connect with a three-legged access token. The authorization step is what binds the
  token to the SIM, which is why there is no `phoneNumber` in the token-issuing leg.
- Send `x-correlator`; the provider echoes it.

## Steps

1. **Get a three-legged token over cellular.** Run the OIDC authorization code flow with scope
   `number-verification:verify` (to compare) or `number-verification:device-phone-number:read`
   (to be told the number). CIBA is the alternative where you cannot redirect the handset.

2. **Compare a claimed number — `phoneNumberVerify`.**
   `POST {apiRoot}/number-verification/v2/verify` with the number the user claimed, in E.164 form
   with a leading `+`, or its hashed form where the operator supports hashing. The response returns
   `devicePhoneNumberVerified` as a boolean. You learn only match or no-match; you never learn the
   real number on a mismatch.

3. **Or ask for the number — `phoneNumberShare`.**
   `GET {apiRoot}/number-verification/v2/device-phone-number` returns `devicePhoneNumber`, the
   network-asserted E.164 number. Use this only when you genuinely need the number (for example
   silent sign-up), because it discloses more than the boolean does.

4. **Prefer verify over share.** Data-minimisation is the reason this API has two operations. If a
   boolean answers your question, use `phoneNumberVerify`.

5. **Fall back deliberately.** When the device is on Wi-Fi or the operator is not certified for this
   API, fall back to One Time Password SMS (`sendCode`, `validateCode` in
   `openapi/camara-one-time-password-sms-openapi.yml`) rather than failing the user.

## Errors

CAMARA `ErrorInfo` on `application/json` — `{status, code, message}`.

- `400 INVALID_ARGUMENT` — the supplied `phoneNumber` is not valid E.164.
- `401 UNAUTHENTICATED` — missing or expired token.
- `403 INVALID_TOKEN_CONTEXT` — the token is not bound to the device making the request; almost
  always means the authorization did not happen over the mobile data connection.
- `403 NUMBER_VERIFICATION` / `403 PERMISSION_DENIED` — scope or consent missing.
- `422 SERVICE_NOT_APPLICABLE` — the operator cannot verify this subscriber.
- `429 TOO_MANY_REQUESTS` — back off.

Full catalogue: `errors/open-gateway-problem-types.yml`.

## Conventions that apply

- Three-legged token only; there is no two-legged mode for this API because consent is the point.
- No idempotency key exists in CAMARA, but both operations are safe to repeat.
- See `conventions/open-gateway-conventions.yml` and `scopes/open-gateway-scopes.yml`.
