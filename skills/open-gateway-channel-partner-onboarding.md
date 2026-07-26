---
name: Onboard and order Open Gateway APIs as a channel partner (TMF931)
description: Use the TM Forum TMF931 Open Gateway Operate API to browse an operator's API product catalogue, register an application owner and application, place an API product order, and subscribe to lifecycle events — the machine-readable path into Open Gateway.
api: openapi/tmforum-tmf931-open-gateway-onboarding-ordering-openapi.yml
api_version: 5.2.1
operations:
  - listApiProduct
  - retrieveApiProduct
  - createApplicationOwner
  - retrieveApplicationOwner
  - patchApplicationOwner
  - createApplication
  - retrieveApplication
  - patchApplication
  - createApiProductOrder
  - retrieveApiProductOrder
  - listApiProductOrder
  - createHub
  - listMonitor
  - retrieveMonitor
generated: '2026-07-25'
method: generated
---

# Onboard and order Open Gateway APIs as a channel partner (TMF931)

Everything else in Open Gateway is a service API. TMF931 is the *Operate* API: the standard way an
aggregator or channel partner onboards against an operator and orders the service APIs it wants to
resell. GSMA and TM Forum certify it jointly alongside the CAMARA service APIs. Twenty-two
operations, Apache-2.0 licensed.

## Before you start

- `servers[0].url` is `{apiRoot}/openGatewayOperateAPIOnboardingAndOrdering/v5/`, where `apiRoot`
  is the operator's TM Forum API root.
- The specification declares no `securitySchemes` — unlike the CAMARA APIs, TMF931 authentication is
  established in the commercial onboarding agreement with each operator, not in the spec. Confirm
  the scheme with the operator before you build.
- This is a business-to-business surface. There is no self-serve signup.

## Steps

1. **Browse what the operator sells — `listApiProduct`.**
   `GET {apiRoot}/.../apiProduct` returns the operator's API product catalogue: which CAMARA APIs
   are certified and offered in which markets, with their commercial terms. Use
   `retrieveApiProduct` (`GET /apiProduct/{id}`) for the detail on one product.

2. **Register the legal entity — `createApplicationOwner`.**
   `POST {apiRoot}/.../applicationOwner`. This is the party that will hold the contract. Read it
   back with `retrieveApplicationOwner` and amend with `patchApplicationOwner`. Approval is
   asynchronous — watch for `applicationOwnerApprovalStatusChangeEvent`.

3. **Register the application — `createApplication`.**
   `POST {apiRoot}/.../application`. The application is what receives credentials and what quotas
   attach to. `listApplication`, `retrieveApplication` and `patchApplication` manage it. Approval is
   again asynchronous — `applicationApprovalStatusChangeEvent`.

4. **Order the API products — `createApiProductOrder`.**
   `POST {apiRoot}/.../apiProductOrder` referencing the application and the products from step 1.
   Track it with `retrieveApiProductOrder` (`GET /apiProductOrder/{id}`) and `listApiProductOrder`.
   Orders move through states asynchronously; do not treat the 201 as fulfilment.

5. **Subscribe to lifecycle events — `createHub`.**
   `POST {apiRoot}/.../hub` registers your listener callback. The operator then POSTs to your
   `/listener/*` endpoints for `apiProductOrderCreateEvent`, `apiProductOrderStateChangeEvent`,
   `apiProductOrderAttributeValueChangeEvent`, the application and application-owner create and
   approval events, and `monitorStateChangeEvent`. Deregister with `hubDelete`; inspect with
   `hubGet`. This is the correct way to learn that an order completed — polling is the fallback.

6. **Watch long-running work — `listMonitor` / `retrieveMonitor`.** Monitor resources track
   asynchronous operations; `monitorStateChangeEvent` notifies on transitions.

7. **Repeat per operator.** TMF931 is what makes that repetition mechanical rather than bespoke —
   the same client works against every certified operator, which is the entire point of the Operate
   API layer.

## Errors

TMF931 follows TM Forum error conventions rather than the CAMARA `ErrorInfo` envelope. Expect
`400`, `401`, `403`, `404`, `405`, `409` and `500` shapes as declared per operation in the
specification. See `errors/open-gateway-problem-types.yml` for the cross-repo catalogue and note
which rows come from this spec.

## Conventions that apply

- Ordering and approval are asynchronous. Build for the hub/listener pattern from the start rather
  than bolting on polling later.
- There is no idempotency key. A retried `createApiProductOrder` can create a duplicate order —
  reconcile with `listApiProductOrder` before retrying.
- See `asyncapi/open-gateway-webhooks.yml` for the full listener event list and
  `conventions/open-gateway-conventions.yml` for the shared semantics.
