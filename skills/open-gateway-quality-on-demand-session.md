---
name: Raise network quality for a device with a Quality on Demand session
description: Discover the QoS profiles an operator publishes, open a temporary Quality on Demand session for a device and application flow, monitor it through CloudEvents notifications, extend it, and terminate it cleanly.
api: openapi/camara-quality-on-demand-openapi.yml
api_version: 1.1.0
also_uses: openapi/camara-qos-profiles-openapi.yml
operations:
  - retrieveQoSProfiles
  - getQosProfile
  - createSession
  - getSession
  - extendQosSessionDuration
  - retrieveSessionsByDevice
  - deleteSession
scopes:
  - qos-profiles:read
  - quality-on-demand:sessions:create
  - quality-on-demand:sessions:read
  - quality-on-demand:sessions:update
  - quality-on-demand:sessions:delete
  - quality-on-demand:sessions:retrieve-by-device
generated: '2026-07-25'
method: generated
---

# Raise network quality for a device with a Quality on Demand session

Quality on Demand is the commercial front door to 5G differentiated connectivity. You ask the
operator to prioritise a specific flow — one device talking to one application server — for a bounded
period. Sessions are temporary by design and must be cleaned up.

## Before you start

- `servers[0].url` is `{apiRoot}/quality-on-demand/v1`; QoS Profiles is `{apiRoot}/qos-profiles/v1`.
- Auth is OpenID Connect. A three-legged token implies the device; do not also send the `device`
  object or you will get `422 UNNECESSARY_IDENTIFIER`.
- Send `x-correlator` on every call and log the echoed value against the session.

## Steps

1. **Discover what the operator actually sells — `retrieveQoSProfiles`.**
   `POST {apiRoot}/qos-profiles/v1/retrieve-qos-profiles`. Profile names are operator-specific;
   never hard-code a profile name across operators. Optionally filter by device.

2. **Inspect one profile — `getQosProfile`.**
   `GET {apiRoot}/qos-profiles/v1/qos-profiles/{name}` returns the profile's performance
   characteristics — latency, jitter and throughput targets — plus `minDuration` and `maxDuration`,
   which bound what you may request in step 3.

3. **Open the session — `createSession`.**
   `POST {apiRoot}/quality-on-demand/v1/sessions` with:
   - `device` (only with a two-legged token) — `phoneNumber`, `networkAccessIdentifier`,
     `ipv4Address` or `ipv6Address`;
   - `applicationServer` — the far end of the flow, `ipv4Address` and/or `ipv6Address`;
   - `devicePorts` / `applicationServerPorts` to narrow the flow;
   - `qosProfile` — the name from step 1;
   - `duration` in seconds, inside the profile's min/max;
   - `sink` and `sinkCredential` if you want status notifications.

   The response carries `sessionId`, `startedAt`, `expiresAt` and `qosStatus`.

   **There is no idempotency key in CAMARA.** A retried `createSession` can create a second billable
   session. Guard the call yourself: use `retrieveSessionsByDevice` before retrying, and record
   `sessionId` durably as soon as you have it.

4. **Receive status changes.** If you supplied `sink`, the operator POSTs CloudEvents notifications
   to it, authenticated with the bearer credential in `sinkCredential`
   (`securitySchemes.notificationsBearerAuth`). See `asyncapi/open-gateway-webhooks.yml`.

5. **Poll or reconcile.**
   - `GET {apiRoot}/quality-on-demand/v1/sessions/{sessionId}` — `getSession` for one session.
   - `POST {apiRoot}/quality-on-demand/v1/retrieve-sessions` — `retrieveSessionsByDevice` for
     everything currently open for a device. This is your reconciliation and leak-detection call.

6. **Extend rather than re-create — `extendQosSessionDuration`.**
   `POST {apiRoot}/quality-on-demand/v1/sessions/{sessionId}/extend` with `requestedAdditionalDuration`.
   Extending keeps one billable session instead of opening a second.

7. **Always terminate — `deleteSession`.**
   `DELETE {apiRoot}/quality-on-demand/v1/sessions/{sessionId}`. Sessions expire on their own, but
   leaving them open until `expiresAt` is usually a billing decision you did not intend to make.

## Errors

CAMARA `ErrorInfo` on `application/json` — `{status, code, message}`.

- `400 INVALID_ARGUMENT` — bad device or application server identifiers, or a malformed profile name.
- `400 OUT_OF_RANGE` — `duration` outside the profile's `minDuration`/`maxDuration`.
- `400 INVALID_SINK` / `400 INVALID_CREDENTIAL` — the notification sink or its credential is unusable.
- `401 UNAUTHENTICATED`, `403 PERMISSION_DENIED` — token or scope problem.
- `404 NOT_FOUND` — unknown `sessionId`.
- `409 QUALITY_ON_DEMAND` / `409 CONFLICT` — an overlapping session already exists for this flow.
- `422 SERVICE_NOT_APPLICABLE` — the operator cannot apply QoS to this subscriber or flow.
- `429 TOO_MANY_REQUESTS`, `503 UNAVAILABLE`.

Full catalogue: `errors/open-gateway-problem-types.yml`.

## Conventions that apply

- Profile names, durations and pricing are operator-specific. Portability lives in the contract
  shape, not in the values.
- See `conventions/open-gateway-conventions.yml` for `x-correlator`, the closed request contract
  (undeclared properties are rejected with `400 INVALID_ARGUMENT`), and the absence of an
  idempotency contract.
