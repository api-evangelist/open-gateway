# GSMA Open Gateway (open-gateway)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

GSMA Open Gateway is the mobile industry's operator-commitment and certification layer for network APIs, run by the GSM Association from its London headquarters in the United Kingdom. Launched at MWC Barcelona on 27 February 2023 with 21 operator groups and eight universal network APIs, it is the go-to-market wrapper around CAMARA, the Linux Foundation-hosted Telco Global API Alliance that actually authors the OpenAPI definitions. GSMA sits above the value chain rather than in it: it signs operators and channel partners to a memorandum of understanding, certifies implementations against CAMARA service APIs and TM Forum Operate APIs, and publishes which certified APIs are live in which markets. Its API posture is honestly non-existent as a first-party surface. GSMA operates no callable API, publishes no OpenAPI under its own name, and runs no self-serve developer portal with keys or a sandbox. open-gateway.gsma.com is a filterable directory of certified deployments, and every automated probe of it and of gsma.com returned an HTTP 403 Cloudflare bot challenge. The specifications are open on GitHub under camaraproject and tmforum-apis; the endpoints belong to operators; and developers reach them through aggregators and channel partners such as Aduna, Vonage, Infobip, Sinch, Twilio, Nokia, AWS and Bridge Alliance rather than through the GSMA. Per the GSMA's own Q1 2026 Open Gateway update, 81 operator groups and 61 channel partners covering 292 networks and about 80 percent of global mobile connections have signed, with 237 certified API assets drawn from 33 CAMARA tagged released APIs and 280 API instances commercially launched across 85 networks in 50 markets.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/open-gateway/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/open-gateway/refs/heads/main/apis.yml)

## The Honest Posture

GSMA Open Gateway does not operate an API. It is the commitment and certification layer that
sits above one: the GSM Association signs mobile operators and channel partners to a memorandum of
understanding, certifies their implementations against CAMARA service APIs and TM Forum Operate
APIs, and publishes a directory of which certified APIs are live in which markets.

There is no self-serve developer portal. `open-gateway.gsma.com` is a filterable catalogue of
certified deployments, and every automated probe of it — and of `www.gsma.com`, `docs.gsma.com` and
every `developer.*` / `api.*` variant — returned an HTTP 403 Cloudflare bot challenge, a 401 member
wall, or no listener at all. See `review.yml` for the full probe table with status codes.

