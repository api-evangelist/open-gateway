---
name: Verify or retrieve a device location from the network
description: Use the CAMARA Device Location Verification and Device Location Retrieval APIs to answer "is this device where it claims to be" without handset GPS or an installed SDK, preferring the privacy-preserving verification answer over raw coordinates.
api: openapi/camara-location-verification-openapi.yml
api_version: 3.0.0
also_uses: openapi/camara-location-retrieval-openapi.yml
operations:
  - verifyLocation
  - retrieveLocation
scopes:
  - location-verification:verify
  - location-retrieval:read
generated: '2026-07-25'
method: generated
---

# Verify or retrieve a device location from the network

The network knows roughly where a device is without asking the handset. Two APIs expose that: one
answers a yes/no question about an area you supply, the other returns the area itself. For fraud and
login checks, always reach for the first.

## Before you start

- `servers[0].url` is `{apiRoot}/location-verification/v3` and `{apiRoot}/location-retrieval/v0.5`.
- Auth is OpenID Connect with a three-legged access token; consent for location is explicit and
  operator-mediated. With a three-legged token the device is implied — do not send `device`.
- Send `x-correlator`.
- Network-derived location is approximate. Both APIs describe accuracy explicitly; treat the
  accuracy figure as part of the answer, not as noise.

## Steps

1. **Prefer verification — `verifyLocation`.**
   `POST {apiRoot}/location-verification/v3/verify` with an `area` object. The CAMARA area shape is
   its own, not GeoJSON: `areaType: CIRCLE` with a `center` (`latitude`, `longitude`) and a `radius`
   in metres. Optionally set `maxAge` in seconds to bound how stale a network fix you will accept.

   The response returns `verificationResult` — `TRUE`, `FALSE`, `PARTIAL` or `UNKNOWN` — plus
   `matchRate` for `PARTIAL` and `lastLocationTime`. You learn whether the device is inside your
   area, never where it actually is.

2. **Handle `PARTIAL` deliberately.** `PARTIAL` means the network's uncertainty region only partly
   overlaps your area. Decide up front whether your risk rule treats it as pass or fail; do not let
   it fall through as a generic failure.

3. **Retrieve coordinates only when you must — `retrieveLocation`.**
   `POST {apiRoot}/location-retrieval/v0.5/retrieve` returns the network-derived area as a circle or
   polygon with `lastLocationTime`. Use `maxAge` to control freshness. This is the higher-disclosure
   call: it is appropriate for logistics and dispatch, rarely for a login check.

4. **Widen the area to match reality.** Network location accuracy varies from a few hundred metres in
   dense urban cells to several kilometres in rural ones. A radius tuned for a city will produce
   false `FALSE` results elsewhere.

5. **Compose with roaming for travel-aware rules.** `getRoamingStatus` in
   `openapi/camara-device-roaming-status-openapi.yml` tells you whether the subscriber is abroad,
   which often explains a location mismatch better than fraud does.

## Errors

CAMARA `ErrorInfo` on `application/json` — `{status, code, message}`.

- `400 INVALID_ARGUMENT` — malformed area, or a radius below the operator's minimum.
- `400 OUT_OF_RANGE` — latitude/longitude or `maxAge` outside permitted bounds.
- `401 UNAUTHENTICATED`, `403 PERMISSION_DENIED` — token, scope or consent problem.
- `404 IDENTIFIER_NOT_FOUND` — the operator does not serve this device.
- `422 LOCATION_VERIFICATION` / `422 LOCATION_RETRIEVAL` — the operator cannot locate this device
  (device off, not attached, or location unsupported on this network).
- `422 UNNECESSARY_IDENTIFIER` — you sent `device` alongside a three-legged token.
- `429 TOO_MANY_REQUESTS`.

Full catalogue: `errors/open-gateway-problem-types.yml`.

## Conventions that apply

- Data minimisation is a design property here, not a suggestion. Verification exists so you do not
  have to hold coordinates.
- No idempotency contract; both operations are reads and safe to repeat.
- See `conventions/open-gateway-conventions.yml`.
