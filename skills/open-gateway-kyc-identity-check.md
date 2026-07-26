---
name: Check a customer's identity against operator-held data
description: Use the CAMARA Know Your Customer Match, Age Verification and Tenure APIs to test customer-supplied identity attributes, an age threshold and subscription longevity against what the mobile operator already holds, without the operator disclosing its own values.
api: openapi/camara-kyc-match-openapi.yml
api_version: 0.4.0
also_uses:
  - openapi/camara-kyc-age-verification-openapi.yml
  - openapi/camara-kyc-tenure-openapi.yml
operations:
  - KYC_Match
  - verifyAge
  - checkTenure
scopes:
  - kyc-match:match
  - kyc-age-verification:verify
  - kyc-tenure:check-tenure
generated: '2026-07-25'
method: generated
---

# Check a customer's identity against operator-held data

Mobile operators hold verified identity data for their subscribers. These three APIs let you test
what a customer told you against that record without the operator ever handing you its copy. Every
answer is a comparison result, not a disclosure.

## Before you start

- Endpoints are `{apiRoot}/kyc-match/v0.4`, `{apiRoot}/kyc-age-verification/v0.2` and
  `{apiRoot}/kyc-tenure/v0.2`. All three are still v0.x initial versions — expect breaking changes
  between CAMARA meta-releases.
- Auth is OpenID Connect with a three-legged access token. These are the most consent-sensitive APIs
  in Open Gateway; the subscriber authorises each scope explicitly.
- Send `x-correlator`.

## Steps

1. **Match supplied attributes — `KYC_Match`.**
   `POST {apiRoot}/kyc-match/v0.4/match` with the attributes the customer gave you: `name`,
   `givenName`, `familyName`, `nameKanaHankaku`/`nameKanaZenkaku` where relevant, `birthdate`,
   `address` and its components (`streetName`, `streetNumber`, `postalCode`, `locality`, `region`,
   `country`), `email`, `idDocument`, `houseNumberExtension`.

   The response returns a per-attribute result — `true`, `false` or `not_available` — for each
   attribute you sent. Send only the attributes you actually need checked; every extra field is
   personal data you asked the operator to process.

2. **Read `not_available` correctly.** It means the operator holds nothing for that attribute, not
   that the customer lied. Do not score it as a mismatch.

3. **Test an age threshold — `verifyAge`.**
   `POST {apiRoot}/kyc-age-verification/v0.2/verify` with `ageThreshold`. The response returns
   `ageCheck` as a boolean-style verdict — over or under the threshold — rather than a date of birth.
   This is the correct call for age-gating; never ask for `birthdate` through `KYC_Match` when a
   threshold answers the question.

4. **Test subscription longevity — `checkTenure`.**
   `POST {apiRoot}/kyc-tenure/v0.2/check-tenure` with the minimum duration you care about. The
   response tells you whether the subscription has been active at least that long. A number that was
   activated yesterday is a materially different onboarding risk from one active for five years.

5. **Compose a score, do not chain blindly.** Tenure plus a SIM Swap check (`checkSimSwap`) plus a
   partial KYC match is a far better signal than any single call, and costs three operator
   transactions — price that in.

## Errors

CAMARA `ErrorInfo` on `application/json` — `{status, code, message}`.

- `400 INVALID_ARGUMENT` — malformed attribute, bad date format, or an unrecognised property
  (CAMARA rejects undeclared JSON properties at any nesting level).
- `401 UNAUTHENTICATED`, `403 PERMISSION_DENIED` — token, scope or consent problem.
- `403 KNOW_YOUR_CUSTOMER` — the operator will not serve KYC for this subscriber.
- `404 IDENTIFIER_NOT_FOUND` — number not served by this operator.
- `422 SERVICE_NOT_APPLICABLE` — KYC data unavailable for this line (common on prepaid in markets
  without registration requirements).
- `422 MISSING_IDENTIFIER` / `422 UNNECESSARY_IDENTIFIER` — identifier sent inconsistently with the
  token leg.
- `429 TOO_MANY_REQUESTS`.

Full catalogue: `errors/open-gateway-problem-types.yml`.

## Conventions that apply

- These APIs return comparisons, never the operator's stored values. Design your UX so a `false`
  result asks the customer to re-enter rather than revealing what the operator holds.
- No idempotency contract; all three are reads.
- See `conventions/open-gateway-conventions.yml` and `scopes/open-gateway-scopes.yml`.
