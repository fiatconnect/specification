# FiatConnect CICO Provider API Specification "RFC"

# 0. Table of Contents

- [0.](#0-table-of-contents) Table of Contents
- [1.](#1-introduction) Introduction
  * [1.1.](#11-a-note-on-celo--multi-chain-support) A Note on Celo & Multi-Chain Support
  * [1.2.](#12-notational-conventions) Notational Conventions
- [2.](#2-lifecycle-of-a-transfer) Lifecycle of a Transfer
  * [2.1.](#21-quote) Quote
  * [2.2.](#22-kyc) KYC
    + [2.2.1.](#221-kyc-status-monitoring) KYC Status Monitoring
  * [2.3.](#23-fiat-accounts) Fiat Accounts
  * [2.4.](#24-creating-the-transfer) Creating the Transfer
    + [2.4.1.](#241-transfer-status-monitoring) Transfer Status Monitoring
- [3.](#3-api-specification) API Specification
  * [3.1.](#31-geolocation) Geolocation
  * [3.2.](#32-authentication--authorization) Authentication & Authorization
    + [3.2.1.](#321-dek-based-jwt-authentication) DEK-based JWT Authentication
      - [3.2.1.1.](#3211-jwt-header) JWT Header
      - [3.2.1.2.](#3212-jwt-payload) JWT Payload
        * [3.2.1.2.1.](#32121-sub-claim) `"sub"` Claim
        * [3.2.1.2.2.](#32122-iss-claim) `"iss"` Claim
        * [3.2.1.2.3.](#32123-exp-claim) `"exp"` Claim
      - [3.2.1.3.](#3213-communicating-jwt) Communicating JWT
    + [3.2.2.](#322-client-api-token-authentication) Client API Token Authentication
      - [3.2.2.1.](#3221-communicating-api-tokens) Communicating API Tokens
  * [3.3.](#33-formal-specification) Formal Specification
    + [3.3.1.](#331-quote-endpoints) Quote Endpoints
      - [3.3.1.1.](#3311-get-quote-in) `GET /quote/in`
        * [3.3.1.1.1.](#33111-parameters) Parameters
          + [3.3.1.1.1.1.](#331111-query-parameters) Query Parameters
        * [3.3.1.1.2.](#33112-responses) Responses
          + [3.3.1.1.2.1.](#331121-http-200) HTTP `200`
        * [3.3.1.1.2.2.](#331122-http-400) HTTP `400`
        * [3.3.1.1.3.](#33113-semantics) Semantics
          + [3.3.1.1.3.1.](#331131-success) Success
          + [3.3.1.1.3.2.](#331132-failure) Failure
            - [3.3.1.1.3.2.1.](#3311321-geonotsupported) `GeoNotSupported`
            - [3.3.1.1.3.2.2.](#3311322-cryptoamounttoolow) `CryptoAmountTooLow`
            - [3.3.1.1.3.2.3.](#3311323-cryptoamounttoohigh) `CryptoAmountTooHigh`
            - [3.3.1.1.3.2.4.](#3311324-fiatamounttoolow) `FiatAmountTooLow`
            - [3.3.1.1.3.2.5.](#3311325-fiatamounttoohigh) `FiatAmountTooHigh`
            - [3.3.1.1.3.2.6.](#3311326-cryptonotsupported) `CryptoNotSupported`
            - [3.3.1.1.3.2.7.](#3311327-fiatnotsupported) `FiatNotSupported`
      - [3.3.1.2.](#3312-get-quote-out) `GET /quote/out`
        * [3.3.1.2.1.](#33121-parameters) Parameters
          + [3.3.1.2.1.1.](#331211-query-parameters) Query Parameters
        * [3.3.1.2.3.](#33123-responses) Responses
          + [3.3.1.2.3.1.](#331231-http-200) HTTP `200`
        * [3.3.1.2.3.2.](#331232-http-400) HTTP `400`
        * [3.3.1.2.2.](#33122-semantics) Semantics
          + [3.3.1.2.2.1.](#331221-success) Success
          + [3.3.1.2.2.2.](#331222-failure) Failure
            - [3.3.1.2.2.2.1.](#3312221-geonotsupported) `GeoNotSupported`
            - [3.3.1.2.2.2.2.](#3312222-cryptoamounttoolow) `CryptoAmountTooLow`
            - [3.3.1.2.2.2.3.](#3312223-cryptoamounttoohigh) `CryptoAmountTooHigh`
            - [3.3.1.2.2.2.4.](#3312224-fiatamounttoolow) `FiatAmountTooLow`
            - [3.3.1.2.2.2.5.](#3312225-fiatamounttoohigh) `FiatAmountTooHigh`
            - [3.3.1.2.2.2.6.](#3312226-cryptonotsupported) `CryptoNotSupported`
            - [3.3.1.2.2.2.7.](#3312227-fiatnotsupported) `FiatNotSupported`
    + [3.3.2.](#332-kyc-endpoints) KYC Endpoints
      - [3.3.2.1.](#3321-get-kyc-schema) `GET /kyc/schema`
        * [3.3.2.1.1.](#33211-responses) Responses
          + [3.3.2.1.1.1.](#332111-http-200) HTTP `200`
          + [3.3.2.1.1.2.](#332112-http-400) HTTP `400`
        * [3.3.2.1.2.](#33212-semantics) Semantics
          + [3.3.2.1.2.1.](#332121-success) Success
          + [3.3.2.1.2.2.](#332122-failure) Failure
      - [3.3.2.2.](#3322-post-kyc-kycschema) `POST /kyc/:kycSchema`
        * [3.3.2.2.1.](#33221-parameters) Parameters
          + [3.3.2.2.1.1.](#332211-path-parameters) Path Parameters
          + [3.3.2.2.1.2.](#332212-request-body) Request Body
        * [3.3.2.2.2.](#33222-responses) Responses
          + [3.3.2.2.2.1.](#332221-http-200) HTTP `200`
          + [3.3.2.2.2.2.](#332222-http-400) HTTP `400`
          + [3.3.2.2.2.3.](#332223-http-409) HTTP `409`
        * [3.3.2.2.3.](#33223-semantics) Semantics
          + [3.3.2.2.3.1.](#332231-success) Success
          + [3.3.2.2.3.2.](#332232-failure) Failure
            - [3.3.2.2.3.2.1.](#3322321-unsupportedschema) `UnsupportedSchema`
            - [3.3.2.2.3.2.2.](#3322322-invalidschema) `InvalidSchema`
            - [3.3.2.2.3.2.3.](#3322323-resourceexists) `ResourceExists`
      - [3.3.2.3.](#3323-get-kyc-kycschema-status) `GET /kyc/:kycSchema/status`
        * [3.3.2.3.1.](#33231-parameters) Parameters
          + [3.3.2.3.1.1.](#332311-path-parameters) Path Parameters
        * [3.3.2.3.2.](#33232-responses) Responses
          + [3.3.2.3.2.1.](#332321-http-200) HTTP `200`
          + [3.3.2.3.2.2.](#332322-http-404) HTTP `404`
        * [3.3.2.3.3.](#33233-semantics) Semantics
          + [3.3.2.3.3.1.](#332331-success) Success
          + [3.3.2.3.3.2.](#332332-failure) Failure
            - [3.3.2.3.3.2.1.](#3323321-resourcenotfound) `ResourceNotFound`
      - [3.3.2.4.](#3324-delete-kyc-kycschema) `DELETE /kyc/:kycSchema`
        * [3.3.2.4.1.](#33241-parameters) Parameters
          + [3.3.2.4.1.1.](#332411-path-parameters) Path Parameters
        * [3.3.2.4.2.](#33242-responses) Responses
          + [3.3.2.4.2.1.](#332421-http-200) HTTP `200`
          + [3.3.2.4.2.2.](#332422-http-404) HTTP `404`
        * [3.3.2.4.3.](#33243-semantics) Semantics
          + [3.3.2.4.3.1.](#332431-success) Success
          + [3.3.2.4.3.2.](#332432-failure) Failure
            - [3.3.2.4.3.2.1.](#3324321-resourcenotfound) `ResourceNotFound`
    + [3.3.3.](#333-fiat-account-endpoints) Fiat Account Endpoints
      - [3.3.3.1.](#3331-get-accounts-schema) `GET /accounts/schema`
        * [3.3.3.1.1.](#33311-parameters) Parameters
          + [3.3.3.1.1.1.](#333111-query-parmaters) Query Parmaters
        * [3.3.3.1.2.](#33312-responses) Responses
          + [3.3.3.1.2.1.](#333121-http-200) HTTP `200`
        * [3.3.3.1.3.](#33313-semantics) Semantics
          + [3.3.3.1.3.1.](#333131-success) Success
      - [3.3.3.2.](#3332-post-accounts-fiataccountschema) `POST /accounts/:fiatAccountSchema`
        * [3.3.3.2.1.](#33321-parameters) Parameters
          + [3.3.3.2.1.1.](#333211-path-parameters) Path Parameters
          + [3.3.3.2.1.2.](#333212-request-body) Request Body
        * [3.3.3.2.2.](#33322-responses) Responses
          + [3.3.3.2.2.1.](#333221-http-200) HTTP `200`
          + [3.3.3.2.2.2.](#333222-http-400) HTTP `400`
          + [3.3.3.2.2.3.](#333223-http-409) HTTP `409`
        * [3.3.3.2.3.](#33323-semantics) Semantics
          + [3.3.3.2.3.1.](#333231-success) Success
          + [3.3.3.2.3.1.](#333231-failure) Failure
            - [3.3.3.2.3.1.1.](#3332311-unsupportedschema) `UnsupportedSchema`
            - [3.3.3.2.3.1.2.](#3332312-invalidschema) `InvalidSchema`
            - [3.3.3.2.3.1.3.](#3332313-resourceexists) `ResourceExists`
      - [3.3.3.3.](#3333-get-accounts) `GET /accounts`
        * [3.3.3.3.1.](#33331-parameters) Parameters
          + [3.3.3.3.1.1.](#333311-query-parmaters) Query Parmaters
        * [3.3.3.3.2.](#33332-responses) Responses
          + [3.3.3.3.2.1.](#333321-http-200) HTTP `200`
        * [3.3.3.3.3.](#33333-semantics) Semantics
          + [3.3.3.3.3.1.](#333331-success) Success
      - [3.3.3.4.](#3334-delete-account-fiataccountid) `DELETE /account/:fiatAccountId`
        * [3.3.3.4.1.](#33341-parameters) Parameters
          + [3.3.3.4.1.1.](#333411-path-parameters) Path Parameters
        * [3.3.3.4.2.](#33342-responses) Responses
          + [3.3.3.4.2.1.](#333421-http-200) HTTP `200`
          + [3.3.3.4.2.2](#333422-http-404) HTTP. `404`
        * [3.3.3.4.3.](#33343-semantics) Semantics
          + [3.3.3.4.3.1.](#333431-success) Success
          + [3.3.3.4.3.1.](#333431-failure) Failure
            - [3.3.3.4.3.1.1.](#3334311-resourcenotfound) `ResourceNotFound`
    + [3.3.4.](#334-transfer-endpoints) Transfer Endpoints
      - [3.3.4.1.](#3341-post-transfer-in) `POST /transfer/in`
        * [3.3.4.1.1.](#33411-parameters) Parameters
          + [3.3.4.1.1.1.](#334111-headers) Headers
          + [3.3.4.1.1.2.](#334112-request-body) Request Body
        * [3.3.4.1.2.](#33412-responses) Responses
          + [3.3.4.1.2.1.](#334121-http-200) HTTP `200`
          + [3.3.4.1.2.2.](#334122-http-400) HTTP `400`
          + [3.3.4.1.2.3.](#334123-http-404) HTTP `404`
          + [3.3.4.1.2.4.](#334124-idempotency-key-errors) Idempotency Key Errors
        * [3.3.4.1.3.](#33413-semantics) Semantics
          + [3.3.4.1.3.1.](#334131-success) Success
          + [3.3.4.1.3.2.](#334132-failure) Failure
            - [3.3.4.1.3.2.1.](#3341321-kycexpired) `KycExpired`
            - [3.3.4.1.3.2.2.](#3341322-transfernotallowed) `TransferNotAllowed`
            - [3.3.4.1.3.2.3.](#3341323-resourcenotfound) `ResourceNotFound`
      - [3.3.4.2.](#3342-post-transfer-out) `POST /transfer/out`
        * [3.3.4.2.1.](#33421-parameters) Parameters
          + [3.3.4.2.1.1.](#334211-headers) Headers
          + [3.3.4.2.1.2.](#334212-request-body) Request Body
        * [3.3.4.2.2.](#33422-responses) Responses
          + [3.3.4.2.2.1.](#334221-http-200) HTTP `200`
          + [3.3.4.2.2.2.](#334222-http-400) HTTP `400`
          + [3.3.4.2.2.3.](#334223-http-404) HTTP `404`
          + [3.3.4.2.2.4.](#334224-idempotency-key-errors) Idempotency Key Errors
        * [3.3.4.2.3.](#33423-semantics) Semantics
          + [3.3.4.2.3.1.](#334231-success) Success
          + [3.3.4.2.3.2.](#334232-failure) Failure
            - [3.3.4.2.3.2.1.](#3342321-kycexpired) `KycExpired`
            - [3.3.4.2.3.2.2.](#3342322-transfernotallowed) `TransferNotAllowed`
            - [3.3.4.2.3.2.3.](#3342323-resourcenotfound) `ResourceNotFound`
      - [3.3.4.3.](#3343-get-transfer-in-transferid-status) `GET /transfer/in/:transferId/status`
        * [3.3.4.3.1.](#33431-parameters) Parameters
          + [3.3.4.3.1.1.](#334311-path-parameters) Path Parameters
        * [3.3.4.3.2.](#33432-responses) Responses
          + [3.3.4.3.2.1.](#334321-http-200) HTTP `200`
          + [3.3.4.3.2.2.](#334322-http-404) HTTP `404`
        * [3.3.4.3.3.](#33433-semantics) Semantics
          + [3.3.4.3.3.1.](#334331-success) Success
          + [3.3.4.3.3.2.](#334332-failure) Failure
            - [3.3.4.3.3.2.1.](#3343321-resourcenotfound) `ResourceNotFound`
      - [3.3.4.4.](#3344-get-transfer-out-transferid-status) `GET /transfer/out/:transferId/status`
        * [3.3.4.4.1.](#33441-parameters) Parameters
          + [3.3.4.4.1.1.](#334411-path-parameters) Path Parameters
        * [3.3.4.4.2.](#33442-responses) Responses
          + [3.3.4.4.2.1.](#334421-http-200) HTTP `200`
          + [3.3.4.4.2.2.](#334422-http-404) HTTP `404`
        * [3.3.4.4.3.](#33443-semantics) Semantics
          + [3.3.4.4.3.1.](#334431-success) Success
          + [3.3.4.4.3.2.](#334432-failure) Failure
            - [3.3.4.4.3.2.1.](#3344321-resourcenotfound) `ResourceNotFound`
- [4.](#4-webhooks) Webhooks
  * [4.1.](#41-webhook-requests) Webhook Requests
  * [4.2.](#42-webhook-request-signing) Webhook Request Signing
- [5.](#5-aml-considerations) AML Considerations
- [6.](#6-definitions) Definitions
  * [6.1.](#61-static-definitions) Static Definitions
    + [6.1.1.](#611-kycstatusenum) `KycStatusEnum`
    + [6.1.2.](#612-errorenum) `ErrorEnum`
    + [6.1.3.](#613-transfertypeenum) `TransferTypeEnum`
    + [6.1.3.](#613-webhookeventtypeenum) `WebhookEventTypeEnum`
    + [6.1.4.](#614-transferstatusenum) `TransferStatusEnum`
  * [6.2.](#62-dynamic-definitions) Dynamic Definitions
    + [6.2.1.](#621-fiattypeenum) `FiatTypeEnum`
    + [6.2.2.](#622-cryptotypeenum) `CryptoTypeEnum`
    + [6.2.3.](#623-kycschemaenum) `KycSchemaEnum`
    + [6.2.4.](#624-fiataccounttypeenum) `FiatAccountTypeEnum`
    + [6.2.5.](#625-fiataccountschemaenum) `FiatAccountSchemaEnum`
  * [6.3.](#63-initial-entity-support) Initial Entity Support
- [7.](#7-references) References
  * [7.1.](#71-normative-references) Normative References
    + [7.1.1.](#711-rfc2119) [RFC2119]
    + [7.1.2.](#712-rfc8174) [RFC8174]
    + [7.1.3.](#713-rfc7519) [RFC7519]
  * [7.2.](#72-informative-references) Informative References
    + [7.2.1.](#721-data-encryption-key) Data Encryption Key
    + [7.2.2.](#722-webhook-best-practices) Webhook Best Practices
    + [7.2.3.](#723-idempotency-keys) Idempotency Keys

# 1. Introduction

Liquidity between fiat and cryptocurrency assets is a common point of friction for new and growing crypto networks. In particular, cashing in/out
to/from crypto assets can be a difficult experience. Oftentimes, there is poor support for geographical regions, and fees and spreads may be excessively high.
Additionally, liquidity providers (referred to here as *cash-in/cash-out providers*, or *CICO providers*) share no common interface for allowing clients
to transfer funds between crypto and fiat. In many cases, CICO providers only provide transfers through their own native (typically web-based) interface.
In the few cases where CICO providers do offer an API for clients to integrate against, there is no uniformity in interface design across providers. Because each
provider's API is unique, native CICO integrations within clients require completely bespoke integrations. Furthermore, since individual CICO providers may only
offer limited geographical support for transfers, a client may have to integrate with many different providers in order to offer acceptably broad coverage to its
users, which requires considerable implementation effort. In many cases, this level of implementation effort is not feasible, which can lead to clients which
offer poor regional CICO availablity. This may lead to users seeking out a variety of different clients in order to access broad regional support. Not only is
this a poor user experience, but it is a detriment to both client developers and the network which those clients and CICO providers service. Clients unable to
devote *considerable* engineering effort towards CICO provider integration lose out, and network TVL is hamstrung due to poor user experience.

*FiatConnect* is a proposed solution to these issues. FiatConnect provides a standardized server-side API specification for CICO providers to provide
cash-in/cash-out functionality to clients. In addition to providing a standardized API specification, the FiatConnect ecosystem also offers a set of
client-side tooling through the *FiatConnect SDK* which makes it seamless for client developers to integrate against any CICO provider who operates
a *FiatConnect API*. By integrating with the FiatConnect SDK, clients will be able to implement a *single* native flow that can be re-used to perform
transfers with *any* provider operating a FiatConnect API; supporting new providers in the client merely requires a small amount of static configuration.
We believe that the FiatConnect specification will provide benefits to a multitude of parties:

1. **Client Developers**: Client developers will not have to exert any specific implementation effort in order to integrate with any particular CICO
   provider. After implementing a single native flow with the FiatConnect SDK, supporting new providers comes for free.
2. **CICO Providers**: CICO providers, particularly smaller ones, face a challenge in gaining initial adoption; they often must rely on the *client* to
   spend engineering effort creating an integration. With FiatConnect, the onus of implementation is placed upon the CICO provider. While this does
   require some engineering effort, once a provider implements a FiatConnect API, they can immediately start providing crypto/fiat liquidity to *any*
   client, at no cost to the client developer. Client developers are thus much more likely to integrate with FiatConnect-compliant providers, making
   FiatConnect a compelling option for providers in order to drive adoption.
3. **End User**: Since FiatConnect makes it simple for clients to integrate with a large variety of providers with differing geographical support,
   the end user will have access to broad CICO coverage in whatever client they choose to use, as long as it integrates with the FiatConnect SDK.
   Since clients will be able to integrate with a large number of providers using FiatConnect, we also predict that the FiatConnect specification will
   encourage competition among CICO providers, ultimately leading a decrease in spreads and fees for the end user
4. **Network**: FiatConnect directly benefits network health, by increasing the bandwidth for liquidity between the network's native assets and fiat
   currencies. Currently, cashing in to new networks from fiat presents a bottleneck for network TVL. FiatConnect solves this, as well as the issue
   of cashing out. While one may argue that since both the cash-in and cash-out experiences are improved symmetrically, network TVL should remain flat,
   we believe that inexpensive, bi-directional liquidity between crypto and fiat is a compelling feature of blockchain networks, and will drive
   a net increase in network TVL.

This document offers a specification for the FiatConnect API for CICO providers; it discusses the client-side FiatConnect SDK only when
necessary, and only as it concerns the FiatConnect API design.

## 1.1. A Note on Celo & Multi-Chain Support

This proposal has been designed without multi-chain support in mind; we assume all blockchain-related concepts (e.g., tokens, addresses) exist within
a single predetermined chain, agreed upon by all parties. It does not matter so much *what* this chain is, just that
all parties (clients and servers) are in agreement. If a provider wants to support multiple chains, they would have to stand up multiple APIs, with identical
interfaces. Likewise, clients would have to maintain per-chain instances of the FiatConnect SDK (discussed later) in order to support transfers from multiple chains.

This specification is explicitly designed for the Celo network. As such, the document will occasionally reference Celo-specific entities; in particular,
specific cryptocurrencies that are native to the Celo network.

The value of this specification extends well beyond the Celo network however; it is likely extensible by providing various parts of the specification with
an e.g. `chainId` parameter. In the interest of keeping this document as simple as possible, and to avoid introducing premature complexity, we assume
that clients and servers referenced in this document are operating against the Celo network.

## 1.2. Notational Conventions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL"
in this document are to be interpreted as described in BCP 14 [RFC2119] [RFC8174] when, and only when, they appear in all capitals, as shown here.

# 2. Lifecycle of a Transfer

We first provide a generalized and somewhat informal model of a "transfer" operation, which will inform the FiatConnect API specification later in this document.
The model should be general enough to support a wide range of CICO provider's requirements. This transfer lifecycle is based on the author's
own experience as a client developer for Valora on the Celo network; it should certainly be reviewed by CICO providers and amended if there are any shortcomings.

For a single provider, we model the lifecycle of a transfer (either *in* or *out*) as a number of discrete steps:

- 1. **Quote**: The client requests a quote for a transfer with the user's desired parameters.
- 2. **KYC**: For regulatory and compliance reasons, the user must provide data used for KYC verification purposes to the provider.
  - 2.1. **Monitor KYC**: KYC verification is assumed to be asynchronous; the client should be able to monitor its status.
- 3. **Fiat Account**: The user must be able to provide a source of fiat funds for transfers in, or a destination for transfers out.
- 4. **Create Transfer**: Once a user has provided KYC information and has an appropriate fiat account on file, the client initiates a transfer.
  - 4.1. **Monitor Transfer**: Transfers are assumed to be asynchronous; the client should be able to monitor its status.

## 2.1. Quote

Before a transfer is ever initiated, the client should be able to request a quote for a potential transfer based off of the user's
desired transfer parameters. We expect that quote generation will depend on the following parameters:

1. `fiatType`: The type of fiat currency used for the transfer.
2. `cryptoType`: The type of cryptocurrency used for the transfer.
3. `amount`: The amount of either fiat or cryptocurrency selected for the quote.
4. `fiatAccountType`: The *type* of fiat account being used for the transfer.
5. `region`: The user's geographical region.

We will explore the precise syntax and semantics of each of these parameters later. If a provider is unable to service a transfer
given specific quote parameters for whatever reason, the provider should return an error code that details why the quote failed. In
this way, a client may use the quote endpoints as an initial "gate" to determine whether or not a provider is supported for a particular
transfer intent.

## 2.2. KYC

For regulatory and compliance reasons, providers may require that users complete a KYC verification process before initiating a transfer.
We expect that different providers will have varying KYC requirements depending on a client's geogrpahical region. The FiatConnect
specification must not make any assumptions about the particular *shape* of KYC data that providers require. Rather, the specification
should be extensible so as to allow providers to communicate to the client what sort of KYC data is required. The FiatConnect specification
allows for a list of standardized *KYC schemas* from which a provider can select those that it accepts. If a provider's KYC needs are
unmet by the specification's recognized schemas, the provider may request that the specification be extended to support a new schema, though
a close eye should be kept on the reusability of this schema by other potential providers.

KYC validation is not expected to occur before *every* transfer. Rather, we expect that KYC validation with a provider will be an infrequent
occurence. A user may have to submit KYC details for a particular region before initiating a transfer there, but subsequent transfers in that
region will likely reuse the previous KYC. One might expect that these details become "stale" after a time and will need to be resubmitted by the
end user before initiating further transfers in their region. Ultimately, what a provider decides to do with KYC data once it is submitted is an implementation
detail and not strictly enforced by the FiatConnect API specification, *except* for requirements on communicating KYC verification statuses to
the client.

### 2.2.1. KYC Status Monitoring

Once a client submits a user's KYC details to a provider, we expect that most providers will complete verification quickly and automatically,
however the verification process is assumed to be fundamentally asynchronous. The server must provide a means for the client to monitor the
status of an ongoing KYC verification, and query the status of previously submitted verifications. The FiatConnect standard specifies a list
of common KYC statuses with fixed semantics that a server can communicate to the client. The standard requires that providers offer two means
of monitoring KYC verification:

1. Via typical endpoints that can be polled by the client; providers are required to implement this feature.
2. Via webhooks that can be configured by the client; providers may optionally offer this feature.

We will discuss these two options in more depth later in this document.

## 2.3. Fiat Accounts

A user needs to be able to store details about a source or destination for fiat funds with the provider. The FiatConnect specification refers
to these as *Fiat Accounts*, but these can commonly be thought of as, e.g., *bank accounts*, *credit cards*, etc. A fiat account refers to
any user-owned accout that supports withdrawals and deposits of fiat-denominated funds. Similar to KYC details, different providers may support
different *types* of fiat accounts, and require different schemas depending on the user's geographical region. The FiatConnect specification
introduces two distinct concepts to account for this; that of a *Fiat Account Type*, and a *Fiat Account Schema*. A *Fiat Account Type* refers to
the high-level *type* of fiat account, e.g., bank account, credit card, debit card, etc. A *Fiat Accout Schema* refers to the precise shape that
account data should take when being communicated from client to server; each *schema* has an associated *type*. This is a subtle, yet important
distinction. As mentioned in section 2.1, providers may use the *type* of a fiat account for determining whether a transfer is allowed. Different
providers, however, may have different requirements on how account data is communicated, even for the same account *type*, depending on geographical
region. Like with KYC schemas, the FiatConnect standard does not make assumptions about fiat account types and schemas; these are designed to be
extensible by the community.

A CICO provider must provide functionality for a client to store fiat accout details with them; these details must be sufficient in order for the
provider to initiate a deposit/withdrawal on the user's behalf. Providers must also allow clients to retrieve and delete stored accounts.
Providers may not allow users to submit fiat account details before undergoing KYC verification for the current region.
Since providers may have restrictions on what fiat account types are allowed for transfers in a particular region, providers must also provide
a means for clients to know what these account types are.

## 2.4. Creating the Transfer

Once a user has completed KYC and has an appropriate fiat account type on file with the provider, the client may intiate a transfer. When creating
a transfer, the client must be able to reference a fiat account on store with the provider to use as a source or destination for fiat funds. It is
extremely important that transfer requests are idempotent; if a transfer request/server response is lost in transit, the client should be able to
safely re-try the request without risk of initiating a duplicate transfer. As such, the FiatConnect specification has a strict requirement that
providers support idempotency keys for transfer requests. This is discussed more in-depth later in this document.

### 2.4.1. Transfer Status Monitoring

Transfers, like KYC verifications, are assumed to be fundamentally asynchronous. We do not expect that all transfers complete quickly (as we do for
KYC verifications), which makes it critical for clients to be able to monitor transfer status over time. The server must provide a means for a client
to retrieve the status of a transfer once it has been submitted. Much like monitoring KYC statuses, the FiatConnect standard requires that providers
offer two means of transfer status monitoring:

1. Via typical endpoints that can be polled by the client; providers are required to implement this feature.
2. Via webhooks that can be configured by the client; providers may optionally offer this feature.

These will be discussed in more depth later in this document.

# 3. API Specification

This section presents the formal specification of the FiatConnect API, including required semantics.

## 3.1. Geolocation

Throughout the specification, many references are made to "geo", "geographical location", "region", etc. These are all meant to refer
to the actual geographical location of the client. This is an important *implicit* parameter of client requests, since regulatory
constraints may dictate that providers implement different requirements on requests from different geographical regions.

Since it is ultimately up to the provider to ensure that a transfer meets e.g., regulatory requirements for a particular geo,
it is the provider's responsibility to infer the actual geolocation of the user from their request. If a provider requires this
information, the provider MAY infer the geographical location of the user from, e.g., the origin IP address of their request.
We assume that geolcation data provided by the client cannot be trusted. The server-side semantics of geolocation support and ultimately
determining whether or not a transfer is allowed is left to the implementor; it seems likely that a reasonable way of dealing with
this is by failing a user's KYC verification when the KYC data (likely from a third party server-side integration) reports that a user's
geolocation does not match the request's origin IP.

## 3.2. Authentication & Authorization

The FiatConnect API specifies *two* types of authentication; the first is required for all requests; the second is optionally required,
depending on whether or not it is supported by the CICO provider. Since none of the endpoints required by the FiatConnect specification
require priveleged authorization, the terms "authentication" and "authorization" here are more or less synonymous. If a user successfully
authenticates, they are authorized to access the entirety of the API.

### 3.2.1. DEK-based JWT Authentication

Only users with a registered address on the Celo blockchain may make requests to a FiatConnect API. The FiatConnect API specification
requires servers to recognize a JWT token encrypted with the user's private key, decryptable with the user's public *Data Encryption Key*.
The [Data Encryption Key](https://docs.celo.org/developer-resources/contractkit/data-encryption-key) (or *DEK*) is a Celo-specific concept.
Using JSON Web Token (JWT) based auth signed using a user's DEK, servers can both:

1. Associate a user's Celo blockchain address with the request, and
2. Verify the requester's ownership of the provided address.

Servers MUST parse and recognize JWT tokens according to the following format. Servers MUST validate these tokens on every request.

#### 3.2.1.1. JWT Header

Servers MUST support the following JWT header:

```json
{
	"alg": "ES256",
	"typ": "JWT"
}
```

Servers MAY support other algorithms, but this is not required.

#### 3.2.1.2. JWT Payload

Servers MUST recognize the following *registered claims* within the JWT payload.

* `iss`, or *issuer*
* `exp`, or *expiration time*
* `sub`, or *subject*

The semantics of these claims are below:

##### 3.2.1.2.1. `"sub"` Claim

The `sub`, or *subject* claim represents the user's Celo network address. This is a required claim. If it is missing, the server MUST respond
to the client with an HTTP `400` error. This claim SHOULD be interpreted and used within server endpoints as a unique user identifier, and as the
source of/destination for crypto funds during transfers. The address should be formatted as 42 hexadecimal characters prefixed by the string literal
`0x`. If the `sub` claim does not match this format, the server MUST respond to the client with an HTTP `400` error.

##### 3.2.1.2.2. `"iss"` Claim

The `iss`, or *issuer* claim is an *optional* claim; the client may or may not choose to include it. If present, it represents the user's public DEK.
This is an optional field, since the a user's public DEK can always be queried on-chain, using the user's account address required in the `sub` claim.
If this field is present, the server SHOULD use it as a user's DEK. If it is not present, the server MUST query the user's public DEK on-chain.

If the issuer claim is present and is used by the server as a source of truth for the user's public DEK, the server MUST verify that the client-provided
DEK matches the on-chain DEK for the address provided in the `sub` claim. This is *critically* important. If this is not done, a
malicious client may sign the JWT using a private key that does not correspond to the account address given in the `sub` claim, in an attempt
to authenticate as an address for which they do not possess the private key. When performing this check, if the public DEK provided in the `iss` claim
does not match the on-chain public DEK associated with the account address in the `sub` claim, the server MUST return an HTTP `401` error.

##### 3.2.1.2.3. `"exp"` Claim

The `exp`, or *expiration time* claim represents the time until which the provided JWT should be accepted by the server. The `exp` claim should be provided
as a numeric timestamp, defined as the number of seconds since Epoch. If this field is missing, a server MUST return an HTTP `400` error.
A server MUST also return an HTTP `400` error if the provided `exp` field is later than the current time with respect to Epoch.

#### 3.2.1.3. Communicating JWT

The JWT should be communicated to the server using the `Bearer` authentication scheme within the `Authorization` header. Because FiatConnect also supports
simaltaneous API token authentication, the JWT should be the first field value present in the `Authorization` header. e.g., if a client *only* sends a JWT, the
header would look like: `Authorization: Bearer <jwt>`. If the header is not present, or is malformed, the server MUST return an HTTP `400` error.

### 3.2.2. Client API Token Authentication

Servers may also support API token based authentication. Recall the webhook-based status monitoring mentioned earlier in this document. In order to
support status monitoring via webhooks, individual clients will need to be able to register a URL pointing to an API able to handle webhook updates
from the server. Once a client has registered a webhook URL with the provider, the client needs a way to identify itself to the server. To uniquely
identify clients in order to know where to send webhook-based status updates, a server may allow clients to register an API key. The exact mechanism
by which servers allocate API keys to clients and allow them to register webhook URLs is out of scope of this document.

A server MAY support API token authentication. If a server does, it MAY *require* that clients include an API key in each request. If a server requires
that clients include an API key in each request, it MUST respond to the client with an HTTP `400` error if the API key is missing from the request.
If a server supports API key authentication, even if it does not require it for all clients, it MUST return an HTTP `401` error if an API key is
provided but does not correspond to any registered client; likewise it MUST return an HTTP `400` error if the API key is poorly formed.

#### 3.2.2.1. Communicating API Tokens

API tokens should be communicated via the `Bearer` authentication scheme within the `Authorization` header. Because FiatConnect requires JWT auth for all requests
and API token-based auth is optional, the API token should be the second field value in the authorization header. For example: `Authorization: Bearer <jwt>, Bearer <token>`.
If the header is not present, or is malformed, the server MUST return an HTTP `400` error.

## 3.3. Formal Specification

This section details the precise syntax and semantics of all the endpoints required by the FiatConnect specification. Endpoints are logically grouped, and roughly presented
in order of dependency. The logical groupings are as follows:

* Quote Endpoints
* KYC Endpoints
* Fiat Account Endpoints
* Transfer Endpoints

This section references a number of definitions/enums; for an exhaustive list of these, please see later in the document.

### 3.3.1. Quote Endpoints

We assume that the lifecycle of a transfer begins with an end-user requesting a quote. We predict that the typical transfer experience will involve
a user providing their desired transfer parameters (crypto type, fiat type, amount, desired fiat account type), and the client then proceeding to show
them a list of supported providers. We require that the *type* of fiat account intended to be used for the transfer is provided to the server, since
certain transfers may be unsupported for certain types of fiat accounts.

Quotes may vary depending on geo, and certain specific transfers may be unavailable in certain regions entirely. As such, we assume that the
client will interpret a 200 response from this endpoint as verification that the provider is supported for the particular transfer parameters,
and a non-200 as verification that it is not.

Some non-200s may be recovered from by modifying the transfer parameters; others may not be, e.g., for those caused by geos where transfers are completely unsupported.

#### 3.3.1.1. `GET /quote/in`

The `GET /quote/in` endpoint is used to retrieve quotes used for transfers in to crypto from fiat currencies.

##### 3.3.1.1.1. Parameters

###### 3.3.1.1.1.1. Query Parameters

* `fiatType`: {`FiatTypeEnum`} [REQUIRED]
  - The desired fiat type to use for a transfer in quote; selected from a predefined list of fiat types supported by FiatConnect.
* `cryptoType`: {`CryptoTypeEnum`} [REQUIRED]
  - The desired crypto type to use for a transfer in quote; selected from a predefined list of crypto types supported by FiatConnect.
* `fiatAmount`: {`float`}
  - The amount of the selected fiat type to use for this transfer in quote; if provided, the returned quote will be denominated in the type of crypto specified for the quote.
* `cryptoAmount`: {`float`}
  - The amount of the selected crypto type to use for this transfer in quote; if provided, the returned quote will be denominated in the type of fiat specified for the quote.
* `fiatAccountType`: {`FiatAccountTypeEnum`} [REQUIRED]
  - The type of fiat account to use for a transfer in quote; selected from a predefined list of fiat account types supported by FiatConnect.

##### 3.3.1.1.2. Responses

###### 3.3.1.1.2.1. HTTP `200`

On success, the server MUST return an HTTP `200`, with the following response body:

```
{
	fiatType: `FiatTypeEnum`,
	cryptoType: `CryptoTypeEnum`,
	fiatAmount?: `float`,
	cryptoAmount?: `float`,
	fiatAccountType: `FiatAccountTypeEnum`,
	quote: {
		amount: `float`,
		converstionRate: `float`,
		fee?: `float`
	}
}
```

##### 3.3.1.1.2.2. HTTP `400`

On failure, the server MUST return an HTTP `400`, with a response body as follows. Refer to the `ErrorEnum` definition for all possible error values.

```
{
	error: `ErrorEnum`,
	minimumFiatAmount?: `float`,
	maximumFiatAmount?: `float`,
	minimumCryptoAmount?: `float`,
	maximumCryptoAmount?: `float`
}
```

##### 3.3.1.1.3. Semantics

All transfer in quotes require the `fiat`, `crypto`, and `fiatAccountType` query parameters, and exactly *one of* `fiatAmount` or `cryptoAmount`. If these requirements
are not met, the server MUST return an HTTP `400` error. If the server responds with an HTTP `200`, the provider MUST support a transfer in for the requested quote. If
the requested quote is not supported, the server MUST return an HTTP `400` error.

###### 3.3.1.1.3.1. Success

A successful response indicates that the provider is able to perform a transfer for the requested quote.
If `fiatAmount` is provided, the `quote.amount` field returned in the success body MUST correspond to the amount of crypto the user should expect to
receive by providing `fiatAmount` worth of the fiat currency. If `cryptoAmount` is provided, the `quote.amount` field MUST correspond to the amount
of fiat currency required in order to receive the requested amount of crypto.

The `quote.conversionRate` field MUST always represent the cost of a unit crypto type with respect to the selected fiat type.

The `quote.fee` field is an optional return value, used to represent an optional fixed fee for the transfer. A server MAY choose to include this in the response body, though it
MUST be included if the provider requires a fee for the transfer. For transfers in, this fee is assumed to be denominated in the selected `fiatType`.

The `fiatType`, `cryptoType`, `fiatAmount`, `cryptoAmount`, and `fiatAccountType` fields in the response body MUST correspond to the query parameters provided to the endpoint.

###### 3.3.1.1.3.2. Failure

On failure, this endpoint MUST return an HTTP `400` error, along with an error code that details exactly why the quote failed.

###### 3.3.1.1.3.2.1. `GeoNotSupported`

If a quote is not supported due to geographical/region constraints, the server MUST return a `GeoNotSupported` error.

###### 3.3.1.1.3.2.2. `CryptoAmountTooLow`

If the user provides `cryptoAmount` in the query parameter, and the value is too low, the server MUST return a `CryptoAmountTooLow` error. The
server MAY also provide a `minimumCryptoAmount` value with the response body.

###### 3.3.1.1.3.2.3. `CryptoAmountTooHigh`

If the user provides `cryptoAmount` in the query parameter, and the value is too high, the server MUST return a `CryptoAmountTooHigh` error. The
server MAY also provide a `maximumCryptoAmount` value with the response body.

###### 3.3.1.1.3.2.4. `FiatAmountTooLow`

If the user provides `fiatAmount` in the query parameter, and the value is too low, the server MUST return a `FiatAmountTooLow` error. The
server MAY also provide a `minimumFiatAmount` value with the response body.

###### 3.3.1.1.3.2.5. `FiatAmountTooHigh`

If the user provides `fiatAmount` in the query parameter, and the value is too high, the server MUST return a `FiatAmountTooHigh` error. The
server MAY also provide a `maximumFiatAmount` value with the response body.

###### 3.3.1.1.3.2.6. `CryptoNotSupported`

If the provided `cryptoType` is unsupported, the server MUST return a `CryptoNotSupported` error.

###### 3.3.1.1.3.2.7. `FiatNotSupported`

If the provided `fiatType` is unsupported, the server MUST return a `FiatNotSupported` error.

#### 3.3.1.2. `GET /quote/out`

The `GET /quote/out` endpoint is used to retrieve quotes used for transfers out from crypto to fiat currencies.

##### 3.3.1.2.1. Parameters

###### 3.3.1.2.1.1. Query Parameters

* `fiatType`: {`FiatTypeEnum`} [REQUIRED]
  - The desired fiat type to use for a transfer in quote; selected from a predefined list of fiat types supported by FiatConnect.
* `cryptoType`: {`CryptoTypeEnum`} [REQUIRED]
  - The desired crypto type to use for a transfer in quote; selected from a predefined list of crypto types supported by FiatConnect.
* `fiatAmount`: {`float`}
  - The amount of the selected fiat type to use for this transfer in quote; if provided, the returned quote will be denominated in the type of crypto specified for the quote.
* `cryptoAmount`: {`float`}
  - The amount of the selected crypto type to use for this transfer in quote; if provided, the returned quote will be denominated in the type of fiat specified for the quote.
* `fiatAccountType`: {`FiatAccountTypeEnum`} [REQUIRED]
  - The type of fiat account to use for a transfer in quote; selected from a predefined list of fiat account types supported by FiatConnect.

##### 3.3.1.2.3. Responses

###### 3.3.1.2.3.1. HTTP `200`

On success, the server MUST return an HTTP `200`, with the following response body:

```
{
	fiatType: `FiatTypeEnum`,
	cryptoType: `CryptoTypeEnum`,
	fiatAmount?: `float`,
	cryptoAmount?: `float`,
	fiatAccountType: `FiatAccountTypeEnum`,
	quote: {
		amount: `float`,
		converstionRate: `float`,
		fee?: `float`
	}
}
```

##### 3.3.1.2.3.2. HTTP `400`

On failure, the MUST return an HTTP `400`, with a response body as follows. Refer to the `ErrorEnum` definition for all possible error values.

```
{
	error: `ErrorEnum`,
	minimumFiatAmount?: `float`,
	maximumFiatAmount?: `float`,
	minimumCryptoAmount?: `float`,
	maximumCryptoAmount?: `float`
}
```

##### 3.3.1.2.2. Semantics

All transfer out quotes require the `fiat`, `crypto`, and `fiatAccountType` query parameters, and exactly *one of* `fiatAmount` or `cryptoAmount`. If these requirements
are not met, the server MUST return an HTTP `400` error. If the server responds with an HTTP `200`, the provider MUST support a transfer out for the requested quote. If
the requested quote is not supported, the server MUST return an HTTP `400` error.

###### 3.3.1.2.2.1. Success

A successful response indicates that the provider is able to perform a transfer for the requested quote.
If `fiatAmount` is provided, the `quote.amount` field returned in the success body MUST correspond to the amount of crypto the user must provide
in order to receive `fiatAmount` worth of the selected fiat currency. If `cryptoAmount` is provided, the `quote.amount` field MUST correspond to the amount
of fiat currency the user should expect to receive by providing `cryptoAmount` worth of the selected crypto currency.

The `quote.conversionRate` field MUST always represent the cost of a unit crypto type with respect to the selected fiat type.

The `quote.fee` field is an optional return value, used to represent an optional fixed fee for the transfer. A server MAY choose to include this in the response body, though it
MUST be included if the provider requires a fee for the transfer. For transfers out, this fee is assumed to be denominated in the selected `cryptoType`.

The `fiatType`, `cryptoType`, `fiatAmount`, `cryptoAmount`, and `fiatAccountType` fields in the response body MUST correspond to the query parameters provided to the endpoint.

###### 3.3.1.2.2.2. Failure

On failure, this endpoint MUST return an HTTP `400` error, along with an error code that details exactly why the quote failed.

###### 3.3.1.2.2.2.1. `GeoNotSupported`

If a quote is not supported due to geographical/region constraints, the server MUST return a `GeoNotSupported` error.

###### 3.3.1.2.2.2.2. `CryptoAmountTooLow`

If the user provides `cryptoAmount` in the query parameter, and the value is too low, the server MUST return a `CryptoAmountTooLow` error. The
server MAY also provide a `minimumCryptoAmount` value with the response body.

###### 3.3.1.2.2.2.3. `CryptoAmountTooHigh`

If the user provides `cryptoAmount` in the query parameter, and the value is too high, the server MUST return a `CryptoAmountTooHigh` error. The
server MAY also provide a `maximumCryptoAmount` value with the response body.

###### 3.3.1.2.2.2.4. `FiatAmountTooLow`

If the user provides `fiatAmount` in the query parameter, and the value is too low, the server MUST return a `FiatAmountTooLow` error. The
server MAY also provide a `minimumFiatAmount` value with the response body.

###### 3.3.1.2.2.2.5. `FiatAmountTooHigh`

If the user provides `fiatAmount` in the query parameter, and the value is too high, the server MUST return a `FiatAmountTooHigh` error. The
server MAY also provide a `maximumFiatAmount` value with the response body.

###### 3.3.1.2.2.2.6. `CryptoNotSupported`

If the provided `cryptoType` is unsupported, the server MUST return a `CryptoNotSupported` error.

###### 3.3.1.2.2.2.7. `FiatNotSupported`

If the provided `fiatType` is unsupported, the server MUST return a `FiatNotSupported` error.

### 3.3.2. KYC Endpoints

A CICO provider may wish to require different KYC types for a single user across separate transfers for various reasons. For exmaple, if
a user initiates a transfer from the US, the provider may request a particular type of KYC for the transfer. Later on, if the user initiates
a transfer from a different geo, the same provider may require a different type of KYC. The provider should store these KYC verifications to re-use
later in case the user returns to a geo where they have already provided an appropriate KYC type.

While validation is typically expected to be completed quickly and automatically, we assume that it is fundamentally an asynchronous process. As such,
the FiatConnect specification must support monitoring the status of an ongoing validation. While we require that a CICO provider be able to maintain
different types of KYC validations per-user, we require that they only maintain *one* record of each type for each user at a time. Otherwise, server-side
implementation would become significantly more difficult, likely requiring an idempotency key to initiate new KYC verifications.

#### 3.3.2.1. `GET /kyc/schema`

The `GET /kyc/schema` endpoint is used to negotiate acceptable KYC schemas that a client may use to provide KYC data for their current geographical region.

##### 3.3.2.1.1. Responses

###### 3.3.2.1.1.1. HTTP `200`

On success, the server should return a response body with the following schema:

```
{
	kycRequired: `boolean`,
	schemas?: `KycSchemaEnum[]`
}
```

###### 3.3.2.1.1.2. HTTP `400`

On failure, the server should respond with the following response body:

```
{
	error: `ErrorEnum.GeoNotSupported`
}
```

##### 3.3.2.1.2. Semantics

This endpoint returns information on what KYC schemas, if any, are acceptable for the user's current geo. As previously mentioned, it is the server's
responsibility to determine the user's geographical location.

###### 3.3.2.1.2.1. Success

If KYC is required, the server MUST set `kycRequired` to `true` in the response body. Likewise, if no KYC is required, this value MUST be
`false`. If `kycRequired` is `true`, the `schemas` field MUST be present, and should contain a list of acceptable
schemas that can be used to transfer KYC data to the server. This list MUST contain at least one entry.

###### 3.3.2.1.2.2. Failure

This endpoint MUST only fail if transfers are not supported for a user's geo. In this case, the response body MUST contain the `GeoNotSupported` error code.

#### 3.3.2.2. `POST /kyc/:kycSchema`

The `POST /kyc/:kycSchema` endpoint allows a client to provide KYC data of a particular schema to the server for verification.

##### 3.3.2.2.1. Parameters

###### 3.3.2.2.1.1. Path Parameters

* `kycSchema`: {`KycSchemaEnum`} [REQUIRED]
  - The KYC schema being used in the request body

###### 3.3.2.2.1.2. Request Body

The request body schema for this endpoint must match the KYC schema selected in the path parameter.

##### 3.3.2.2.2. Responses

###### 3.3.2.2.2.1. HTTP `200`

On success, the server MUST return an HTTP `200` and respond with the following response schema:

```
{
	status: `KycStatusEnum`
}
```

###### 3.3.2.2.2.2. HTTP `400`

On failure, the server MUST respond with the following response schema:

```
{
	error: `ErrorEnum`
}
```

###### 3.3.2.2.2.3. HTTP `409`

On conflict, the server MUST respond with the following response schema:

```
{
	error: `ErrorEnum.ResourceExists`
}
```

##### 3.3.2.2.3. Semantics

This endpoint should accept data that the server will use to verify a user's KYC status. Upon receipt of valid data, the server SHOULD initiate a verification of the
provided data, likely using a third party KYC service. If the user's region as reported by the independent KYC verification process does not match their "provided" geo
(i.e., the one calculated from the caller's IP), the KYC verification SHOULD likely eventually be denied. Once a user submits KYC data, the server MUST maintain the status
of the verification. If a client has enabled webhooks for the API, the server MUST publish these statuses to the webhook endpoint asynchronously.

###### 3.3.2.2.3.1. Success

If the selected schema (via the path parameter) is valid in the user's geo, and the request body exactly matches the selected schema, and the server does not already
have a KYC verification (either pending or complete) on file for the user, the server MUST return an HTTP `200`. The response body MUST contain the current state of the
KYC verification in the `status` field.

###### 3.3.2.2.3.2. Failure

The server may fail this request for two primary reasons; if the query is poorly formed, or if the server has already received KYC data for the user in the selected schema.

###### 3.3.2.2.3.2.1. `UnsupportedSchema`

If the KYC schema selected in the path parameter is not supported for the user's geo, the server MUST return an `UnsupportedSchema` error.

###### 3.3.2.2.3.2.2. `InvalidSchema`

If the KYC schema selected in the path parameter is supported for the user's geo, but the request body does not match the selected schema, the server
MUST return an `InvalidSchema` error.

###### 3.3.2.2.3.2.3. `ResourceExists`

If the server already has KYC data on file for the user in the selected schema, the server MUST return a `ResourceExists` error.

#### 3.3.2.3. `GET /kyc/:kycSchema/status`

The `GET /kyc/:kycSchema/status` endpoint is used to query the status of an ongoing, completed, or expired KYC verification for a particular KYC schema type.
Note that these statuses MUST also be made available via webhook, if supported by the server and configured by the client.

##### 3.3.2.3.1. Parameters

###### 3.3.2.3.1.1. Path Parameters

* `kycSchema`: {`KycSchemaEnum`} [REQUIRED]
  - The KYC schema used for the verification whose status is being requested

##### 3.3.2.3.2. Responses

###### 3.3.2.3.2.1. HTTP `200`

On success, the server MUST return an HTTP `200` status code, with the following response body:

```
{
	status: `KycStatusEnum`
}
```

###### 3.3.2.3.2.2. HTTP `404`

If the resource does not exist, the server MUST return an HTTP `404` status code, with the following response body:

```
{
	error: `ErrorEnum.ResourceNotFound`
}
```

##### 3.3.2.3.3. Semantics

If KYC data has been submitted for a particular schema type, this endpoint MUST return the current status of the KYC verification for that schema.

###### 3.3.2.3.3.1. Success

On success, the server MUST return the status of the KYC verification.

###### 3.3.2.3.3.2. Failure

This endpoint may fail if there is no record of KYC data being submitted for the requested schema.

###### 3.3.2.3.3.2.1. `ResourceNotFound`

If the server has no data on file for a KYC verification of the given schema, the server MUST return a `ResourceNotFound` error.

#### 3.3.2.4. `DELETE /kyc/:kycSchema`

The `DELETE /kyc/:kycSchema` endpoint is used to delete a KYC record for a particular KYC schema.

##### 3.3.2.4.1. Parameters

###### 3.3.2.4.1.1. Path Parameters

* `kycSchema`: {`KycSchemaEnum`} [REQUIRED]
  - The KYC schema to delete from the server

##### 3.3.2.4.2. Responses

###### 3.3.2.4.2.1. HTTP `200`

On success, the server MUST return an HTTP `200` status code, with an empty response body.

###### 3.3.2.4.2.2. HTTP `404`

If the resource does not exist, the server MUST return an HTTP `404` status code, with the following response body:

```
{
	error: `ErrorEnum.ResourceNotFound`
}
```

##### 3.3.2.4.3. Semantics

If KYC data has been submitted for a particular schema type, this endpoint MUST delete all record of the data from the server. If a user wishes to
engage in a transfer that requires this KYC later, the user must resubmit the appropriate KYC information.

###### 3.3.2.4.3.1. Success

On succesful deletion, the server MUST return an HTTP `200` status code.

###### 3.3.2.4.3.2. Failure

This endpoint may fail if there is no record of KYC data for the requested schema.

###### 3.3.2.4.3.2.1. `ResourceNotFound`

If the server has no data on file for a KYC verification of the given schema, the server MUST return a `ResourceNotFound` error.

### 3.3.3. Fiat Account Endpoints

FiatConnect's Fiat Account model consists of two primary concepts; the notion of an "account schema", and an "account type". Account schemas refer to the precise
*representation* of an account; i.e., the exact structure and fields required when communicating Fiat Account objects from client to server. All account schemas share
a set of base fields that are common to *all* schemas, such as "name", "institution", etc. Each account must have an "account type" associated with it, selected from
one of the account types supported by FiatConnect (see `FiatAccountTypeEnum` for an initial list). For example, one can imagine two different account schemas,
`CheckingAccountSchema1` and `CheckingAccountSchema2`. These two schemas *both* would be associated with the, e.g., `FiatAccountTypeEnum.CheckingAccount` account type,
but might require different data; it might be the case that different geos have different representations for communicating checking account information.

Similar to KYC schemas, providers may require different sorts of fiat (i.e., *bank*) accounts depending on both the geo of the client, *and* the particular details
of the transfer. CICO providers MUST be able to maintain multiple fiat accounts on record for each user. CICO providers must advertise a list of
valid account types allowed for a particular geo & transfer.

In order for clients to uniquely select accounts on file when communicating with the server, the server MUST instrument their account records
with unique identifiers (i.e. a UUID) that the client can use to select an account. These identifiers MAY be globally unique across all users, but need not be. A server
MUST NOT allow a user to interact with fiat account identifiers (i.e., selecting one for a transfer) for accounts not owned by that user.

#### 3.3.3.1. `GET /accounts/schema`

The `GET /acccounts/schema` endpoint is used to negotiate what fiat account types are acceptable for some particular transfer details, as well as what fiat account schemas
can be used to store fiat accounts of those types with the server.

##### 3.3.3.1.1. Parameters

###### 3.3.3.1.1.1. Query Parmaters

* `transferType`: {`TransferTypeEnum`} [REQUIRED]
  - The desired transfer type
* `fiatType`: {`FiatTypeEnum`} [REQUIRED]
  - The fiat type used for the transfer
* `cryptoType`: {`CryptoTypeEnum`} [REQUIRED]
  - The crypto type used for the transfer
* `fiatAmount`: {`float`}
  - The amount of fiat used for the transfer
* `cryptoAmount`: {`float`}
  - The amount of crypto used for the transfer

##### 3.3.3.1.2. Responses

###### 3.3.3.1.2.1. HTTP `200`

On success, the server should return an HTTP `200` status code along with a response body in the following format. Informally,
this is a map from fiat account types to lists of schemas allowed to be used to add an account of that type.

```
{
	[FiatAccountTypeEnum]: [FiatAccountSchemaEnum[]]
}
```
##### 3.3.3.1.3. Semantics

This endpoint should be used by the client before adding a new fiat account to use for a transfer, in order to negotiate with the
server what fiat account schemas are permitted. The query parameters specified for this endpoint are similar to the ones specified for
the quote endpoints, and should be interpreted identically.

###### 3.3.3.1.3.1. Success

On success, the server MUST return a mapping from fiat account types to lists of schemas that the client may use to add a new account of that
type. This is expected to vary by geographical region as well as transfer details provided by the query parameters. If no fiat account types are
allowed for this transfer (meaning the transfer is not possible due to some combination of transfer parameters and user geo), the response body should
be empty. If a fiat account type is present as a key in the response body, it MUST NOT map to an empty list.

#### 3.3.3.2. `POST /accounts/:fiatAccountSchema`

The `POST /accounts/:fiatAccountSchema` endpoint is used to store a new fiat account on file with the server.

##### 3.3.3.2.1. Parameters

###### 3.3.3.2.1.1. Path Parameters

* `fiatAccountSchema`: {`FiatAccountSchemaEnum`} [REQUIRED]
  - The fiat account schema to use to add the fiat account

###### 3.3.3.2.1.2. Request Body

The request body schema for this endpoint must match the fiat accout schema  selected in the path parameter.

##### 3.3.3.2.2. Responses

###### 3.3.3.2.2.1. HTTP `200`

On success, the server MUST respond with an HTTP `200` status code, along with a response body with the following schema:

```
{
	fiatAccountId: `string`,
	name: `string`,
	institution: `string`,
	fiatAccountType: `FiatAccountTypeEnum`
}
```

###### 3.3.3.2.2.2. HTTP `400`

On failure, the server MUST return an HTTP `400` error code, along with a response body with the following schema:

```
{
	error: `ErrorEnum`
}
```

###### 3.3.3.2.2.3. HTTP `409`

On conflict, the server MUST return an HTTP `400` error code, along with a response body with the following schema:

```
{
	error: `ErrorEnum.ResourceExists`
}
```

##### 3.3.3.2.3. Semantics

This endpoint is used to add a new fiat accout for a user.

###### 3.3.3.2.3.1. Success

On success, the server MUST return metadata associated with the account. The server MUST also generate a unique `fiatAccountId` that the
client can later use to reference the account on file.

###### 3.3.3.2.3.1. Failure

This endpoint may fail for two main reasons; if there's an issue with the selected schema/payload, or if the account already exists for
the user. The server MAY implement checks to ensure that multiple accounts whose "unique fields" are the same cannot be added for a user.
The semantics of these checks may be different for each fiat account schema/fiat account type, and are up to the server to implement. For
example, a user may not be allowed to add two debit cards with the same numbers.

###### 3.3.3.2.3.1.1. `UnsupportedSchema`

If the schema selected in the path parameter is not supported by the server, the server MUST return an `UnsupportedSchema` error.

###### 3.3.3.2.3.1.2. `InvalidSchema`

If the schema of the request body does not match the schema selected by the path parameter, the server MUST return an `InvalidSchema` error.

###### 3.3.3.2.3.1.3. `ResourceExists`

If the server determines that the user is trying to add an accout that is fundamentally identical to one that the user has already added, the
server MUST return a `ResourceExists` error.

#### 3.3.3.3. `GET /accounts`

The `GET /accounts` endpoint is used to return a list of all fiat accounts on file for a user; it can also be used to just return the fiat accounts
on file for a user that are allowed to be used for a particular transfer. This is useful for when a user is selecting an account to use for a transfer.

##### 3.3.3.3.1. Parameters

###### 3.3.3.3.1.1. Query Parmaters

* `transferType`: {`TransferTypeEnum`}
  - The desired transfer type
* `fiatType`: {`FiatTypeEnum`}
  - The fiat type used for the transfer
* `cryptoType`: {`CryptoTypeEnum`}
  - The crypto type used for the transfer
* `fiatAmount`: {`float`}
  - The amount of fiat used for the transfer
* `cryptoAmount`: {`float`}
  - The amount of crypto used for the transfer

##### 3.3.3.3.2. Responses

###### 3.3.3.3.2.1. HTTP `200`

On success, the server MUST respond with an HTTP `200` status code, along with a response body with the following schema. Informally,
this is a mapping from fiat account types that the user has on file to metadata about those accounts.

```
{
	[FiatAccountTypeEnum]: [{
		fiatAccountId: `string`,
		name: `string`,
		institution: `string`,
		fiatAccountType: `FiatAccountTypeEnum`
	}]
}
```

##### 3.3.3.3.3. Semantics

This endpoint can be used to either return *all* fiat accounts that a user has on file, or *just* those accounts that a user has on file
that are allowed to be used for a transfer, given that transfer's details. If clients provide no query parameters, the server MUST
return all of the user's accounts. Otherwise, the client must provide each of `transferType`, `fiatType`, `cryptoType`, and ONE of `fiatAmount`
or `cryptoAmount`. If the client sends these parameters, the server MUST filter the response to just include the user's fiat accounts which
are acceptable to use for the given transfer details.

###### 3.3.3.3.3.1. Success

On success, this endpoint MUST return a mapping from fiat account types to metadata about fiat accounts that the user has on file, optionally
filtered based on whether or not they're valid for use for the given transfer details.

#### 3.3.3.4. `DELETE /account/:fiatAccountId`

The `DELETE /account/:fiatAccountId` endpoint is used to delete a user's fiat account from the server.

##### 3.3.3.4.1. Parameters

###### 3.3.3.4.1.1. Path Parameters

* `fiatAccountId`: {`string`} [REQUIRED]
  - The internal fiat account ID to delete

##### 3.3.3.4.2. Responses

###### 3.3.3.4.2.1. HTTP `200`

On success, the server MUST return an HTTP `200` status code, with an empty response body.

###### 3.3.3.4.2.2 HTTP. `404`

If the resource does not exist, the server MUST return an HTTP `404` error code, along with the following repsonse body.

```
{
	error: `ErrorEnum.ResourceNotFound`
}
```

##### 3.3.3.4.3. Semantics

This endpoint allows a user to delete a fiat account record from the server. The user MUST only be able to delete accounts which they have
added themselves. In other words, if a server receives a `fiatAccountId` that corresponds to an account added by a user other than the one
calling the endpoint, the server MUST return an HTTP `404` error code.

###### 3.3.3.4.3.1. Success

On successful deletion of the fiat account record, the server MUST return an HTTP `200` status code.

###### 3.3.3.4.3.1. Failure

The server may fail if there is no fiat account on file with the provided `fiatAccountId` for the current user.

###### 3.3.3.4.3.1.1. `ResourceNotFound`

If no fiat account is on file with the provided `fiatAccountId` for the current user, the server MUST return a `ResourceNotFound` error.

### 3.3.4. Transfer Endpoints

Once a user has created a fiat account with a CICO provider, they may begin a transfer. CICO providers MUST reject a transfer request if a fiat account identifier is
provided that refers to an account type that is not allowed in the user's geo. A CICO provider MUST allow concurrent transfers for a single user. (This allows
a user to try a new transfer in case one is hanging for whatever reason.)

Since transfers are asynchronous and may be concurrent, this specification requires that CICO providers accept an idempotency key in case the request/response
is lost in transit and the client never receives a response. An idempotency key will allow the client to safely attempt to re-initiate a transfer without running
the risk of creating a duplicate transaction. Idempotency keys MUST be implemented as request headers according to IETF's NWG's
[draft proposal](https://datatracker.ietf.org/doc/html/draft-ietf-httpapi-idempotency-key-header-00).

#### 3.3.4.1. `POST /transfer/in`

The `POST /transfer/in` endpoint is used to initiate a new transfer in from fiat to crypto.

##### 3.3.4.1.1. Parameters

###### 3.3.4.1.1.1. Headers

* `Idempotency-Key: <idempotency-key>`
  - An idempotency key, generated by the client

###### 3.3.4.1.1.2. Request Body

The request body must contain the following fields:

* `fiatType`: {`FiatTypeEnum`} [REQUIRED]
  - The type of fiat currency to transfer in from.
* `cryptoType`: {`CryptoTypeEnum`} [REQUIRED]
  - The type of cryptocurrency to transfer in to.
* `amount`: {`float`}: [REQUIRED]
  - The amount of fiat currency to transfer in.
* `fiatAccountId`: {`string`} [REQUIRED]
  - The fiat account ID to use for the transfer.

##### 3.3.4.1.2. Responses

###### 3.3.4.1.2.1. HTTP `200`

On a successfully initiated transfer in request, the server MUST respond with an HTTP `200` status code, along with the following response body:

```
{
	transferId: `string`,
	transferStatus: `TransferStatusEnum`
}
```

###### 3.3.4.1.2.2. HTTP `400`

If the transfer parameters are invalid, or if the transfer is not possible for reasons *other* than the fiat account not existing, the server MUST
return an HTTP `400` error code, along with the following response body:

```
{
	error: `ErrorEnum`
}
```

###### 3.3.4.1.2.3. HTTP `404`

If the selected fiat account does not exist, the server MUST respond with an HTTP `404` error code, along with the following response body:

```
{
	error: `ErrorEnum.ResourceNotFound`
}
```

###### 3.3.4.1.2.4. Idempotency Key Errors

The server MUST follow the semantics outlined in this [draft proposal](https://datatracker.ietf.org/doc/html/draft-ietf-httpapi-idempotency-key-header-00) with
respect to idempotency key errors.

##### 3.3.4.1.3. Semantics

This endpoint allows a user to initiate a new transfer in request. The server MUST support idempotency keys, and MUST NOT accept any requests which lack them.
If a user provides a `fiatAccountId` that refers to an account they have on file that is allowed for the transfer, and the transfer parameters are acceptable,
and the user has non-expired KYC on file, the server MUST respond with an HTTP `200` and initiate the transfer. When a new transfer is initiated, the server MUST
generate a transfer ID that the client can use to monitor the progress of the transfer. If the client has enabled webhooks, and the server supports them, the server
MUST call the user-specified webhook before returning an HTTP `200`.

###### 3.3.4.1.3.1. Success

On success, the server MUST return the `transferId` associated with the pending transfer, as well as the initial status of the transfer. If supported and configured, the
endpoint MUST also report the initial status of the transfer to the client-specified webhook.

###### 3.3.4.1.3.2. Failure

This endpoint may fail for a number of reasons; the error code returned by the endpoint will vary depending on failure reason.

###### 3.3.4.1.3.2.1. `KycExpired`

If a user's KYC has expired for their current geo, the server MUST reject the transfer and return a `KycExpired` error.

###### 3.3.4.1.3.2.2. `TransferNotAllowed`

If a transfer is not allowed for a generic reason (such as unacceptable transfer parameters) the server MUST reject the transfer and return a `TransferNotAllowed` error.

###### 3.3.4.1.3.2.3. `ResourceNotFound`

If the selected `fiatAccountId` is not found for the current user, the server MUST reject the transfer and return a `ResourceNotFound` error.

#### 3.3.4.2. `POST /transfer/out`

The `POST /transfer/out` endpoint is used to initiate a new transfer out from crypto to fiat.

##### 3.3.4.2.1. Parameters

###### 3.3.4.2.1.1. Headers

* `Idempotency-Key: <idempotency-key>`
  - An idempotency key, generated by the client

###### 3.3.4.2.1.2. Request Body

The request body must contain the following fields:

* `fiatType`: {`FiatTypeEnum`} [REQUIRED]
  - The type of fiat currency to transfer out to.
* `cryptoType`: {`CryptoTypeEnum`} [REQUIRED]
  - The type of cryptocurrency to transfer out from.
* `amount`: {`float`}: [REQUIRED]
  - The amount of cryptocurrency to transfer out.
* `fiatAccountId`: {`string`} [REQUIRED]
  - The fiat account ID to use for the transfer.

##### 3.3.4.2.2. Responses

###### 3.3.4.2.2.1. HTTP `200`

On a successfully initiated transfer out request, the server MUST respond with an HTTP `200` status code, along with the following response body:

```
{
	transferId: `string`,
	transferStatus: `TransferStatusEnum`
}
```

###### 3.3.4.2.2.2. HTTP `400`

If the transfer parameters are invalid, or if the transfer is not possible for reasons *other* than the fiat account not existing, the server MUST
return an HTTP `400` error code, along with the following response body:

```
{
	error: `ErrorEnum`
}
```

###### 3.3.4.2.2.3. HTTP `404`

If the selected fiat account does not exist, the server MUST respond with an HTTP `404` error code, along with the following response body:

```
{
	error: `ErrorEnum.ResourceNotFound`
}
```

###### 3.3.4.2.2.4. Idempotency Key Errors

The server MUST follow the semantics outlined in this [draft proposal](https://datatracker.ietf.org/doc/html/draft-ietf-httpapi-idempotency-key-header-00) with
respect to idempotency key errors.

##### 3.3.4.2.3. Semantics

This endpoint allows a user to initiate a new transfer out request. The server MUST support idempotency keys, and MUST NOT accept any requests which lack them.
If a user provides a `fiatAccountId` that refers to an account they have on file that is allowed for the transfer, and the transfer parameters are acceptable,
and the user has non-expired KYC on file, the server MUST respond with an HTTP `200` and initiate the transfer. When a new transfer is initiated, the server MUST
generate a transfer ID that the client can use to monitor the progress of the transfer. If the client has enabled webhooks, and the server supports them, the server
MUST call the user-specified webhook before returning an HTTP `200`.

###### 3.3.4.2.3.1. Success

On success, the server MUST return the `transferId` associated with the pending transfer, as well as the initial status of the transfer. If supported and configured, the
endpoint MUST also report the initial status of the transfer to the client-specified webhook.

###### 3.3.4.2.3.2. Failure

This endpoint may fail for a number of reasons; the error code returned by the endpoint will vary depending on failure reason.

###### 3.3.4.2.3.2.1. `KycExpired`

If a user's KYC has expired for their current geo, the server MUST reject the transfer and return a `KycExpired` error.

###### 3.3.4.2.3.2.2. `TransferNotAllowed`

If a transfer is not allowed for a generic reason (such as unacceptable transfer parameters) the server MUST reject the transfer and return a `TransferNotAllowed` error.

###### 3.3.4.2.3.2.3. `ResourceNotFound`

If the selected `fiatAccountId` is not found for the current user, the server MUST reject the transfer and return a `ResourceNotFound` error.

#### 3.3.4.3. `GET /transfer/in/:transferId/status`

The `GET /transfer/in/:transferId/status` endpoint is used to get the status of an ongoing or completed transfer in, as well as metadata about
the transfer.

##### 3.3.4.3.1. Parameters

###### 3.3.4.3.1.1. Path Parameters

* `transferId`: {`string`} [REQUIRED]
  - The transfer ID for the transfer in whose status to return.

##### 3.3.4.3.2. Responses

###### 3.3.4.3.2.1. HTTP `200`

On success, the server MUST return an HTTP `200` status code, along with a response body with the following schema:

```
{
	status: `TransferStatusEnum`,
	transferType: `TransferTypeEnum.TransferIn`,
	fiatType: `FiatTypeEnum`,
	cryptoType: `CryptoTypeEnum`,
	amountProvided: `float`,
	amountReceived: `float`,
	fee?: `float`,
	fiatAccountId: `string`
}
```

###### 3.3.4.3.2.2. HTTP `404`

If the user has no transfer in on file with the provided `transferId`, the server MUST return an HTTP `200` status code,
along with a response body with the following schema:

```
{
	error: `ErrorEnum.ResourceNotFound`
}
```

##### 3.3.4.3.3. Semantics

This endpoint is meant to be used by clients to monitor the status of an ongoing or completed transfer in request, as well
as retrieve details about the transfer. Note that servers MUST also make this status information available via webhooks,
if the server supports webhooks and if the client has configured them.

###### 3.3.4.3.3.1. Success

If the user has a transfer in record on file with the corresponding `transferId`, the server MUST respond with an HTTP
`200` status code. The fields in the success response body correspond to the details of the submitted transfer. In particular,
`amountProvided` refers to the amount of fiat that the user has provided for the transfer. `amountRecieved` refers to the amount
of crypto that the server will be crediting to the user. `fee`, if present refers to the fee, if any, associated with the transfer,
denominated in fiat.

###### 3.3.4.3.3.2. Failure

This endpoint MUST fail when the user has no transfer in on file with the provided `transferId`.

###### 3.3.4.3.3.2.1. `ResourceNotFound`

If the user has no transfer in on file with the provided `transferId`, the server MUST return a `ResourceNotFound` error.

#### 3.3.4.4. `GET /transfer/out/:transferId/status`

The `GET /transfer/out/:transferId/status` endpoint is used to get the status of an ongoing or completed transfer out, as well as metadata about
the transfer.

##### 3.3.4.4.1. Parameters

###### 3.3.4.4.1.1. Path Parameters

* `transferId`: {`string`} [REQUIRED]
  - The transfer ID for the transfer out whose status to return.

##### 3.3.4.4.2. Responses

###### 3.3.4.4.2.1. HTTP `200`

On success, the server MUST return an HTTP `200` status code, along with a response body with the following schema:

```
{
	status: `TransferStatusEnum`,
	transferType: `TransferTypeEnum.TransferOut`,
	fiatType: `FiatTypeEnum`,
	cryptoType: `CryptoTypeEnum`,
	amountProvided: `float`,
	amountReceived: `float`,
	fee?: `float`,
	fiatAccountId: `string`
}
```

###### 3.3.4.4.2.2. HTTP `404`

If the user has no transfer out on file with the provided `transferId`, the server MUST return an HTTP `200` status code,
along with a response body with the following schema:

```
{
	error: `ErrorEnum.ResourceNotFound`
}
```

##### 3.3.4.4.3. Semantics

This endpoint is meant to be used by clients to monitor the status of an ongoing or completed transfer out request, as well
as retrieve details about the transfer. Note that servers MUST also make this status information available via webhooks,
if the server supports webhooks and if the client has configured them.

###### 3.3.4.4.3.1. Success

If the user has a transfer out record on file with the corresponding `transferId`, the server MUST respond with an HTTP
`200` status code. The fields in the success response body correspond to the details of the submitted transfer. In particular,
`amountProvided` refers to the amount of crypto that the user has provided for the transfer. `amountRecieved` refers to the amount
of fiat that the server will be crediting to the user. `fee`, if present refers to the fee, if any, associated with the transfer,
denominated in crypto.

###### 3.3.4.4.3.2. Failure

This endpoint MUST fail when the user has no transfer out on file with the provided `transferId`.

###### 3.3.4.4.3.2.1. `ResourceNotFound`

If the user has no transfer out on file with the provided `transferId`, the server MUST return a `ResourceNotFound` error.

# 4. Webhooks

As mentioned throughout this document, CICO providers MAY support sending status updates to webhooks, whose URL is configurable by the client.
CICO providers may generate an API token that the client can use to authenticate against the FiatConnect API, allowing the server to know
where to send status updates to. This section outlines the details of webhook request syntax, as well as how the server must sign them to guarantee
the client that the requests are genuine.

## 4.1. Webhook Requests

Webhook requests made from the server MUST be in a standard format, outlined below:

```
{
	eventType: `WebhookEventTypeEnum`,
	provider: `string`,
	eventId: `string,
	timestamp: `string`,
	payload: {WebhookKycStatusEventSchema | WebhookTransferInStatusEventSchema | WebhookTransferOutStatusEventSchema}
}
```

The `eventType` field corresponds to the type of event being sent to the webhook. The `provider` field corresponds to the name of the provider
that this webhook event is coming from. This is important to include, since the client's webhook handler may receive events from multiple
different providers. The `eventId` field is a unique event ID generated by the server. The client may use this field as an idempotency key to
ensure that events are not processed more than once. The `timestamp` field represents the exact time this event was initially generated, represented
as time since Epoch in seconds. The `payload` field contains the actual event data, and will vary depending on the selected `eventType`.

Servers SHOULD implement retries with exponential backoff when sending payloads to a client's webhook. Once a server has received an HTTP `200`
status code from the client's webhook, it MUST stop retrying.

## 4.2. Webhook Request Signing

Requests made to a client's webhook handler MUST be signed, in order to prevent non-genuine webhook requests sent by malicious actors from being
interpreted as genuine. To facilitate this, the CICO provider MUST generate a secret key that it shares with the client developer that can be used
to recompute and verify the signature provided by the server.

Server requests to webhooks MUST contain a `FiatConnect-Signature` header containing an HMAC. Using the private key that the CICO provider shares
with the client developer, a webhook can verify that a request is genuine by recomputing the digest and comparing it against the one provided by
the CICO provider.

The `FiatConnect-Signature` header MUST contain two comma-separated key-value pairs. The first is of the form `t=<unix_timestamp`, and represents
the UNIX timestamp that the request was sent. The second will be of the form `s=<signature>`, and contain the signature itself. The signature
MUST be computed from the shared webhook private key and a dot-separated string consisting of the UNIX timestamp joined with the request body.
This signature verification design is based off of [Persona's webhook documentation](https://docs.withpersona.com/docs/best-practices).

# 5. AML Considerations

Anti-Money Laundering (AML) checks are an important part of regulatory compliance in the financial services industry. This proposal, however,
does not require CICO providers to implement such checks. Providers MAY implement their own AML checks if they wish, according to local law.
It is likely that by requiring KYC verificaiton, CICO providers will be able to collect enough relevant information on the user in order to
facilitate AML checks, if required.

# 6. Definitions

This document references a number of definitions, all of which are enumerated in their entirety below. There are two "types" of definitions; those
which are static, and *not* subject to change upon this proposal's acceptance, and those that are dynamic, and meant to be extended by the community.

## 6.1. Static Definitions

### 6.1.1. `KycStatusEnum`

An enum listing KYC verification statuses.

```
[
	`KycNotCreated`,
	`KycPending`,
	`KycApproved`,
	`KycDenied`,
	`KycExpired`
]
```

### 6.1.2. `ErrorEnum`

An enum listing the error types used by various endpoints.

```
[
	`GeoNotSupported`,
	`CryptoAmountTooLow`,
	`CryptoAmountTooHigh`,
	`FiatAmountTooLow`,
	`FiatAmountTooHigh`,
	`CryptoNotSupported`,
	`FiatNotSupported`,
	`UnsupportedSchema`,
	`InvalidSchema`,
	`ResourceExists`,
	`ResourceNotFound`,
	`TransferNotAllowed`,
	`KycExpired`
]
```

### 6.1.3. `TransferTypeEnum`

An enum listing transfer types.

```
[
	`TransferIn`,
	`TransferOut`
]
```

### 6.1.3. `WebhookEventTypeEnum`

An enum listing payload types for webhook status updates.

```
[
	`WebhookKycStatusEvent`
	`WebhookTransferInStatusEvent`,
	`WebhookTransferOutStatusEvent`
]
```

### 6.1.4. `TransferStatusEnum`

An enum listing the types of transfer statuses recognized by FiatConnect.

```
[
	`TransferStarted`,
	`TransferPending`,
	`TransferComplete`,
	`TransferFailed`
]
```
## 6.2. Dynamic Definitions

### 6.2.1. `FiatTypeEnum`

An enum listing the types of fiat currencies supported by FiatConnect.

```
[
	`USD`,
	`EUR`
]
```

### 6.2.2. `CryptoTypeEnum`

An enum listing the types of crypto tokens suppored by FiatConnect.

```
[
	`cUSD`,
	`cEUR`,
	`CELO`
]
```

### 6.2.3. `KycSchemaEnum`

An enum listing the KYC schema types recognized by the FiatConnect specification. To be determined once initial KYC schemas are known.

### 6.2.4. `FiatAccountTypeEnum`

An enum listing the *types* of Fiat Accounts recognized by the FiatConnect specification. A Fiat Account Type is a property of each Fiat Account Schema, and
represents what *kind* of account that schema represents; e.g., Debit Card, Credit Card, Checking Account, etc.

```
[
	`CheckingAccount`,
	`DebitCard`,
	`CreditCard`
]
```

### 6.2.5. `FiatAccountSchemaEnum`

An enum listing the Fiat Account schemas recognized by the FiatConnect specification. To be determined once initial Fiat Account schemas are known.

## 6.3. Initial Entity Support

This section details the initial entity support for FiatConnect. In particular, the initial KYC and Fiat Account schemas that the FiatConnect specification
should support for initial integrators. This section is pending further research and feedback from potential CICO providers.

# 7. References

## 7.1. Normative References

### 7.1.1. [RFC2119]

Bradner, S., "Key words for use in RFCs to Indicate Requirement Levels", BCP 14, RFC 2119, DOI 10.17487/RFC2119, March 1997, <https://www.rfc-editor.org/info/rfc2119>.

### 7.1.2. [RFC8174]

Leiba, B., "Ambiguity of Uppercase vs Lowercase in RFC 2119 Key Words", BCP 14, RFC 8174, DOI 10.17487/RFC8174, May 2017, <https://www.rfc-editor.org/info/rfc8174>.

### 7.1.3. [RFC7519]

M. Jones, "JSON Web Token", RFC7519, May 2015, <https://datatracker.ietf.org/doc/html/rfc7519>.

## 7.2. Informative References

### 7.2.1. Data Encryption Key

Celo Foundation, "Data Encryption Key", <https://docs.celo.org/developer-resources/contractkit/data-encryption-key>.

### 7.2.2. Webhook Best Practices

Persona, "Best Practices", <https://docs.withpersona.com/docs/best-practices>.

### 7.2.3. Idempotency Keys

J. Jena, The Idempotency-Key HTTP Header Field, July 2021, <https://datatracker.ietf.org/doc/html/draft-ietf-httpapi-idempotency-key-header-00>