The specifications, by contrast, are genuinely open. They are just published by other people. The
service APIs live in the Linux Foundation-hosted [CAMARA project](https://github.com/camaraproject);
the onboarding and ordering Operate API lives with TM Forum as
[TMF931](https://github.com/tmforum-apis/TMF931_OpenGatewayOnboardingAndOrderingComponentSuite),
Apache-2.0. The 22 OpenAPI documents in `openapi/` were harvested verbatim from those two sources at
their latest stable release tags on 2026-07-25. None came from a GSMA host.

Every one of those specs uses a templated server — `{apiRoot}/<api-name>/v<major>`. There is no
absolute base URL anywhere in this stack. The specification is global; the endpoint is not. You get
an `apiRoot` and an OIDC issuer from whichever operator or aggregator you contracted with, which in
practice means Aduna, Bridge Alliance, or a CPaaS channel partner — never from the GSMA.


## CAMARA Posture

- **Role:** convener and certification authority. Open Gateway *is* the CAMARA commitment layer.
- **Exposes CAMARA directly:** no. Operators expose; 61 channel partners resell.
- **Press release only:** no — this is a real programme with real certified deployments, real open
  specs, and a joint GSMA/TM Forum conformance certification. What is missing is a first-party
  callable surface, not evidence of implementation.
- **Scale (GSMA Q1 2026 update):** 140 signatories — 81 operator groups and 61 channel partners —
  covering 292 networks and roughly 80% of global mobile connections; 237 certified API assets drawn
  from 33 CAMARA tagged released APIs, with 40 more in development; 280 API instances commercially
  launched across 85 networks in 50 markets, of which 27 markets are 100% API aligned.
- **Auth:** OpenID Connect throughout, with **CIBA** (Client-Initiated Backchannel Authentication) as
  the network-based authorization pattern — named 21 times in CAMARA's Identity and Consent
  Management profile and referenced directly in the Number Verification spec for TS.43 SIM-based
  authentication. Two-legged client credentials where no subscriber is identifiable.
- **Webhooks:** CloudEvents push to consumer-supplied sinks, defined in-spec. No AsyncAPI.
- **SDKs / Postman / GraphQL / gRPC:** none published by GSMA.


## Tags

- Telecommunications
- United Kingdom
- Network APIs
- CAMARA
- Open Gateway
- Standards
- Mobile Network Operator
- Identity Verification
- SIM Swap
- Quality on Demand
- 5G
- Certification
- Trade Association
- TM Forum

## Timestamps

- **Created:** 2026-07-25
- **Modified:** 2026-07-25

## Specifications (22)

Harvested verbatim from CAMARA and TM Forum. GSMA certifies these; it does not publish them.

### CAMARA Number Verification API

CAMARA Number Verification 2.1.0 as certified under GSMA Open Gateway. Verifies that the phone number claimed by a user matches the number of the SIM on the network connection, or returns the network-asserted phone number, using OIDC over the mobile data connection. The most widely launched Open Gateway API alongside SIM Swap.

- **Human URL:** [https://github.com/camaraproject/NumberVerification](https://github.com/camaraproject/NumberVerification)
- **Tags:** CAMARA, Identity Verification, Authentication, Anti-Fraud
- [OpenAPI](openapi/camara-number-verification-openapi.yml)
- [Documentation](https://github.com/camaraproject/NumberVerification)
- [APIReference](https://camaraproject.github.io/swagger-ui/)
- [Documentation](https://www.gsma.com/solutions-and-impact/gsma-open-gateway/gsma-open-gateway-api-descriptions/)

### CAMARA SIM Swap API

CAMARA SIM Swap 2.1.0 as certified under GSMA Open Gateway. Checks whether the SIM associated with a mobile phone number has been swapped within a given period, and retrieves the last SIM change date, so banks and fintechs can block account-takeover attacks. Per the GSMA Q1 2026 update this is the single most commercially launched Open Gateway API.

- **Human URL:** [https://github.com/camaraproject/SimSwap](https://github.com/camaraproject/SimSwap)
- **Tags:** CAMARA, Anti-Fraud, SIM Swap, Identity Verification
- [OpenAPI](openapi/camara-sim-swap-openapi.yml)
- [Documentation](https://github.com/camaraproject/SimSwap)
- [APIReference](https://camaraproject.github.io/swagger-ui/)
- [Documentation](https://www.gsma.com/solutions-and-impact/gsma-open-gateway/gsma-open-gateway-api-descriptions/)

### CAMARA Device Swap API

CAMARA Device Swap 1.0.0 as certified under GSMA Open Gateway. Reports whether the device attached to a mobile subscription has changed within a supplied period and retrieves the last device change date, used as an anti-fraud signal alongside SIM Swap.

- **Human URL:** [https://github.com/camaraproject/DeviceSwap](https://github.com/camaraproject/DeviceSwap)
- **Tags:** CAMARA, Anti-Fraud, Device
- [OpenAPI](openapi/camara-device-swap-openapi.yml)
- [Documentation](https://github.com/camaraproject/DeviceSwap)
- [APIReference](https://camaraproject.github.io/swagger-ui/)
- [Documentation](https://www.gsma.com/solutions-and-impact/gsma-open-gateway/gsma-open-gateway-api-descriptions/)

### CAMARA Call Forwarding Signal API

CAMARA Call Forwarding Signal 0.4.0 as certified under GSMA Open Gateway. Exposes whether unconditional or conditional call forwarding is active on a subscriber line, a signal used to detect social-engineering and call-diversion fraud.

- **Human URL:** [https://github.com/camaraproject/CallForwardingSignal](https://github.com/camaraproject/CallForwardingSignal)
- **Tags:** CAMARA, Anti-Fraud, Voice
- [OpenAPI](openapi/camara-call-forwarding-signal-openapi.yml)
- [Documentation](https://github.com/camaraproject/CallForwardingSignal)
- [APIReference](https://camaraproject.github.io/swagger-ui/)
- [Documentation](https://www.gsma.com/solutions-and-impact/gsma-open-gateway/gsma-open-gateway-api-descriptions/)

### CAMARA Know Your Customer Match API

CAMARA Know Your Customer Match 0.4.0 as certified under GSMA Open Gateway. Compares customer-supplied identity attributes (name, address, birthdate, identity document, email) against the data the mobile operator holds for the subscriber and returns per-attribute match results without disclosing the operator's own values.

- **Human URL:** [https://github.com/camaraproject/KnowYourCustomerMatch](https://github.com/camaraproject/KnowYourCustomerMatch)
- **Tags:** CAMARA, KYC, Identity Verification
- [OpenAPI](openapi/camara-kyc-match-openapi.yml)
- [Documentation](https://github.com/camaraproject/KnowYourCustomerMatch)
- [APIReference](https://camaraproject.github.io/swagger-ui/)
- [Documentation](https://www.gsma.com/solutions-and-impact/gsma-open-gateway/gsma-open-gateway-api-descriptions/)

### CAMARA Know Your Customer Age Verification API

CAMARA Know Your Customer Age Verification 0.2.1 as certified under GSMA Open Gateway. Answers whether the subscriber behind a phone number is over a given age threshold using operator-held identity data, returning a boolean rather than a date of birth.

- **Human URL:** [https://github.com/camaraproject/KnowYourCustomerAgeVerification](https://github.com/camaraproject/KnowYourCustomerAgeVerification)
- **Tags:** CAMARA, KYC, Age Verification, Identity Verification
- [OpenAPI](openapi/camara-kyc-age-verification-openapi.yml)
- [Documentation](https://github.com/camaraproject/KnowYourCustomerAgeVerification)
- [APIReference](https://camaraproject.github.io/swagger-ui/)
- [Documentation](https://www.gsma.com/solutions-and-impact/gsma-open-gateway/gsma-open-gateway-api-descriptions/)

### CAMARA KYC Tenure API

CAMARA KYC Tenure 0.2.0 as certified under GSMA Open Gateway. Confirms whether a mobile subscription has been active with the operator for at least a requested duration, a low-friction trust signal for onboarding and fraud scoring.

- **Human URL:** [https://github.com/camaraproject/Tenure](https://github.com/camaraproject/Tenure)
- **Tags:** CAMARA, KYC, Anti-Fraud
- [OpenAPI](openapi/camara-kyc-tenure-openapi.yml)
- [Documentation](https://github.com/camaraproject/Tenure)
- [APIReference](https://camaraproject.github.io/swagger-ui/)
- [Documentation](https://www.gsma.com/solutions-and-impact/gsma-open-gateway/gsma-open-gateway-api-descriptions/)

### CAMARA One Time Password SMS API

CAMARA One Time Password SMS 1.1.1 as certified under GSMA Open Gateway. Sends a one-time password by SMS to a subscriber's phone number and validates the code the user returns, delivered through the operator network rather than an aggregator's messaging route.

- **Human URL:** [https://github.com/camaraproject/OTPValidation](https://github.com/camaraproject/OTPValidation)
- **Tags:** CAMARA, Authentication, SMS, One Time Password
- [OpenAPI](openapi/camara-one-time-password-sms-openapi.yml)
- [Documentation](https://github.com/camaraproject/OTPValidation)
- [APIReference](https://camaraproject.github.io/swagger-ui/)
- [Documentation](https://www.gsma.com/solutions-and-impact/gsma-open-gateway/gsma-open-gateway-api-descriptions/)

### CAMARA Quality on Demand API

CAMARA Quality-On-Demand 1.1.0 as certified under GSMA Open Gateway. Creates, reads, extends and deletes temporary sessions that raise the network quality of service for a specific device and application flow, with event notifications on session status. The commercial front door to 5G differentiated connectivity.

- **Human URL:** [https://github.com/camaraproject/QualityOnDemand](https://github.com/camaraproject/QualityOnDemand)
- **Tags:** CAMARA, Quality on Demand, 5G, Network Slicing
- [OpenAPI](openapi/camara-quality-on-demand-openapi.yml)
- [Documentation](https://github.com/camaraproject/QualityOnDemand)
- [APIReference](https://camaraproject.github.io/swagger-ui/)
- [Documentation](https://www.gsma.com/solutions-and-impact/gsma-open-gateway/gsma-open-gateway-api-descriptions/)

### CAMARA QoS Profiles API

CAMARA QoS Profiles 1.1.0 as certified under GSMA Open Gateway. Lists the quality-of-service profiles an operator makes available and retrieves a single profile by name, so a developer can discover what Quality on Demand levels can actually be requested in a given market.

- **Human URL:** [https://github.com/camaraproject/QualityOnDemand](https://github.com/camaraproject/QualityOnDemand)
- **Tags:** CAMARA, Quality on Demand, 5G
- [OpenAPI](openapi/camara-qos-profiles-openapi.yml)
- [Documentation](https://github.com/camaraproject/QualityOnDemand)
- [APIReference](https://camaraproject.github.io/swagger-ui/)
- [Documentation](https://www.gsma.com/solutions-and-impact/gsma-open-gateway/gsma-open-gateway-api-descriptions/)

### CAMARA Device Reachability Status API

CAMARA Device Reachability Status 1.1.0 as certified under GSMA Open Gateway. Returns whether a device is currently reachable on the network and by which means (SMS, data), the core device-state signal for IoT fleet operations.

- **Human URL:** [https://github.com/camaraproject/DeviceReachabilityStatus](https://github.com/camaraproject/DeviceReachabilityStatus)
- **Tags:** CAMARA, Device Status, IoT
- [OpenAPI](openapi/camara-device-reachability-status-openapi.yml)
- [Documentation](https://github.com/camaraproject/DeviceReachabilityStatus)
- [APIReference](https://camaraproject.github.io/swagger-ui/)
- [Documentation](https://www.gsma.com/solutions-and-impact/gsma-open-gateway/gsma-open-gateway-api-descriptions/)

### CAMARA Device Reachability Status Subscriptions API

CAMARA Device Reachability Status Subscriptions 0.8.0 as certified under GSMA Open Gateway. Creates and manages event subscriptions that push CloudEvents notifications to a consumer sink when a device becomes reachable by data or SMS, or becomes disconnected. This is the event-driven half of Open Gateway device status.

- **Human URL:** [https://github.com/camaraproject/DeviceReachabilityStatus](https://github.com/camaraproject/DeviceReachabilityStatus)
- **Tags:** CAMARA, Device Status, IoT, Webhooks, CloudEvents
- [OpenAPI](openapi/camara-device-reachability-status-subscriptions-openapi.yml)
- [Documentation](https://github.com/camaraproject/DeviceReachabilityStatus)
- [APIReference](https://camaraproject.github.io/swagger-ui/)
- [Documentation](https://www.gsma.com/solutions-and-impact/gsma-open-gateway/gsma-open-gateway-api-descriptions/)

### CAMARA Device Identifier API

CAMARA Device Identifier 0.3.0 as certified under GSMA Open Gateway. Retrieves the identifier, type and pseudonymous identifier of the device a subscription is currently using, and matches a supplied identifier against the network view, supporting device-binding and fraud checks.

- **Human URL:** [https://github.com/camaraproject/DeviceIdentifier](https://github.com/camaraproject/DeviceIdentifier)
- **Tags:** CAMARA, Device, Identity Verification, Anti-Fraud
- [OpenAPI](openapi/camara-device-identifier-openapi.yml)
- [Documentation](https://github.com/camaraproject/DeviceIdentifier)
- [APIReference](https://camaraproject.github.io/swagger-ui/)
- [Documentation](https://www.gsma.com/solutions-and-impact/gsma-open-gateway/gsma-open-gateway-api-descriptions/)

### CAMARA Device Roaming Status API

CAMARA Device Roaming Status 1.0.0 as certified under GSMA Open Gateway. Reports whether a device is roaming and, where available, the country it is roaming in, used for travel-aware fraud rules and IoT logistics.

- **Human URL:** [https://github.com/camaraproject/DeviceStatus](https://github.com/camaraproject/DeviceStatus)
- **Tags:** CAMARA, Device Status, Roaming
- [OpenAPI](openapi/camara-device-roaming-status-openapi.yml)
- [Documentation](https://github.com/camaraproject/DeviceStatus)
- [APIReference](https://camaraproject.github.io/swagger-ui/)
- [Documentation](https://www.gsma.com/solutions-and-impact/gsma-open-gateway/gsma-open-gateway-api-descriptions/)

### CAMARA Connected Network Type API

CAMARA Connected Network Type 0.1.0 as certified under GSMA Open Gateway. Returns the access technology a device is currently attached to (for example 4G or 5G standalone), letting an application adapt its behaviour to real network conditions.

- **Human URL:** [https://github.com/camaraproject/DeviceStatus](https://github.com/camaraproject/DeviceStatus)
- **Tags:** CAMARA, Device Status, 5G
- [OpenAPI](openapi/camara-connected-network-type-openapi.yml)
- [Documentation](https://github.com/camaraproject/DeviceStatus)
- [APIReference](https://camaraproject.github.io/swagger-ui/)
- [Documentation](https://www.gsma.com/solutions-and-impact/gsma-open-gateway/gsma-open-gateway-api-descriptions/)

### CAMARA Population Density Data API

CAMARA Population Density Data 0.3.0 as certified under GSMA Open Gateway. Returns aggregated, anonymised estimates of how many devices are present in a requested geographic area over a time window, for planning, retail and mobility analytics rather than individual tracking.

- **Human URL:** [https://github.com/camaraproject/PopulationDensityData](https://github.com/camaraproject/PopulationDensityData)
- **Tags:** CAMARA, Location, Analytics, Privacy
- [OpenAPI](openapi/camara-population-density-data-openapi.yml)
- [Documentation](https://github.com/camaraproject/PopulationDensityData)
- [APIReference](https://camaraproject.github.io/swagger-ui/)
- [Documentation](https://www.gsma.com/solutions-and-impact/gsma-open-gateway/gsma-open-gateway-api-descriptions/)

### CAMARA Device Location Retrieval API

CAMARA Device Location Retrieval 0.5.0 as certified under GSMA Open Gateway. Returns the network-derived location of a device as a circle or polygon with an accuracy indication, without relying on handset GPS or an installed SDK.

- **Human URL:** [https://github.com/camaraproject/DeviceLocation](https://github.com/camaraproject/DeviceLocation)
- **Tags:** CAMARA, Location
- [OpenAPI](openapi/camara-location-retrieval-openapi.yml)
- [Documentation](https://github.com/camaraproject/DeviceLocation)
- [APIReference](https://camaraproject.github.io/swagger-ui/)
- [Documentation](https://www.gsma.com/solutions-and-impact/gsma-open-gateway/gsma-open-gateway-api-descriptions/)

### CAMARA Device Location Verification API

CAMARA Device Location Verification 3.0.0 as certified under GSMA Open Gateway. Verifies whether a device is inside, outside or partly within a supplied area rather than returning coordinates, a privacy-preserving pattern widely deployed for payment and login fraud checks.

- **Human URL:** [https://github.com/camaraproject/DeviceLocation](https://github.com/camaraproject/DeviceLocation)
- **Tags:** CAMARA, Location, Anti-Fraud
- [OpenAPI](openapi/camara-location-verification-openapi.yml)
- [Documentation](https://github.com/camaraproject/DeviceLocation)
- [APIReference](https://camaraproject.github.io/swagger-ui/)
- [Documentation](https://www.gsma.com/solutions-and-impact/gsma-open-gateway/gsma-open-gateway-api-descriptions/)

### CAMARA Simple Edge Discovery API

CAMARA Simple Edge Discovery 2.0.1 as certified under GSMA Open Gateway. Returns the closest MEC (multi-access edge computing) platform to a given device so that an application can route traffic to the lowest-latency edge cloud zone.

- **Human URL:** [https://github.com/camaraproject/SimpleEdgeDiscovery](https://github.com/camaraproject/SimpleEdgeDiscovery)
- **Tags:** CAMARA, Edge Computing, MEC, 5G
- [OpenAPI](openapi/camara-simple-edge-discovery-openapi.yml)
- [Documentation](https://github.com/camaraproject/SimpleEdgeDiscovery)
- [APIReference](https://camaraproject.github.io/swagger-ui/)
- [Documentation](https://www.gsma.com/solutions-and-impact/gsma-open-gateway/gsma-open-gateway-api-descriptions/)

### CAMARA Carrier Billing API

CAMARA Carrier Billing 0.5.0 as certified under GSMA Open Gateway. Creates and manages direct carrier-billing payments charged to a subscriber's mobile account, including payment status, confirmation and event notifications. The payments-and-charging leg of Open Gateway.

- **Human URL:** [https://github.com/camaraproject/CarrierBillingCheckOut](https://github.com/camaraproject/CarrierBillingCheckOut)
- **Tags:** CAMARA, Payments, Carrier Billing
- [OpenAPI](openapi/camara-carrier-billing-openapi.yml)
- [Documentation](https://github.com/camaraproject/CarrierBillingCheckOut)
- [APIReference](https://camaraproject.github.io/swagger-ui/)
- [Documentation](https://www.gsma.com/solutions-and-impact/gsma-open-gateway/gsma-open-gateway-api-descriptions/)

### CAMARA Home Devices QoD API

CAMARA Home Devices QoD 0.4.0 as certified under GSMA Open Gateway. Requests prioritised quality of service for a device on a fixed broadband home network, extending Quality on Demand beyond mobile to the operator's fixed access line. Note that the upstream CAMARA sandbox repository is now archived.

- **Human URL:** [https://github.com/camaraproject/HomeDevicesQoD](https://github.com/camaraproject/HomeDevicesQoD)
- **Tags:** CAMARA, Quality on Demand, Broadband, Fixed Access
- [OpenAPI](openapi/camara-home-devices-qod-openapi.yml)
- [Documentation](https://github.com/camaraproject/HomeDevicesQoD)
- [APIReference](https://camaraproject.github.io/swagger-ui/)
- [Documentation](https://www.gsma.com/solutions-and-impact/gsma-open-gateway/gsma-open-gateway-api-descriptions/)

### TM Forum TMF931 Open Gateway Onboarding and Ordering API

TM Forum TMF931 Open Gateway Operate API - Onboarding and Ordering 5.2.1, the GSMA-defined 'Operate API' that channel partners and aggregators use to onboard against an operator: browse the API product catalogue, place API product orders, manage applications and application owners, and subscribe to lifecycle events. This is the machine-readable onboarding path into Open Gateway, and it is certified jointly by GSMA and TM Forum alongside the CAMARA service APIs. Twenty-two operations, Apache-2.0 licensed.

- **Human URL:** [https://github.com/tmforum-apis/TMF931_OpenGatewayOnboardingAndOrderingComponentSuite](https://github.com/tmforum-apis/TMF931_OpenGatewayOnboardingAndOrderingComponentSuite)
- **Tags:** TM Forum, Operate API, Onboarding, Ordering, Standards
- [OpenAPI](openapi/tmforum-tmf931-open-gateway-onboarding-ordering-openapi.yml)
- [Documentation](https://github.com/tmforum-apis/TMF931_OpenGatewayOnboardingAndOrderingComponentSuite)
- [APIReference](https://github.com/tmforum-apis/TMF931_OpenGatewayOnboardingAndOrderingComponentSuite)
- [Documentation](https://www.gsma.com/solutions-and-impact/gsma-open-gateway/gsma-open-gateway-api-descriptions/)

## Common Properties

- [Website](https://www.gsma.com/)
- [Documentation](https://www.gsma.com/solutions-and-impact/gsma-open-gateway/)
- [Portal](https://open-gateway.gsma.com/)
- [APIDescriptions](https://www.gsma.com/solutions-and-impact/gsma-open-gateway/gsma-open-gateway-api-descriptions/)
- [Certification](https://www.gsma.com/solutions-and-impact/gsma-open-gateway/what-is-gsma-open-gateway-certification/)
- [Specification](https://github.com/camaraproject)
- [Specification](https://github.com/tmforum-apis/TMF931_OpenGatewayOnboardingAndOrderingComponentSuite)
- [Authentication](https://github.com/camaraproject/IdentityAndConsentManagement)
- [GitHubOrganization](https://github.com/GSMA)
- [LinkedIn](https://www.linkedin.com/company/gsma)
- [SecurityTxt](https://www.gsma.com/.well-known/security.txt)
- [VulnerabilityDisclosure](https://www.gsma.com/security/cvd-submit-a-vulnerability/)
- [Privacy](https://www.gsma.com/aboutus/legal/privacy)

## Maintainers

- Kin Lane — kin@apievangelist.com
