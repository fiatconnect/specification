# FiatConnect CICO Provider API Specification "RFC"

# 0. Table of Contents

- [FiatConnect CICO Provider API Specification "RFC"](#fiatconnect-cico-provider-api-specification-rfc)
- [0. Table of Contents](#0-table-of-contents)
- [1. Introduction](#1-introduction)
  - [1.1. A Note on Celo & Multi-Chain Support](#11-a-note-on-celo--multi-chain-support)
  - [1.2. Notational Conventions](#12-notational-conventions)
- [2. Lifecycle of a Transfer](#2-lifecycle-of-a-transfer)
  - [2.1. Quote](#21-quote)
  - [2.2. KYC](#22-kyc)
    - [2.2.1. KYC Status Monitoring](#221-kyc-status-monitoring)
  - [2.3. Fiat Accounts](#23-fiat-accounts)
  - [2.4. Creating the Transfer](#24-creating-the-transfer)
    - [2.4.1. Transfer Status Monitoring](#241-transfer-status-monitoring)
- [3. API Specification](#3-api-specification)
  - [3.1. Geolocation](#31-geolocation)
  - [3.2. Clock Synchronization](#32-clock-synchronization)
    - [3.2.1. `GET /clock`](#321-get-clock)
      - [3.2.1.1. Parameters](#3211-parameters)
      - [3.2.1.2. Responses](#3212-responses)
        - [3.2.1.2.1. HTTP `200`](#32121-http-200)
      - [3.2.1.3. Semantics](#3213-semantics)
        - [3.2.1.3.1. Success](#32131-success)
  - [3.3. Authentication & Authorization](#33-authentication--authorization)
    - [3.3.1. Sign-In With Ethereum](#331-sign-in-with-ethereum)
      - [3.3.1.1. SIWE Resources](#3311-siwe-resources)
      - [3.3.1.2. SIWE Sessions](#3312-siwe-sessions)
      - [3.3.1.3. SIWE Message Format](#3313-siwe-message-format)
      - [3.3.1.4. Session Cookies](#3314-session-cookies)
      - [3.3.1.5. `POST /auth/login`](#3315-post-authlogin)
        - [3.3.1.5.1. Parameters](#33151-parameters)
          - [3.3.1.5.1.1. Request Body](#331511-request-body)
        - [3.3.1.5.2. Responses](#33152-responses)
          - [3.3.1.5.2.1. HTTP `200`](#331521-http-200)
          - [3.3.1.5.2.2. HTTP `401`](#331522-http-401)
        - [3.3.1.5.3. Semantics](#33153-semantics)
          - [3.3.1.5.3.1. Success](#331531-success)
          - [3.3.1.5.3.2. Failure](#331532-failure)
          - [3.3.1.5.3.2.1. `InvalidSignature`](#3315321-invalidsignature)
          - [3.3.1.5.3.2.2. `InvalidParameters`](#3315322-invalidparameters)
          - [3.3.1.5.3.2.3. `ContractLoginNotSupported`](#3315323-contractloginnotsupported)
          - [3.3.1.5.3.2.4. `NonceInUse`](#3315324-nonceinuse)
          - [3.3.1.5.3.2.5. `IssuedTooEarly`](#3315325-issuedtooearly)
          - [3.3.1.5.3.2.6. `ExpirationTooLong`](#3315326-expirationtoolong)
      - [3.3.1.6. Using Sessions](#3316-using-sessions)
        - [3.3.1.6.1. Non-Privileged Endpoints](#33161-non-privileged-endpoints)
        - [3.3.1.6.2. Privileged Endpoints](#33162-privileged-endpoints)
    - [3.3.2. Client API Key](#332-client-api-key)
    - [3.3.3. HTTPS](#333-https)
  - [3.4. Formal Specification](#34-formal-specification)
    - [3.4.1. Quote Endpoints](#341-quote-endpoints)
      - [3.4.1.1. `POST /quote/in`](#3411-post-quotein)
        - [3.4.1.1.1. Parameters](#34111-parameters)
          - [3.4.1.1.1.1. Request body](#341111-request-body)
        - [3.4.1.1.2. Responses](#34112-responses)
          - [3.4.1.1.2.1. HTTP `200`](#341121-http-200)
        - [3.4.1.1.2.2. HTTP `400`](#341122-http-400)
        - [3.4.1.1.3. Semantics](#34113-semantics)
          - [3.4.1.1.3.1. Success](#341131-success)
          - [3.4.1.1.3.2. Failure](#341132-failure)
          - [3.4.1.1.3.2.1. `GeoNotSupported`](#3411321-geonotsupported)
          - [3.4.1.1.3.2.2. `CryptoAmountTooLow`](#3411322-cryptoamounttoolow)
          - [3.4.1.1.3.2.3. `CryptoAmountTooHigh`](#3411323-cryptoamounttoohigh)
          - [3.4.1.1.3.2.4. `FiatAmountTooLow`](#3411324-fiatamounttoolow)
          - [3.4.1.1.3.2.5. `FiatAmountTooHigh`](#3411325-fiatamounttoohigh)
          - [3.4.1.1.3.2.6. `CryptoNotSupported`](#3411326-cryptonotsupported)
          - [3.4.1.1.3.2.7. `FiatNotSupported`](#3411327-fiatnotsupported)
          - [3.4.1.1.3.2.8. `InvalidParameters`](#3411328-invalidparameters)
      - [3.4.1.2. `POST /quote/out`](#3412-post-quoteout)
        - [3.4.1.2.1. Parameters](#34121-parameters)
          - [3.4.1.2.1.1. Request body](#341211-request-body)
        - [3.4.1.2.2. Responses](#34122-responses)
          - [3.4.1.2.2.1. HTTP `200`](#341221-http-200)
          - [3.4.1.2.2.2. HTTP `400`](#341222-http-400)
        - [3.4.1.2.3. Semantics](#34123-semantics)
          - [3.4.1.2.3.1. Success](#341231-success)
          - [3.4.1.2.3.2. Failure](#341232-failure)
          - [3.4.1.2.3.2.1. `GeoNotSupported`](#3412321-geonotsupported)
          - [3.4.1.2.3.2.2. `CryptoAmountTooLow`](#3412322-cryptoamounttoolow)
          - [3.4.1.2.3.2.3. `CryptoAmountTooHigh`](#3412323-cryptoamounttoohigh)
          - [3.4.1.2.3.2.4. `FiatAmountTooLow`](#3412324-fiatamounttoolow)
          - [3.4.1.2.3.2.5. `FiatAmountTooHigh`](#3412325-fiatamounttoohigh)
          - [3.4.1.2.3.2.6. `CryptoNotSupported`](#3412326-cryptonotsupported)
          - [3.4.1.2.3.2.7. `FiatNotSupported`](#3412327-fiatnotsupported)
          - [3.4.1.2.3.2.8. `InvalidParameters`](#3412328-invalidparameters)
    - [3.4.2. KYC Endpoints](#342-kyc-endpoints)
      - [3.4.2.1. `POST /kyc/:kycSchema`](#3421-post-kyckycschema)
        - [3.4.2.1.1. Parameters](#34211-parameters)
          - [3.4.2.1.1.1. Path Parameters](#342111-path-parameters)
          - [3.4.2.1.1.2. Request Body](#342112-request-body)
        - [3.4.2.1.2. Responses](#34212-responses)
          - [3.4.2.1.2.1. HTTP `200`](#342121-http-200)
          - [3.4.2.1.2.2. HTTP `400`](#342122-http-400)
          - [3.4.2.1.2.3. HTTP `409`](#342123-http-409)
        - [3.4.2.1.3. Semantics](#34213-semantics)
          - [3.4.2.1.3.1. Success](#342131-success)
          - [3.4.2.1.3.2. Failure](#342132-failure)
          - [3.4.2.1.3.2.1. `UnsupportedSchema`](#3421321-unsupportedschema)
          - [3.4.2.1.3.2.2. `InvalidSchema`](#3421322-invalidschema)
          - [3.4.2.1.3.2.3. `ResourceExists`](#3421323-resourceexists)
      - [3.4.2.2. `GET /kyc/:kycSchema/status`](#3422-get-kyckycschemastatus)
        - [3.4.2.2.1. Parameters](#34221-parameters)
          - [3.4.2.2.1.1. Path Parameters](#342211-path-parameters)
        - [3.4.2.2.2. Responses](#34222-responses)
          - [3.4.2.2.2.1. HTTP `200`](#342221-http-200)
          - [3.4.2.2.2.2. HTTP `404`](#342222-http-404)
        - [3.4.2.2.3. Semantics](#34223-semantics)
          - [3.4.2.2.3.1. Success](#342231-success)
          - [3.4.2.2.3.2. Failure](#342232-failure)
          - [3.4.2.2.3.2.1. `ResourceNotFound`](#3422321-resourcenotfound)
      - [3.4.2.3. `DELETE /kyc/:kycSchema`](#3423-delete-kyckycschema)
        - [3.4.2.3.1. Parameters](#34231-parameters)
          - [3.4.2.3.1.1. Path Parameters](#342311-path-parameters)
        - [3.4.2.3.2. Responses](#34232-responses)
          - [3.4.2.3.2.1. HTTP `200`](#342321-http-200)
          - [3.4.2.3.2.2. HTTP `404`](#342322-http-404)
        - [3.4.2.3.3. Semantics](#34233-semantics)
          - [3.4.2.3.3.1. Success](#342331-success)
          - [3.4.2.3.3.2. Failure](#342332-failure)
          - [3.4.2.3.3.2.1. `ResourceNotFound`](#3423321-resourcenotfound)
    - [3.4.3. Fiat Account Endpoints](#343-fiat-account-endpoints)
      - [3.4.3.1. `POST /accounts`](#3431-post-accounts)
        - [3.4.3.1.1. Parameters](#34311-parameters)
          - [3.4.3.1.1.1. Request Body](#343111-request-body)
        - [3.4.3.1.2. Responses](#34312-responses)
          - [3.4.3.1.2.1. HTTP `200`](#343121-http-200)
          - [3.4.3.1.2.2. HTTP `400`](#343122-http-400)
          - [3.4.3.1.2.3. HTTP `409`](#343123-http-409)
        - [3.4.3.1.3. Semantics](#34313-semantics)
          - [3.4.3.1.3.1. Success](#343131-success)
          - [3.4.3.1.3.1. Failure](#343131-failure)
          - [3.4.3.1.3.1.1. `UnsupportedSchema`](#3431311-unsupportedschema)
          - [3.4.3.1.3.1.2. `InvalidSchema`](#3431312-invalidschema)
          - [3.4.3.1.3.1.3. `ResourceExists`](#3431313-resourceexists)
      - [3.4.3.2. `GET /accounts`](#3432-get-accounts)
        - [3.4.3.2.1. Parameters](#34321-parameters)
        - [3.4.3.2.2. Responses](#34322-responses)
          - [3.4.3.2.2.1. HTTP `200`](#343221-http-200)
        - [3.4.3.2.3. Semantics](#34323-semantics)
          - [3.4.3.2.3.1. Success](#343231-success)
      - [3.4.3.3. `DELETE /accounts/:fiatAccountId`](#3433-delete-accountsfiataccountid)
        - [3.4.3.3.1. Parameters](#34331-parameters)
          - [3.4.3.3.1.1. Path Parameters](#343311-path-parameters)
        - [3.4.3.3.2. Responses](#34332-responses)
          - [3.4.3.3.2.1. HTTP `200`](#343321-http-200)
          - [3.4.3.3.2.2 HTTP. `404`](#343322-http-404)
        - [3.4.3.3.3. Semantics](#34333-semantics)
          - [3.4.3.3.3.1. Success](#343331-success)
          - [3.4.3.3.3.1. Failure](#343331-failure)
          - [3.4.3.3.3.1.1. `ResourceNotFound`](#3433311-resourcenotfound)
    - [3.4.4. Transfer Endpoints](#344-transfer-endpoints)
      - [3.4.4.1. `POST /transfer/in`](#3441-post-transferin)
        - [3.4.4.1.1. Parameters](#34411-parameters)
          - [3.4.4.1.1.1. Headers](#344111-headers)
          - [3.4.4.1.1.2. Request Body](#344112-request-body)
        - [3.4.4.1.2. Responses](#34412-responses)
          - [3.4.4.1.2.1. HTTP `200`](#344121-http-200)
          - [3.4.4.1.2.2. HTTP `400`](#344122-http-400)
          - [3.4.4.1.2.3. HTTP `404`](#344123-http-404)
          - [3.4.4.1.2.4. Idempotency Key Errors](#344124-idempotency-key-errors)
        - [3.4.4.1.3. Semantics](#34413-semantics)
          - [3.4.4.1.3.1. Success](#344131-success)
          - [3.4.4.1.3.2. Failure](#344132-failure)
          - [3.4.4.1.3.2.1. `KycExpired`](#3441321-kycexpired)
          - [3.4.4.1.3.2.2. `TransferNotAllowed`](#3441322-transfernotallowed)
          - [3.4.4.1.3.2.3. `InvalidQuote`](#3441323-invalidquote)
          - [3.4.4.1.3.2.4. `ResourceNotFound`](#3441324-resourcenotfound)
          - [3.4.4.1.3.2.5. `InvalidFiatAccount`](#3441325-invalidfiataccount)
      - [3.4.4.2. `POST /transfer/out`](#3442-post-transferout)
        - [3.4.4.2.1. Parameters](#34421-parameters)
          - [3.4.4.2.1.1. Headers](#344211-headers)
          - [3.4.4.2.1.2. Request Body](#344212-request-body)
        - [3.4.4.2.2. Responses](#34422-responses)
          - [3.4.4.2.2.1. HTTP `200`](#344221-http-200)
          - [3.4.4.2.2.2. HTTP `400`](#344222-http-400)
          - [3.4.4.2.2.3. HTTP `404`](#344223-http-404)
          - [3.4.4.2.2.4. Idempotency Key Errors](#344224-idempotency-key-errors)
        - [3.4.4.2.3. Semantics](#34423-semantics)
          - [3.4.4.2.3.1. Success](#344231-success)
          - [3.4.4.2.3.2. Failure](#344232-failure)
          - [3.4.4.2.3.2.1. `KycExpired`](#3442321-kycexpired)
          - [3.4.4.2.3.2.2. `TransferNotAllowed`](#3442322-transfernotallowed)
          - [3.4.4.2.3.2.3. `InvalidQuote`](#3442323-invalidquote)
          - [3.4.4.2.3.2.4. `ResourceNotFound`](#3442324-resourcenotfound)
          - [3.4.4.2.3.2.5. `InvalidFiatAccount`](#3442325-invalidfiataccount)
      - [3.4.4.3. `GET /transfer/:transferId/status`](#3443-get-transfertransferidstatus)
        - [3.4.4.3.1. Parameters](#34431-parameters)
          - [3.4.4.3.1.1. Path Parameters](#344311-path-parameters)
        - [3.4.4.3.2. Responses](#34432-responses)
          - [3.4.4.3.2.1. HTTP `200`](#344321-http-200)
          - [3.4.4.3.2.2. HTTP `404`](#344322-http-404)
        - [3.4.4.3.3. Semantics](#34433-semantics)
          - [3.4.4.3.3.1. Success](#344331-success)
          - [3.4.4.3.3.2. Failure](#344332-failure)
          - [3.4.4.3.3.2.1. `ResourceNotFound`](#3443321-resourcenotfound)
- [4. Transfer State Machines](#4-transfer-state-machines)
  - [4.1. Transfers In](#41-transfers-in)
  - [4.2. Transfers Out](#42-transfers-out)
- [5. Webhooks](#5-webhooks)
  - [5.1. Webhook Requests](#51-webhook-requests)
    - [5.1.1. `WebhookKycStatusEventSchema`](#511-webhookkycstatuseventschema)
    - [5.1.2. `WebhookTransferInStatusEventSchema`](#512-webhooktransferinstatuseventschema)
    - [5.1.3. `WebhookTransferOutStatusEventSchema`](#513-webhooktransferoutstatuseventschema)
  - [5.2. Webhook Request Signing](#52-webhook-request-signing)
- [6. Amounts](#6-amounts)
  - [6.1. Units](#61-units)
  - [6.2. Decimal Places](#62-decimal-places)
  - [6.3. Precision](#63-precision)
- [7. AML Considerations](#7-aml-considerations)
- [8. Sandbox Environment](#8-sandbox-environment)
  - [8.1. Celo Network](#81-celo-network)
  - [8.2. Authentication](#82-authentication)
  - [8.3. KYC](#83-kyc)
  - [8.4. Fiat Accounts](#84-fiat-accounts)
- [8.5. Transfers](#85-transfers)
- [9. Definitions](#9-definitions)
  - [9.1. Static Definitions](#91-static-definitions)
    - [9.1.1. `KycStatusEnum`](#911-kycstatusenum)
    - [9.1.2. `ErrorEnum`](#912-errorenum)
    - [9.1.3. `TransferTypeEnum`](#913-transfertypeenum)
    - [9.1.3. `WebhookEventTypeEnum`](#913-webhookeventtypeenum)
    - [9.1.4. `TransferStatusEnum`](#914-transferstatusenum)
  - [9.2. Dynamic Definitions](#92-dynamic-definitions)
    - [9.2.1. `FiatTypeEnum`](#921-fiattypeenum)
    - [9.2.2. `CryptoTypeEnum`](#922-cryptotypeenum)
    - [9.2.3. `KycSchemaEnum`](#923-kycschemaenum)
    - [9.2.4. `FiatAccountTypeEnum`](#924-fiataccounttypeenum)
    - [9.2.5. `FeeTypeEnum`](#925-feetypeenum)
    - [9.2.6. `FeeFrequencyEnum`](#926-feefrequencyenum)
    - [9.2.7. `FiatAccountSchemaEnum`](#927-fiataccountschemaenum)
  - [9.3. Initial Entity Support](#93-initial-entity-support)
    - [9.3.1. KYC Schemas](#931-kyc-schemas)
      - [9.3.1.1. `PersonalDataAndDocuments`](#9311-personaldataanddocuments)
      - [9.3.1.2. `PersonalDataAndDocumentsDetailed`](#9312-personaldataanddocumentsdetailed)
        - [9.3.1.2.1. `IdentificationDocumentType`](#93121-identificationdocumenttype)
    - [9.3.2. Fiat Account Schemas](#932-fiat-account-schemas)
      - [9.3.2.1. `AccountNumber`](#9321-accountnumber)
      - [9.3.2.2. `MobileMoney`](#9322-mobilemoney)
      - [9.3.2.3. `DuniaWallet`](#9323-duniawallet)
      - [9.3.2.4. `IBANNumber`](#9324-ibannumber)
      - [9.3.2.5. `IFSCAccount`](#9325-ifscaccount)
- [10. References](#10-references)
  - [10.1. Normative References](#101-normative-references)
    - [10.1.1. [RFC2119]](#1011-rfc2119)
    - [10.1.2. [RFC8174]](#1012-rfc8174)
    - [10.1.3. [RFC7519]](#1013-rfc7519)
    - [10.1.4. [EIP-4361]](#1014-eip-4361)
    - [10.1.5. [EIP-55]](#1015-eip-55)
    - [10.1.6. [EIP-191]](#1016-eip-191)
    - [10.1.7. [EIP-1271]](#1017-eip-1271)
    - [10.1.8. [EIP-2098]](#1018-eip-2098)
  - [10.2. Informative References](#102-informative-references)
    - [10.2.1. Webhook Best Practices](#1021-webhook-best-practices)
    - [10.2.2. Idempotency Keys](#1022-idempotency-keys)
    - [10.2.3. ISO 3166-1](#1023-iso-3166-1)
    - [10.2.4. ISO 3166-2](#1024-iso-3166-2)
    - [10.2.5. ISO 8601](#1025-iso-8601)

# 1. Introduction

Liquidity between fiat and cryptocurrency assets is a common point of friction for new and growing crypto networks. In particular, cashing in/out
to/from crypto assets can be a difficult experience. Oftentimes, there is poor support for geographical regions, and fees and spreads may be excessively high.
Additionally, liquidity providers (referred to here as *cash-in/cash-out providers*, or *CICO providers*) share no common interface for allowing clients
to transfer funds between crypto and fiat. In many cases, CICO providers only provide transfers through their own native (typically web-based) interface.
In the few cases where CICO providers do offer an API for clients to integrate against, there is no uniformity in interface design across providers. Because each
provider's API is unique, native CICO integrations within clients require completely bespoke integrations. Furthermore, since individual CICO providers may only
offer limited geographical support for transfers, a client may have to integrate with many different providers in order to offer acceptably broad coverage to its
users, which requires considerable implementation effort. In many cases, this level of implementation effort is not feasible, which can lead to clients which
offer poor regional CICO availability. This may lead to users seeking out a variety of different clients in order to access broad regional support. Not only is
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
   spend engineering effort creating an integration. With FiatConnect, this dependency is removed. Once a provider implements a FiatConnect API,
   they can immediately start providing crypto/fiat liquidity to *any*
   client, at no cost to the client developer. Client developers are thus much more likely to integrate with FiatConnect-compliant providers, making
   FiatConnect a compelling option for providers in order to drive adoption.
3. **End User**: Since FiatConnect will reduce the total amount of engineering work required to integrate providers in clients, end users will
   have access to a much broader selection of providers than they previously did. Furthermore, with the time saved by wallet/dapp developers by integrating
   with FiatConnect-compatible providers, they will have more bandwidth to innovate on their core products, further increasing value for the end user.

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
3. `fiatAmount`/`cryptoAmount`: The amount of either fiat or cryptocurrency selected for the quote.
4. `region`: The user's geographical region.

We will explore the precise syntax and semantics of each of these parameters later. If a provider is unable to service a transfer
given specific quote parameters for whatever reason, the provider should return an error code that details why the quote failed. In
this way, a client may use the quote endpoints as an initial "gate" to determine whether or not a provider is supported for a particular
transfer intent.

## 2.2. KYC

For regulatory and compliance reasons, providers may require that users complete a KYC verification process before initiating a transfer.
We expect that different providers will have varying KYC requirements depending on a client's geographical region. The FiatConnect
specification must not make any assumptions about the particular *shape* of KYC data that providers require. Rather, the specification
should be extensible so as to allow providers to communicate to the client what sort of KYC data is required. The FiatConnect specification
allows for a list of standardized *KYC schemas* from which a provider can select those that it accepts. If a provider's KYC needs are
unmet by the specification's recognized schemas, the provider may request that the specification be extended to support a new schema, though
a close eye should be kept on the reusability of this schema by other potential providers.

KYC validation is not expected to occur before *every* transfer. Rather, we expect that KYC validation with a provider will be an infrequent
occurrence. A user may have to submit KYC details for a particular region before initiating a transfer there, but subsequent transfers in that
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

1. Via typical endpoints that can be polled by the client
2. Via webhooks that can be configured by the client

We will discuss these two options in more depth later in this document.

## 2.3. Fiat Accounts

A user needs to be able to store details about a source or destination for fiat funds with the provider. The FiatConnect specification refers
to these as *Fiat Accounts*, but these can commonly be thought of as, e.g., *bank accounts*, *credit cards*, etc. A fiat account refers to
any user-owned account that supports withdrawals and deposits of fiat-denominated funds. Similar to KYC details, different providers may support
different *types* of fiat accounts, and require different schemas depending on the user's geographical region. The FiatConnect specification
introduces two distinct concepts to account for this; that of a *Fiat Account Type*, and a *Fiat Account Schema*. A *Fiat Account Type* refers to
the high-level *type* of fiat account, e.g., bank account, credit card, debit card, etc. A *Fiat Account Schema* refers to the precise shape that
account data should take when being communicated from client to server; each *schema* has an associated *type*. This is a subtle, yet important
distinction. As mentioned in section 2.1, providers may use the *type* of a fiat account for determining whether a transfer is allowed. Different
providers, however, may have different requirements on how account data is communicated, even for the same account *type*, depending on geographical
region. Like with KYC schemas, the FiatConnect standard does not make assumptions about fiat account types and schemas; these are designed to be
extensible by the community.

A CICO provider must provide functionality for a client to store fiat account details with them; these details must be sufficient in order for the
provider to initiate a deposit/withdrawal on the user's behalf. Providers must also allow clients to retrieve and delete stored accounts.
Providers may not allow users to submit fiat account details before undergoing KYC verification for the current region, if KYC verification is required.
Since providers may have restrictions on what fiat account types are allowed for transfers in a particular region, providers must also provide
a means for clients to know what these account types are.

## 2.4. Creating the Transfer

Once a user has completed KYC and has an appropriate fiat account type on file with the provider, the client may initiate a transfer. When creating
a transfer, the client must be able to reference a fiat account on store with the provider to use as a source or destination for fiat funds. It is
extremely important that transfer requests are idempotent; if a transfer request/server response is lost in transit, the client should be able to
safely re-try the request without risk of initiating a duplicate transfer. As such, the FiatConnect specification has a strict requirement that
providers support idempotency keys for transfer requests. This is discussed more in-depth later in this document.

### 2.4.1. Transfer Status Monitoring

Transfers, like KYC verifications, are assumed to be fundamentally asynchronous. We do not expect that all transfers complete quickly (as we do for
KYC verifications), which makes it critical for clients to be able to monitor transfer status over time. The server must provide a means for a client
to retrieve the status of a transfer once it has been submitted. Much like monitoring KYC statuses, the FiatConnect standard requires that providers
offer two means of transfer status monitoring:

1. Via typical endpoints that can be polled by the client
2. Via webhooks that can be configured by the client

These will be discussed in more depth later in this document.

# 3. API Specification

This section presents the formal specification of the FiatConnect API, including required semantics.

## 3.1. Geolocation

Throughout the specification, many references are made to "geo", "geographical location", "region", etc. These are all meant to refer
to the actual geographical location of the client. Having access to geolocation information about an end-user is important, since regulatory
constraints may dictate that providers implement different requirements on requests from different geographical regions.

When requesting a quote, a client will explicitly provide its location through request parameters. The server must respond to this
request assuming that the client is being honest about its location. We expect that a server's actual source of truth about an
end-user's location will come from KYC verification.

As an example, a client using a VPN should still be able to interact with FiatConnect APIs; when requesting a quote,
this client would provide their actual country, and *not* the one implied by their request IP. If their self-reported location
is allowed for transfers, and their actual location (as collected through KYC) matches this, their KYC will be accepted and they
will be allowed to initiate a transfer. In the case where a user submits KYC details for a geolocation that is *not* supported by a
provider, they will never be able to initiate a transfer, regardless of what location they submit to the quote endpoint, since the KYC
verification should be denied.

## 3.2. Clock Synchronization

Various parts of the FiatConnect specification rely on the client and server synchronizing their clocks in order to agree on a common time.
In particular, the authentication scheme relies on clock synchronization to choose proper issuance and expiration times for session cookies,
and the quote endpoints return timestamps representing the expiry of the requested quote, which requires client-server clock synchronization
in order to be interpreted correctly.

Rather than having bespoke clock synchronization solutions for each of these cases, the FiatConnect specification requires API implementors to
include a single `/clock` endpoint, which simply returns the server's current timestamp. Servers MUST implement this endpoint. With this information,
the client can determine the offset between their local time and server time, and synchronize on clocks (to an acceptable approximation).
The exact method by which clients determine the clock offset is an implementation detail left to the client. (For example, a relatively
easy and inexpensive method that incorporates round-trip delay is the approach taken by the
[Network Time Protocol](https://en.wikipedia.org/wiki/Network_Time_Protocol#Clock_synchronization_algorithm).)

### 3.2.1. `GET /clock`

This endpoint is responsible for reporting the current server time to the client.

#### 3.2.1.1. Parameters

This endpoint does not require any parameters.

#### 3.2.1.2. Responses

##### 3.2.1.2.1. HTTP `200`

On success, the server MUST return an HTTP `200`, with the following response body:

```
{
	time: `string`
}
```

#### 3.2.1.3. Semantics

The semantics of this endpoint are extremely simple; the server MUST always return a successful response containing the current server time.

##### 3.2.1.3.1. Success

On success, the server MUST return a 200 response. The `time` field in the response MUST be the server's current time, formatted as an ISO 8601 datetime string.

## 3.3. Authentication & Authorization

The FiatConnect API specifies *two* types of authentication; the first authenticates the *user*, and is required for all requests;
the second authenticates the *client*, and is optionally required, depending on whether or not it has been configured by the client
with the provider.

### 3.3.1. Sign-In With Ethereum

FiatConnect-compliant APIs are required to use the *Sign-In With Ethereum* standard, otherwise known as [EIP-4361](https://eips.ethereum.org/EIPS/eip-4361)
in order to identify users and confirm ownership of a Celo blockchain address. At a high level, the Sign-In With Ethereum standard
requires the client to use a user's private key to sign a plaintext message containing a number of standardized fields, and send that signed message to the
server to prove account ownership. FiatConnect APIs MUST use the SIWE standard to create authenticated sessions for the user, and return session cookies to
the client. More detail on exactly how this should be structured is provided below.

#### 3.3.1.1. SIWE Resources

SIWE has rich tooling and support across multiple languages; see [here](https://docs.login.xyz/) for more details, example implementations, and libraries for many popular
languages.

#### 3.3.1.2. SIWE Sessions

At a high level, the client will craft a SIWE-compliant message and sign it with the user's private key. This message will contain various parameters relating
to the origin URL which they're requesting to be authorized against, desired session length, etc. Once the client has crafted and signed the message, the client will send
the signed message to a FiatConnect API at a `POST /auth/login` endpoint. The server will validate the signature, and check that all the included fields in the signed message are valid.
If everything checks out, the server will create a session for the user according to the details in the signed message. Once the server has created the session,
it responds to the client with a `200`, and returns session cookies that the client can use in subsequent requests to privileged endpoints on behalf of the signed-in address.

#### 3.3.1.3. SIWE Message Format

Within the FiatConnect specification, all references to SIWE-compliant messages refer to plaintext messages adhering exactly to the
[SIWE message format](https://docs.login.xyz/general-information/siwe-overview/eip-4361#abnf-message-format), and additionally to the constraints described below.

```
${domain} wants you to sign in with your Ethereum account:
${address}

URI: ${uri}
Version: 1
Chain ID: 42220
Nonce: ${nonce}
Issued At: ${issued-at}
Expiration Time: ${expiration-time}
```

Each field in this template is required to be filled in by the client, and has a unique meaning. As described later, servers MUST reject authorization attempts
if any fields are not as expected. The meaning of each of these fields is enumerated below:

The `domain` field MUST correspond to the [RFC 3986](https://datatracker.ietf.org/doc/html/rfc3986#section-3.2) authority of the API that the client is requesting to be authorized for, e.g. `example.com`.
The `address` field MUST correspond to the Celo blockchain address that the user is attempting to login/authorize themself as. For externally-owned accounts, this
must conform to the mixed-case checksum encoding specified in [EIP-55](https://eips.ethereum.org/EIPS/eip-55).
The `uri` field MUST correspond to the origin URL of the API to authorize for, with the `/auth/login` path appended, e.g. `https://example.com/auth/login`.
The `nonce` field MUST be a unique, at least 8 characters long, alpha-numeric string not previously seen for any unexpired login request from the given client.
The `issued-at` field MUST be the ISO 8601 datetime string of the time at which the client generated the message.
The `expiration-time` field MUST be an ISO 8601 datetime string that specifies when this message will no longer valid, and when a session created with this message
will expire. This field MUST NOT be more than four hours (14400 seconds) later than the `issued-at` field.

Note that in cases where a client is attempting to authorize themselves as the owner of a contract-owned account (rather than an externally-owned account), the `address`
field MUST correspond to the address of the on-chain contract account, rather than the externally-owned account (EOA) address. The message itself should be signed in accordance
with the [EIP-1271](https://eips.ethereum.org/EIPS/eip-1271) standard, in such a way that the contract account can validate the signature and message hash with its
implementation of the `isValidSignature` method as outlined in the EIP-1271 standard. If desired, CICO providers MAY support authenticating as a contract-owned account,
but it is not required.

#### 3.3.1.4. Session Cookies

Once the client generates a SIWE-compliant plaintext message, it signs it with the user's externally-owned account private key according to [EIP-191](https://eips.ethereum.org/EIPS/eip-191)
`personal_sign` format. The client will then send the message and signature to the server at a `POST /auth/login` endpoint, described below. If the server
accepts the signed message as valid, it will respond with a session cookie linked that identifies the user's authenticated session within the server. The server
MUST set the [SameSite](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie/SameSite) policy on the session cookie to prevent the client
from sending the cookie to other hosts. Ultimately, it is the client's responsibility to manage session cookies between different FiatConnect APIs.

If a client sends a session cookie when calling the `POST /auth/login` endpoint (either to refresh their current, valid session, or to generate a new session if their current one is
expired), the server MUST ignore the provided cookie. If the authentication request is valid, the server MUST set a new value on the cookie referencing the client's new, valid session,
and return it to the client.

#### 3.3.1.5. `POST /auth/login`

The `POST /auth/login` endpoint is responsible for verifying signed messages sent by clients, creating and authenticating a user's session, and returning session cookies.
Note that the Sign-In With Ethereum standard, and FiatConnect, support authorization for externally owned accounts (EOAs) and contract-owned accounts; The `POST /auth/login`
endpoint MUST honor login requests for EOAs, and MAY support smart contract-owned accounts as well, as described below.

##### 3.3.1.5.1. Parameters

###### 3.3.1.5.1.1. Request Body

The request body must contain the following fields:

* `message`: {`string`} [REQUIRED]
  - The plaintext SIWE message
* `signature`: {`string`} [REQUIRED]
  - The [EIP-191](https://eips.ethereum.org/EIPS/eip-191) signed message if logging in as EOA, or a signature according to the
	[EIP-1271](https://eips.ethereum.org/EIPS/eip-1271) standard, if logging in as a contract-owned account.

The `message` field should be the *plaintext* message, while the signature should be an string containing the typical secp256k1 signature fields: `r`, `s`, and `v`.
This `signature` string should be a hex string, prefixed with `0x`. The hex data should be 65 bytes long (130 hex characters, *excluding* the leading `0x`). The first
32 bytes (64 hex characters) represent `r`, the next 32 bytes (64 hex characters) represent `s`, and the final byte (2 hex characters) represent `v`. This is the "canonical"
form of the signature as referenced in [EIP-2098](https://eips.ethereum.org/EIPS/eip-2098), rather than the "compact" form.

##### 3.3.1.5.2. Responses

###### 3.3.1.5.2.1. HTTP `200`

On success, the server MUST return an HTTP `200`, update the state of the user's session to be authenticated, and return a session cookie.

###### 3.3.1.5.2.2. HTTP `401`

On failure, the server MUST return an HTTP `401`, with the following response body.

```
{
	error: `ErrorEnum`,
}
```

##### 3.3.1.5.3. Semantics

This endpoint performs validation on a signed SIWE-compliant message. In addition to checking that the signature itself is valid and corresponds to the included
address, it also performs various safety and security checks on the contents of the message. If all checks pass, the server updates the client's session to be authenticated,
and returns a `200`.

###### 3.3.1.5.3.1. Success

On success, the server MUST respond with a `200` status code. The server MUST only respond successfully after performing the following checks:

* A server MUST verify that the plaintext `message` adheres to the SIWE grammar/syntax (refer to the `ABNF Message Format` section within
  [EIP-4361](https://eips.ethereum.org/EIPS/eip-4361)). If it does not, the server MUST return a 401 (`InvalidParameters`)

* A server MUST determine whether the address being signed in as corresponds to an EOA or contract account. If a server determines that the address corresponds to a
  contract-owned account and does *not* support contract-owned account login, it must return a 401 (`ContractLoginNotSupported`).

  * If the address to sign in as corresponds to an EOA, the server MUST verify that the `signature` field was signed by the address attempting to be signed is and
	is a valid signature for the `message` included in the request.

  * If the address to sign in as corresponds to a contract-owned account and the server supports contract-owned account login, the server MUST verify the validity of
	the included `signature` according to the [EIP-1271](https://eips.ethereum.org/EIPS/eip-1271) standard.

* A server MUST verify that the `nonce` field does not correspond to a nonce that has already been used to authenticate an ongoing, valid session.

* A server MUST verify that the `domain` and `uri` fields correspond exactly to the values expected for the server's hostname/origin.

* A server MUST verify that the `Version` and `Chain ID` lines in the signed message are exactly `1` and `42220`, respectively.

* If the `issued-at` field in the signed message is after the server's current timestamp, or if the `issued-at` field is after the `expiration-time` field, the server
  MUST fail the request. If the server's current timestamp is past the `expiration-time` field, the server MUST fail the request. If the `expiration-time` field is
  greater than four hours (14400 seconds) ahead of the `issued-at` field, the server MUST fail the request.

If all fields are acceptable, and the message is formatted correctly, the server SHOULD authenticate the user's session, and return an HTTP `200`.
The server SHOULD attach the fields from the signed message to the session internally, in order to access data about the user and their session in other endpoints.

Once a server authenticates a session using a particular nonce, the server MUST NOT allow any other sessions to be created using the same nonce and address, until
`expiration-time` of the session created using that nonce has been reached. The implementation of this is up to the server, but will require storing some global
state about nonces in order to check if a client is attempting to sign-in using a nonce that is temporarily forbidden. Careful consideration should be taken to make
sure that nonce management works correctly when scaling server resources.

###### 3.3.1.5.3.2. Failure

On failure, the server MUST return an HTTP `401` error, along with an error code that details exactly why the request failed.

###### 3.3.1.5.3.2.1. `InvalidSignature`

If the signature is invalid or doesn't match the included `address` field, the server MUST return an `InvalidSignature` error. Note that the address used
to sign the message may not actually match the address being authenticated in the case where the client is authenticating as a contract-owned account. In
such cases, `InvalidSignature` MUST NOT be returned when the signature is valid, and the address used to sign the message is considered valid by the contract
referenced in the `address` field.

###### 3.3.1.5.3.2.2. `InvalidParameters`

If the request body is missing, malformed, or fails to pass any of the required checks, the server MUST respond with an `InvalidParameters` error. If another
error type is more descriptive of the actual issue, that error should be used instead.

###### 3.3.1.5.3.2.3. `ContractLoginNotSupported`

If the server determines that a client is trying to log in as a contract-owned account but the server does not support contract-owned account logins, the
server MUST respond with a `ContractLoginNotSupported` error.

###### 3.3.1.5.3.2.4. `NonceInUse`

If the nonce included in the message is already attached to a valid session, the server MUST respond with a `NonceInUse` error.

###### 3.3.1.5.3.2.5. `IssuedTooEarly`

If the `issued-at` field is after the server's current timestamp, the server MUST respond with an `IssuedTooEarly` error.

###### 3.3.1.5.3.2.6. `ExpirationTooLong`

If the `expiration-time` field is more than four hours into the future, the server MUST respond with an `ExpirationTooLong` error.

#### 3.3.1.6. Using Sessions

Once a client has established a session with a server, they can use that session (by sending the session cookie to the server) to access privileged endpoints
throughout the API. Certain endpoints impose different requirements on the in-use session. There are *two levels* of endpoints, with respect to their authentication
requirements. These levels are explained below.

##### 3.3.1.6.1. Non-Privileged Endpoints

The first group of endpoints are those that *do not* require the user to have a logged-in session. These are:

* `POST /quote/in`
* `POST /quote/out`
* `POST /auth/login`
* `GET /clock`

Since it may be a painful user experience to have a user log in to a provider just to get a quote, authentication is not required for these endpoints. Of course,
clients may still access these endpoints when logged in, but a logged-in session (or any session at all) is not required to access these endpoints. `POST /auth/login`
does not require a user to already be logged in, as described above.

##### 3.3.1.6.2. Privileged Endpoints

The second, and largest set of endpoints, are the ones that require a user to be logged in. These endpoints are the ones that deal with reading and writing user-specific data.

* `POST /kyc/:kycSchema`
* `GET /kyc/:kycSchema/status`
* `DELETE /kyc/:kycSchema`
* `POST /accounts`
* `GET /accounts`
* `DELETE /accounts/:fiatAccountId`
* `GET /transfer/:transferId/status`
* `POST /transfer/in`
* `POST /transfer/out`

For these endpoints, a user needs to be logged in with an authenticated session. This session MUST have an expiration time of no greater than four hours (the maximum allowed by FiatConnect).

If a client attempts to access any of these endpoints without a session, the server MUST return a `401` status code with an `Unauthorized` error.
If a client tries to access any of these endpoints with an expired session, the server MUST respond with an HTTP `401` status code with a `SessionExpired` error.

### 3.3.2. Client API Key

In addition to the SIWE-based auth used to authenticate users and create sessions, the FiatConnect specification also supports a more traditional authentication standard, used
to identify specific clients with requests. Recall the webhook-based status monitoring mentioned earlier in this document. In order to support status
monitoring via webhooks, individual clients will need to be able to register a URL pointing to an API able to handle webhook updates from the server.
Once a client has registered a webhook URL with the provider, the client needs a way to identify itself to the server. To uniquely identify clients in order
to know where to send webhook-based status updates, a server may allow clients to register an API key. The exact mechanism by which servers allocate API keys
to clients and allow them to register webhook URLs is out of scope of this document.

Once a client has registered an API key with a provider and associated it with a webhook URL, a client MAY provide it to a server through an `Authorization` header,
using the `Bearer` authorization scheme. For example: `Authorization: Bearer <key>`, where `<key>` is the client's API key.

A server MUST support API token authentication, and MAY *require* that clients include an API key in each request. If a server requires
that clients include an API key in each request, it MUST respond to the client with an HTTP `400` error if the API key is missing from the request.
Regardless of whether or not a server requires an API key on every request, it MUST return an HTTP `401` error if an API key is
provided but does not correspond to any registered client; likewise it MUST return an HTTP `400` error if the API key is poorly formed.

API token authentication is *completely separate* from SIWE-based auth. The privileged endpoints listed earlier *always* require a user to have a valid session via SIWE
authentication. API token authentication MAY be required on all endpoints, or optional. Regardless of whether or not the client provides an API token, if the client attempts
to access a privileged endpoint without a valid session, the server MUST reject their request.

### 3.3.3. HTTPS

All servers implementing a FiatConnect-compliant API MUST use HTTPS, and MUST NOT support unsecured HTTP requests.

## 3.4. Formal Specification

This section details the precise syntax and semantics of all the endpoints required by the FiatConnect specification, besides those devoted to authentication.
Endpoints are logically grouped, and roughly presented in order of dependency. The logical groupings are as follows:

* Quote Endpoints
* KYC Endpoints
* Fiat Account Endpoints
* Transfer Endpoints

This section references a number of definitions/enums; for an exhaustive list of these, please see later in the document.

### 3.4.1. Quote Endpoints

We assume that the lifecycle of a transfer begins with an end-user requesting a quote. We predict that the typical transfer experience will involve
a user providing their desired transfer parameters (crypto type, fiat type, amount), and the client then proceeding to show
them a list of supported providers.

We assume that different quotes represent transfers that may have differing requirements regarding KYC and fiat accounts. To support this, the quote
endpoints must return information about KYC and fiat account schemas that are required in order to actually perform a transaction for the requested quote.

Quotes may vary depending on geo, and certain specific transfers may be unavailable in certain regions entirely. As such, we assume that the
client will interpret a 200 response from this endpoint as verification that the provider is supported for the particular transfer parameters,
and a non-200 as verification that it is not.

Some non-200s may be recovered from by modifying the transfer parameters; others may not be, e.g., for those caused by geos where transfers are completely unsupported.

#### 3.4.1.1. `POST /quote/in`

The `POST /quote/in` endpoint is used to retrieve quotes used for transfers in to crypto from fiat currencies. In addition to returning quote information, it also
returns the permissable types of KYC that a user must have on file to initiate the corresponding transfer, as well as the fiat account types that are allowed to be
used for the transfer.

##### 3.4.1.1.1. Parameters

###### 3.4.1.1.1.1. Request Body

* `fiatType`: {`FiatTypeEnum`} [REQUIRED]
  - The desired fiat type to use for a transfer in quote; selected from a predefined list of fiat types supported by FiatConnect.
* `cryptoType`: {`CryptoTypeEnum`} [REQUIRED]
  - The desired crypto type to use for a transfer in quote; selected from a predefined list of crypto types supported by FiatConnect.
* `fiatAmount`: {`string`}
  - String-ified numerical amount (E.g. `"5.00"`) of the selected fiat type to use for this transfer in quote; if provided, the returned quote will be denominated in the type of crypto specified for the quote.
* `cryptoAmount`: {`string`}
  - String-ified numerical amount (E.g. `"1.0"`) of the selected crypto type to use for this transfer in quote; if provided, the returned quote will be denominated in the type of fiat specified for the quote.
* `country`: {`string`} [REQUIRED]
  - An ISO 3166-1 alpha-2 country code representing the country where the quote should be requested for.
* `region`: {`string`}
  - An optional ISO 3166-2 subdivision code representing a region within the provided country.
* `address`: {`string`} [REQUIRED]
  - An [EIP-55](https://eips.ethereum.org/EIPS/eip-55) formatted address, representing the Celo address of the user to get the quote for. For contract-owned accounts, this should be
	the address of the contract itself.
* `preview`: {`boolean`}
  - An optional boolean flag, that if included, will cause the server not to generate a `quoteId` for the request.

##### 3.4.1.1.2. Responses

###### 3.4.1.1.2.1. HTTP `200`

On success, the server MUST return an HTTP `200`, with the following response body:

```
{
	quote: {
		fiatType: `FiatTypeEnum`,
		cryptoType: `CryptoTypeEnum`,
		fiatAmount: `string`,
		cryptoAmount: `string`,
		fee?: `string`,
		feeType?: `FeeTypeEnum`,
		feeFrequency?: `FeeFrequencyEnum`,
		quoteId?: `string`,
		guaranteedUntil: `string`
		transferType: `TransferTypeEnum.TransferIn`
	},
	kyc: {
		kycRequired: `boolean`,
		kycSchemas: {
			kycSchema: `KycSchemaEnum`,
			allowedValues: {
				[string]: `string[]`
			}
		}[]
	},
	fiatAccount: {
		[FiatAccountTypeEnum]: {
			fiatAccountSchemas: {
				fiatAccountSchema: `FiatAccountSchemaEnum`,
				allowedValues: {
					[string]: `string[]`
				}
			}[],
			settlementTimeLowerBound?: `string`,
			settlementTimeUpperBound?: `string`
		}
	}
}
```

##### 3.4.1.1.2.2. HTTP `400`

On failure, the server MUST return an HTTP `400`, with a response body as follows. Refer to the `ErrorEnum` definition for all possible error values.

```
{
	error: `ErrorEnum`,
	minimumFiatAmount?: `string`,
	maximumFiatAmount?: `string`,
	minimumCryptoAmount?: `string`,
	maximumCryptoAmount?: `string`
}
```

##### 3.4.1.1.3. Semantics

All transfer in quotes require the `fiatType`, `cryptoType`, and exactly *one of* `fiatAmount` or `cryptoAmount`. `country` is required, and `region` is optional.
`preview` is also an optional parameter. If these requirements are not met, the server MUST return an HTTP `400` error. If the server responds with an HTTP `200`, the provider MUST support
a transfer in for the requested details. If the requested quote is not supported, the server MUST return an HTTP `400` error.

###### 3.4.1.1.3.1. Success

A successful response indicates that the provider is able to perform a transfer for the requested quote in the specified country/region.
If `fiatAmount` is provided, the `quote.cryptoAmount` field returned in the success body MUST correspond to the amount of crypto the user should expect to
receive by providing `fiatAmount` worth of the fiat currency. If `cryptoAmount` is provided, the `quote.fiatAmount` field MUST correspond to the amount
of fiat currency required in order to receive the requested amount of crypto.

The `quote.fiatType`, `quote.cryptoType`, `quote.fiatAmount`, and `quote.cryptoAmount` fields in the response body MUST correspond to the request body provided to the endpoint.
The `quote.guaranteedUntil` field represents the time that the quote is guaranteed until, as an ISO 8601 datetime string. The `quote.quoteId` field
is a globally unique identifier for the quote, and is used by the client to initiate a transfer with the parameters associated with the quote including
conversion rate, fee, amount, crypto type, fiat type, etc. A server MUST provide `quote.guaranteedUntil` and `quote.quoteId`, and MUST honor the
provided conversion rate and fee if the client initiates a transfer with the `quoteId` before the time `quote.guaranteedUntil`.

The `quote.fee` field is an optional return value, used to represent an optional fixed fee as a string-ified numerical
amount (e.g. `"1.0"`) for the transfer. Note that `fee` is meant for end-user informational purposes only, and `fee`
MUST be included in the exchange rate (ratio of `cryptoAmount` to `fiatAmount`) already in the provided quote. For
example, if `fiatAmount` is 15 in the quote response for some transfer in of USD for cUSD, and `cryptoAmount` is 12,
and `fee` is 3, the end user MUST receive 12 cUSD for 15 USD (not 9, which would double-count the fee).

For transfers in, `fee` is assumed to be denominated in the selected `fiatType`. If `fee` is provided, the server MAY
return `quote.feeType` and/or `quote.feeFrequency`. `feeType` represents the *type* of fee; e.g, if it's for KYC or a
fixed platform fee. `feeFrequency` represents the frequency at which the fee is required; e.g., one-time, or on each
transfer.

The `quote.settlementTimeLowerBound` and `quote.settlementTimeUpperBound` fields are optional return values, representing the lower and upper bounds for transaction settlement time using
a particular `FiatAccountType` respectively. A server MAY include these in the response. If included, these MUST be strings representing time deltas in number of seconds. If included, a server
SHOULD try to honor the advertised settlement time bounds when performing a related transfer, but it is not required.

In addition to returning quote data, a successful response must also return information about which KYC schemas are acceptable/required in order to initiate a transfer
with the given quote parameters. If KYC is required, the server MUST set `kyc.kycRequired` to `true` in the response body. Likewise, if no KYC is required, this value MUST be
`false`. If `kyc.kycRequired` is `true`, the `kyc.kycSchemas` field MUST be present. `kyc.kycSchemas` is a list of objects. Each object corresponds to a single acceptable KYC schema.
The `kycSchema` field within this object represents the type of KYC schema that can be used. The `allowedValues` object is an optional mapping from any number of keys in the selected
KYC schema to values that are allowed for that key. For example, if a server wants to limit the selection for certain fields on the schema, `allowedValues` can contain lists of selectable
values for those fields. On the client-side, this could be used to render a list of options, for example.

Finally, a successful response must also return information about what fiat account types are allowed to be used for the transfer, and what schemas are allowed to communicate
those account details.

If the quote request contains a value of `true` for the `preview` field, the returned quote SHOULD represent a preview; the provider has no obligation to persist
any information relating to the generated quote on the server. If a quote preview is requested, the provider MUST NOT populate the `quoteId` field in the response
body. This prevents clients from actually attempting to use this quote for a transfer. Besides the exclusion of the `quoteId` field, *all other aspects* of a preview
quote response MUST be identical to those of the response had the client requested a non-preview quote.

On success, the server MUST return a mapping from fiat account types to lists of schemas that the client may use to add a new account of that
type. This is expected to vary by geographical region as well as quote details provided by the request body.
Each `fiatAccount[FiatAccountTypeEnum]` MUST correspond to a fiat account type that is allowed to be used for the requested quote.
`fiatAccount[FiatAccountTypeEnum].fiatAccountSchemas` is a list of objects, where each object represents a fiat account schema that can be used to communicate data
about the corresponding fiat account type. Each object MUST contain a `fiatAccountSchema` field representing the actual fiat account schema that can be used, and an
optional `allowedValues` field. The `allowedValues` object is an optional mapping from any number of keys in the selected fiat account schema to values that are allowed for that key.
This is identical in purpose and function to the `allowedValues` field for KYC schemas, discussed earlier.

###### 3.4.1.1.3.2. Failure

On failure, this endpoint MUST return an HTTP `400` error, along with an error code that details exactly why the quote failed.

###### 3.4.1.1.3.2.1. `GeoNotSupported`

If a quote is not supported due to the provider not supporting the user's region at all, the server MUST return a `GeoNotSupported` error.

###### 3.4.1.1.3.2.2. `CryptoAmountTooLow`

If the user provides `cryptoAmount` in the request body, and the value is too low, the server MUST return a `CryptoAmountTooLow` error. The
server MAY also provide a `minimumCryptoAmount` value with the response body.

###### 3.4.1.1.3.2.3. `CryptoAmountTooHigh`

If the user provides `cryptoAmount` in the request body, and the value is too high, the server MUST return a `CryptoAmountTooHigh` error. The
server MAY also provide a `maximumCryptoAmount` value with the response body.

###### 3.4.1.1.3.2.4. `FiatAmountTooLow`

If the user provides `fiatAmount` in the request body, and the value is too low, the server MUST return a `FiatAmountTooLow` error. The
server MAY also provide a `minimumFiatAmount` value with the response body.

###### 3.4.1.1.3.2.5. `FiatAmountTooHigh`

If the user provides `fiatAmount` in the request body, and the value is too high, the server MUST return a `FiatAmountTooHigh` error. The
server MAY also provide a `maximumFiatAmount` value with the response body.

###### 3.4.1.1.3.2.6. `CryptoNotSupported`

If the provided `cryptoType` is unsupported in the user's current region, but the server supports other transfers in their region,
the server MUST return a `CryptoNotSupported` error.

###### 3.4.1.1.3.2.7. `FiatNotSupported`

If the provided `fiatType` is unsupported in the user's current region, but the server supports other transfers in their region,
the server MUST return a `FiatNotSupported` error.

###### 3.4.1.1.3.2.8. `InvalidParameters`

If the request is missing any required parameters, or if the parameters are poorly formed, the server MUST respond
with an `InvalidParameters` error.

#### 3.4.1.2. `POST /quote/out`

The `POST /quote/out` endpoint is used to retrieve quotes used for transfers out from crypto to fiat currencies.

##### 3.4.1.2.1. Parameters

###### 3.4.1.2.1.1. Request Body

* `fiatType`: {`FiatTypeEnum`} [REQUIRED]
  - The desired fiat type to use for a transfer out quote; selected from a predefined list of fiat types supported by FiatConnect.
* `cryptoType`: {`CryptoTypeEnum`} [REQUIRED]
  - The desired crypto type to use for a transfer out quote; selected from a predefined list of crypto types supported by FiatConnect.
* `fiatAmount`: {`string`}
  - String-ified numerical amount (e.g. `"5.00"`) of the selected fiat type to use for this transfer out quote; if provided, the returned quote will be denominated in the type of crypto specified for the quote.
* `cryptoAmount`: {`string`}
  - String-ified numerical amount (e.g. `"1.0"`) of the selected crypto type to use for this transfer out quote; if provided, the returned quote will be denominated in the type of fiat specified for the quote.
* `country`: {`string`} [REQUIRED]
  - An ISO 3166-1 alpha-2 country code representing the country where the quote should be requested for.
* `region`: {`string`}
  - An optional ISO 3166-2 subdivision code representing a region within the provided country.
* `address`: {`string`} [REQUIRED]
  - An [EIP-55](https://eips.ethereum.org/EIPS/eip-55) formatted address, representing the Celo address of the user to get the quote for. For contract-owned accounts, this should be
	the address of the contract itself.
* `preview`: {`boolean`}
  - An optional boolean flag, that if included, will cause the server not to generate a `quoteId` for the request.

##### 3.4.1.2.2. Responses

###### 3.4.1.2.2.1. HTTP `200`

On success, the server MUST return an HTTP `200`, with the following response body:

```
{
	quote: {
		fiatType: `FiatTypeEnum`,
		cryptoType: `CryptoTypeEnum`,
		fiatAmount: `string`,
		cryptoAmount: `string`,
		fee?: `string`,
		feeType?: `FeeTypeEnum`,
		feeFrequency?: `FeeFrequencyEnum`,
		quoteId?: `string`,
		guaranteedUntil: `string`,
		transferType: `TransferTypeEnum.TransferOut`
	},
	kyc: {
		kycRequired: `boolean`,
		kycSchemas: {
			kycSchema: `KycSchemaEnum`,
			allowedValues: {
				[string]: `string[]`
			}
		}[]
	},
	fiatAccount: {
		[FiatAccountTypeEnum]: {
			fiatAccountSchemas: {
				fiatAccountSchema: `FiatAccountSchemaEnum`,
				allowedValues: {
					[string]: `string[]`
				}
			}[],
			settlementTimeLowerBound?: `string`,
			settlementTimeUpperBound?: `string`
		}
	}
}
```

###### 3.4.1.2.2.2. HTTP `400`

On failure, the MUST return an HTTP `400`, with a response body as follows. Refer to the `ErrorEnum` definition for all possible error values.

```
{
	error: `ErrorEnum`,
	minimumFiatAmount?: `string`,
	maximumFiatAmount?: `string`,
	minimumCryptoAmount?: `string`,
	maximumCryptoAmount?: `string`
}
```


##### 3.4.1.2.3. Semantics

All transfer out quotes require the `fiatType`, `cryptoType`, and exactly *one of* `fiatAmount` or `cryptoAmount`. `country` is required, and `region` is optional.
`preview` is also an optional parameter. If these requirements are not met, the server MUST return an HTTP `400` error. If the server responds with an HTTP `200`, the provider MUST support
a transfer out for the requested details. If the requested quote is not supported, the server MUST return an HTTP `400` error.

###### 3.4.1.2.3.1. Success

A successful response indicates that the provider is able to perform a transfer for the requested quote for the specified country/region.
If `fiatAmount` is provided, the `quote.cryptoAmount` field returned in the success body MUST correspond to the amount of crypto the user must provide
in order to receive `fiatAmount` worth of the fiat currency. If `cryptoAmount` is provided, the `quote.fiatAmount` field MUST correspond to the amount
of fiat currency the user should expect to receive in exchange for `cryptoAmount` worth of the cryptocurrency.

The `quote.fiatType`, `quote.cryptoType`, `quote.fiatAmount`, and `quote.cryptoAmount` fields in the response body MUST correspond to the request body provided to the endpoint.
The `quote.guaranteedUntil` field represents the time that the quote is guaranteed until, as an ISO 8601 datetime string. The `quote.quoteId` field
is a globally unique identifier for the quote, and is used by the client to initiate a transfer with the parameters associated with the quote including
conversion rate, fee, amount, crypto type, fiat type, etc.  A server MUST provide `quote.guaranteedUntil` and `quote.quoteId`, and MUST honor the
provided conversion rate and fee if the client initiates a transfer with the `quoteId` before the time `quote.guaranteedUntil`.

The `quote.fee` field is an optional return value, used to represent an optional fixed fee as a string-ified numerical
amount (e.g. `"1.0"`) for the transfer. Note that `fee` is meant for end-user informational purposes only, and `fee`
MUST be included in the exchange rate (ratio of `fiatAmount` to `cryptoAmount`) already in the provided quote. For
example, if `cryptoAmount` is 15 in the quote response for some transfer out of cUSD for USD, and `fiatAmount` is 12,
and `fee` is 3, the end user MUST receive 12 USD for 15 cUSD (not 9, which would double-count the fee).

For transfers out, `fee` is assumed to be denominated in the selected `cryptoType`. If `fee` is provided, the server MAY
return `quote.feeType` and/or `quote.feeFrequency`. `feeType` represents the *type* of fee; e.g, if it's for KYC or a
fixed platform fee. `feeFrequency` represents the frequency at which the fee is required; e.g., one-time, or on each
transfer.

The `quote.settlementTimeLowerBound` and `quote.settlementTimeUpperBound` fields are optional return values, representing the lower and upper bounds for transaction settlement time using
a particular `FiatAccountType` respectively. A server MAY include these in the response. If included, these MUST be strings representing time deltas in number of seconds. If included, a server
SHOULD try to honor the advertised settlement time bounds when performing a related transfer, but it is not required.

In addition to returning quote data, a successful response must also return information about which KYC schemas are acceptable/required in order to initiate a transfer
with the given quote parameters. If KYC is required, the server MUST set `kyc.kycRequired` to `true` in the response body. Likewise, if no KYC is required, this value MUST be
`false`. If `kyc.kycRequired` is `true`, the `kyc.kycSchemas` field MUST be present. `kyc.kycSchemas` is a list of objects. Each object corresponds to a single acceptable KYC schema.
The `kycSchema` field within this object represents the type of KYC schema that can be used. The `allowedValues` object is an optional mapping from any number of keys in the selected
KYC schema to values that are allowed for that key. For example, if a server wants to limit the selection for certain fields on the schema, `allowedValues` can contain lists of selectable
values for those fields. On the client-side, this could be used to render a list of options, for example.

If the quote request contains a value of `true` for the `preview` field, the returned quote SHOULD represent a preview; the provider has no obligation to persist
any information relating to the generated quote on the server. If a quote preview is requested, the provider MUST NOT populate the `quoteId` field in the response
body. This prevents clients from actually attempting to use this quote for a transfer. Besides the exclusion of the `quoteId` field, *all other aspects* of a preview
quote response MUST be identical to those of the response had the client requested a non-preview quote.

Finally, a successful response must also return information about what fiat account types are allowed to be used for the transfer, and what schemas are allowed to communicate
those account details.

On success, the server MUST return a mapping from fiat account types to lists of schemas that the client may use to add a new account of that
type. This is expected to vary by geographical region as well as quote details provided by the request body.
Each `fiatAccount[FiatAccountTypeEnum]` MUST correspond to a fiat account type that is allowed to be used for the requested quote.
`fiatAccount[FiatAccountTypeEnum].fiatAccountSchemas` is a list of objects, where each object represents a fiat account schema that can be used to communicate data
about the corresponding fiat account type. Each object MUST contain a `fiatAccountSchema` field representing the actual fiat account schema that can be used, and an
optional `allowedValues` field. The `allowedValues` object is an optional mapping from any number of keys in the selected fiat account schema to values that are allowed for that key.
This is identical in purpose and function to the `allowedValues` field for KYC schemas, discussed earlier.

###### 3.4.1.2.3.2. Failure

On failure, this endpoint MUST return an HTTP `400` error, along with an error code that details exactly why the quote failed.

###### 3.4.1.2.3.2.1. `GeoNotSupported`

If a quote is not supported due to the provider not supporting the user's region at all, the server MUST return a `GeoNotSupported` error.

###### 3.4.1.2.3.2.2. `CryptoAmountTooLow`

If the user provides `cryptoAmount` in the request body, and the value is too low, the server MUST return a `CryptoAmountTooLow` error. The
server MAY also provide a `minimumCryptoAmount` value with the response body.

###### 3.4.1.2.3.2.3. `CryptoAmountTooHigh`

If the user provides `cryptoAmount` in the request body, and the value is too high, the server MUST return a `CryptoAmountTooHigh` error. The
server MAY also provide a `maximumCryptoAmount` value with the response body.

###### 3.4.1.2.3.2.4. `FiatAmountTooLow`

If the user provides `fiatAmount` in the request body, and the value is too low, the server MUST return a `FiatAmountTooLow` error. The
server MAY also provide a `minimumFiatAmount` value with the response body.

###### 3.4.1.2.3.2.5. `FiatAmountTooHigh`

If the user provides `fiatAmount` in the request body, and the value is too high, the server MUST return a `FiatAmountTooHigh` error. The
server MAY also provide a `maximumFiatAmount` value with the response body.

###### 3.4.1.2.3.2.6. `CryptoNotSupported`

If the provided `cryptoType` is unsupported in the user's current region, but the server supports other transfers in their region,
the server MUST return a `CryptoNotSupported` error.

###### 3.4.1.2.3.2.7. `FiatNotSupported`

If the provided `fiatType` is unsupported in the user's current region, but the server supports other transfers in their region,
the server MUST return a `FiatNotSupported` error.

###### 3.4.1.2.3.2.8. `InvalidParameters`

If the request is missing any required parameters, or if the parameters are poorly formed, the server MUST respond
with an `InvalidParameters` error.

### 3.4.2. KYC Endpoints

A CICO provider may wish to require different KYC types for a single user across separate transfers for various reasons. For example, if
a user initiates a transfer from the US, the provider may request a particular type of KYC for the transfer. Later on, if the user initiates
a transfer from a different geo, the same provider may require a different type of KYC. The provider should store these KYC verifications to re-use
later in case the user returns to a geo where they have already provided an appropriate KYC type. Negotiation of what KYC types are allowed for
a particular transfer occurs entirely within the quote endpoints.

While validation is typically expected to be completed quickly and automatically, we assume that it is fundamentally an asynchronous process. As such,
the FiatConnect specification must support monitoring the status of an ongoing validation. While we require that a CICO provider be able to maintain
different types of KYC validations per-user, we require that they only maintain *one* record of each type for each user at a time. Otherwise, server-side
implementation would become significantly more difficult, likely requiring an idempotency key to initiate new KYC verifications.

#### 3.4.2.1. `POST /kyc/:kycSchema`

The `POST /kyc/:kycSchema` endpoint allows a client to provide KYC data of a particular schema to the server for verification.

##### 3.4.2.1.1. Parameters

###### 3.4.2.1.1.1. Path Parameters

* `kycSchema`: {`KycSchemaEnum`} [REQUIRED]
  - The KYC schema being used in the request body

###### 3.4.2.1.1.2. Request Body

The request body schema for this endpoint must match the KYC schema selected in the path parameter.

##### 3.4.2.1.2. Responses

###### 3.4.2.1.2.1. HTTP `200`

On success, the server MUST return an HTTP `200` and respond with the following response schema:

```
{
	kycStatus: `KycStatusEnum`
}
```

###### 3.4.2.1.2.2. HTTP `400`

On failure, the server MUST respond with the following response schema:

```
{
	error: `ErrorEnum`
}
```

###### 3.4.2.1.2.3. HTTP `409`

On conflict, the server MUST respond with the following response schema:

```
{
	error: `ErrorEnum.ResourceExists`
}
```

##### 3.4.2.1.3. Semantics

This endpoint should accept data that the server will use to verify a user's KYC status. Upon receipt of valid data, the server SHOULD initiate a verification of the
provided data, likely using a third party KYC service. If the user's region as reported by the independent KYC verification process does not match their "provided" geo
(i.e., the one calculated from the caller's IP), the KYC verification SHOULD likely eventually be denied. Once a user submits KYC data, the server MUST maintain the status
of the verification. If a client has enabled webhooks for the API, the server MUST publish these statuses to the webhook endpoint asynchronously.

###### 3.4.2.1.3.1. Success

If the selected schema (via the path parameter) is valid in the user's geo, and the request body exactly matches the selected schema, and the server does not already
have a KYC verification (either pending or complete) on file for the user, the server MUST return an HTTP `200`. The response body MUST contain the current state of the
KYC verification in the `kycStatus` field.

###### 3.4.2.1.3.2. Failure

The server may fail this request for two primary reasons; if the query is poorly formed, or if the server has already received KYC data for the user in the selected schema.

###### 3.4.2.1.3.2.1. `UnsupportedSchema`

If the KYC schema selected in the path parameter is not supported for the user's geo, the server MUST return an `UnsupportedSchema` error.

###### 3.4.2.1.3.2.2. `InvalidSchema`

If the KYC schema selected in the path parameter is supported for the user's geo, but the request body does not match the selected schema, the server
MUST return an `InvalidSchema` error.

###### 3.4.2.1.3.2.3. `ResourceExists`

If the server already has KYC data on file for the user in the selected schema, the server MUST return a `ResourceExists` error.

#### 3.4.2.2. `GET /kyc/:kycSchema/status`

The `GET /kyc/:kycSchema/status` endpoint is used to query the status of an ongoing, completed, or expired KYC verification for a particular KYC schema type.
Note that these statuses MUST also be made available via webhook, if configured by the client.

##### 3.4.2.2.1. Parameters

###### 3.4.2.2.1.1. Path Parameters

* `kycSchema`: {`KycSchemaEnum`} [REQUIRED]
  - The KYC schema used for the verification whose status is being requested

##### 3.4.2.2.2. Responses

###### 3.4.2.2.2.1. HTTP `200`

On success, the server MUST return an HTTP `200` status code, with the following response body:

```
{
	kycStatus: `KycStatusEnum`
}
```

###### 3.4.2.2.2.2. HTTP `404`

If the resource does not exist, the server MUST return an HTTP `404` status code, with the following response body:

```
{
	error: `ErrorEnum.ResourceNotFound`
}
```

##### 3.4.2.2.3. Semantics

If KYC data has been submitted for a particular schema type, this endpoint MUST return the current status of the KYC verification for that schema.

###### 3.4.2.2.3.1. Success

On success, the server MUST return the status of the KYC verification.

###### 3.4.2.2.3.2. Failure

This endpoint may fail if there is no record of KYC data being submitted for the requested schema.

###### 3.4.2.2.3.2.1. `ResourceNotFound`

If the server has no data on file for a KYC verification of the given schema, the server MUST return a `ResourceNotFound` error.

#### 3.4.2.3. `DELETE /kyc/:kycSchema`

The `DELETE /kyc/:kycSchema` endpoint is used to delete a KYC record for a particular KYC schema.

##### 3.4.2.3.1. Parameters

###### 3.4.2.3.1.1. Path Parameters

* `kycSchema`: {`KycSchemaEnum`} [REQUIRED]
  - The KYC schema to delete from the server

##### 3.4.2.3.2. Responses

###### 3.4.2.3.2.1. HTTP `200`

On success, the server MUST return an HTTP `200` status code, with an empty response body.

###### 3.4.2.3.2.2. HTTP `404`

If the resource does not exist, the server MUST return an HTTP `404` status code, with the following response body:

```
{
	error: `ErrorEnum.ResourceNotFound`
}
```

##### 3.4.2.3.3. Semantics

If KYC data has been submitted for a particular schema type, this endpoint MUST delete all record of the data from the server. If a user wishes to
engage in a transfer that requires this KYC later, the user must resubmit the appropriate KYC information.

###### 3.4.2.3.3.1. Success

On successful deletion, the server MUST return an HTTP `200` status code.

###### 3.4.2.3.3.2. Failure

This endpoint may fail if there is no record of KYC data for the requested schema.

###### 3.4.2.3.3.2.1. `ResourceNotFound`

If the server has no data on file for a KYC verification of the given schema, the server MUST return a `ResourceNotFound` error.

### 3.4.3. Fiat Account Endpoints

FiatConnect's Fiat Account model consists of two primary concepts; the notion of an "account schema", and an "account type". Account schemas refer to the precise
*representation* of an account; i.e., the exact structure and fields required when communicating Fiat Account objects from client to server. All account schemas share
a set of base fields that are common to *all* schemas, such as "name", "institution", etc. Each account must have an "account type" associated with it, selected from
one of the account types supported by FiatConnect (see `FiatAccountTypeEnum` for an initial list). For example, one can imagine two different account schemas,
`CheckingAccountSchema1` and `CheckingAccountSchema2`. These two schemas *both* would be associated with the, e.g., `FiatAccountTypeEnum.CheckingAccount` account type,
but might require different data; it might be the case that different geos have different representations for communicating checking account information.

Similar to KYC schemas, providers may require different sorts of fiat (i.e., *bank*) accounts depending on both the geo of the client, *and* the particular details
of the transfer. CICO providers MUST be able to maintain multiple fiat accounts on record for each user. Negotiation of what types of fiat accounts are allowed for a
transfer, and what schemas to use to communicate the account details, occurs entirely within the quote endpoints.

In order for clients to uniquely select accounts on file when communicating with the server, the server MUST instrument their account records
with unique identifiers (i.e. a UUID) that the client can use to select an account. These identifiers MAY be globally unique across all users, but need not be. A server
MUST NOT allow a user to interact with fiat account identifiers (i.e., selecting one for a transfer) for accounts not owned by that user.

#### 3.4.3.1. `POST /accounts`

The `POST /accounts` endpoint is used to store a new fiat account on file with the server.

##### 3.4.3.1.1. Parameters

###### 3.4.3.1.1.1. Request Body

The request body must contain the following fields:

* `fiatAccountSchema`: {`FiatAccountSchemaEnum`} [REQUIRED]
  - The fiat account schema to use to add the fiat account
* `data`: {`object`} [REQUIRED]
  - An object containing the data required by the Fiat Account schema chosen in the `fiatAccountSchema` field above.

##### 3.4.3.1.2. Responses

###### 3.4.3.1.2.1. HTTP `200`

On success, the server MUST respond with an HTTP `200` status code, along with a response body with the following schema:

```
{
	fiatAccountId: `string`,
	accountName: `string`,
	institutionName: `string`,
	fiatAccountType: `FiatAccountTypeEnum`,
	fiatAccountSchema: `FiatAccountSchemaEnum`
}
```

###### 3.4.3.1.2.2. HTTP `400`

On failure, the server MUST return an HTTP `400` error code, along with a response body with the following schema:

```
{
	error: `ErrorEnum`
}
```

###### 3.4.3.1.2.3. HTTP `409`

On conflict, the server MUST return an HTTP `409` error code, along with a response body with the following schema:

```
{
	error: `ErrorEnum.ResourceExists`
}
```

##### 3.4.3.1.3. Semantics

This endpoint is used to add a new fiat account for a user.

###### 3.4.3.1.3.1. Success

On success, the server MUST return metadata associated with the account. The server MUST also generate a unique `fiatAccountId` that the
client can later use to reference the account on file.

###### 3.4.3.1.3.1. Failure

This endpoint may fail for two main reasons; if there's an issue with the selected schema/payload, or if the account already exists for
the user. The server MAY implement checks to ensure that multiple accounts whose "unique fields" are the same cannot be added for a user.
The semantics of these checks may be different for each fiat account schema/fiat account type, and are up to the server to implement. For
example, a user may not be allowed to add two debit cards with the same numbers.

###### 3.4.3.1.3.1.1. `UnsupportedSchema`

If the schema selected in the path parameter is not supported by the server, the server MUST return an `UnsupportedSchema` error.

###### 3.4.3.1.3.1.2. `InvalidSchema`

If the schema of the request body does not match the schema selected by the path parameter, the server MUST return an `InvalidSchema` error.

###### 3.4.3.1.3.1.3. `ResourceExists`

If the server determines that the user is trying to add an account that is fundamentally identical to one that the user has already added, the
server MUST return a `ResourceExists` error.

#### 3.4.3.2. `GET /accounts`

The `GET /accounts` endpoint is used to return a list of all fiat accounts on file for a user.

##### 3.4.3.2.1. Parameters

This endpoint requires no parameters.

##### 3.4.3.2.2. Responses

###### 3.4.3.2.2.1. HTTP `200`

On success, the server MUST respond with an HTTP `200` status code, along with a response body with the following schema. Informally,
this is a mapping from fiat account types that the user has on file to metadata about those accounts.

```
{
	[FiatAccountTypeEnum]: [{
		fiatAccountId: `string`,
		accountName: `string`,
		institutionName: `string`,
		fiatAccountType: `FiatAccountTypeEnum`,
		fiatAccountSchema: `FiatAccountSchemaEnum`
	}]
}
```

##### 3.4.3.2.3. Semantics

This endpoint is simply used to return a list of metadata about all of the fiat accounts that a user has on file with a provider.

###### 3.4.3.2.3.1. Success

On success, this endpoint MUST return a mapping from fiat account types to metadata about fiat accounts that the user has on file.

#### 3.4.3.3. `DELETE /accounts/:fiatAccountId`

The `DELETE /accounts/:fiatAccountId` endpoint is used to delete a user's fiat account from the server.

##### 3.4.3.3.1. Parameters

###### 3.4.3.3.1.1. Path Parameters

* `fiatAccountId`: {`string`} [REQUIRED]
  - The internal fiat account ID to delete

##### 3.4.3.3.2. Responses

###### 3.4.3.3.2.1. HTTP `200`

On success, the server MUST return an HTTP `200` status code, with an empty response body.

###### 3.4.3.3.2.2 HTTP. `404`

If the resource does not exist, the server MUST return an HTTP `404` error code, along with the following response body.

```
{
	error: `ErrorEnum.ResourceNotFound`
}
```

##### 3.4.3.3.3. Semantics

This endpoint allows a user to delete a fiat account record from the server. The user MUST only be able to delete accounts which they have
added themselves. In other words, if a server receives a `fiatAccountId` that corresponds to an account added by a user other than the one
calling the endpoint, the server MUST return an HTTP `404` error code.

###### 3.4.3.3.3.1. Success

On successful deletion of the fiat account record, the server MUST return an HTTP `200` status code.

###### 3.4.3.3.3.1. Failure

The server may fail if there is no fiat account on file with the provided `fiatAccountId` for the current user.

###### 3.4.3.3.3.1.1. `ResourceNotFound`

If no fiat account is on file with the provided `fiatAccountId` for the current user, the server MUST return a `ResourceNotFound` error.

### 3.4.4. Transfer Endpoints

Once a user has created a fiat account with a CICO provider, they may begin a transfer. CICO providers MUST reject a transfer request if a fiat account identifier is
provided that refers to an account type that is not allowed in the user's geo. A CICO provider MUST allow concurrent transfers for a single user. (This allows
a user to try a new transfer in case one is hanging for whatever reason.)

Since transfers are asynchronous and may be concurrent, this specification requires that CICO providers accept an idempotency key in case the request/response
is lost in transit and the client never receives a response. An idempotency key will allow the client to safely attempt to re-initiate a transfer without running
the risk of creating a duplicate transaction. Idempotency keys MUST be implemented as request headers according to IETF's NWG's
[draft proposal](https://datatracker.ietf.org/doc/html/draft-ietf-httpapi-idempotency-key-header-00).

#### 3.4.4.1. `POST /transfer/in`

The `POST /transfer/in` endpoint is used to initiate a new transfer in from fiat to crypto.

##### 3.4.4.1.1. Parameters

###### 3.4.4.1.1.1. Headers

* `Idempotency-Key: <idempotency-key>`
  - An idempotency key, generated by the client

###### 3.4.4.1.1.2. Request Body

The request body must contain the following fields:

* `fiatAccountId`: {`string`} [REQUIRED]
  - The fiat account ID to use for the transfer.
* `quoteId`: {`string`} [REQUIRED]
  - Identifier of the quote to use for the transfer.

##### 3.4.4.1.2. Responses

###### 3.4.4.1.2.1. HTTP `200`

On a successfully initiated transfer in request, the server MUST respond with an HTTP `200` status code, along with the following response body:

```
{
	transferId: `string`,
	transferStatus: `TransferStatusEnum`,
	transferAddress: `string`
}
```

###### 3.4.4.1.2.2. HTTP `400`

If the transfer parameters are invalid, or if the transfer is not possible for reasons *other* than the fiat account not existing, the server MUST
return an HTTP `400` error code, along with the following response body:

```
{
	error: `ErrorEnum`
}
```

###### 3.4.4.1.2.3. HTTP `404`

If the selected fiat account does not exist, the server MUST respond with an HTTP `404` error code, along with the following response body:

```
{
	error: `ErrorEnum.ResourceNotFound`
}
```

###### 3.4.4.1.2.4. Idempotency Key Errors

The server MUST follow the semantics outlined in this [draft proposal](https://datatracker.ietf.org/doc/html/draft-ietf-httpapi-idempotency-key-header-00) with
respect to idempotency key errors.

##### 3.4.4.1.3. Semantics

This endpoint allows a user to initiate a new transfer in request. The server MUST support idempotency keys, and MUST NOT accept any requests which lack them.
If a user provides a `fiatAccountId` that refers to an account they have on file that is allowed for the transfer, and the transfer parameters are acceptable,
and the user has non-expired KYC on file, and a quote with a matching `quoteId` exists for the user and has not expired, the server MUST respond with an HTTP `200` and initiate the transfer.
For the transfer, the quote with `quoteId` MUST be honored, meaning the same exchange rate and fees that were issued with the original quote MUST be used, as
well as the amount, fiat type, and crypto type.

When a new transfer is initiated, the server MUST
generate a transfer ID that the client can use to monitor the progress of the transfer. If the client has enabled webhooks, and the server supports them, the server
MUST call the user-specified webhook before returning an HTTP `200`. The response body MUST contain a `transferAddress`, indicating the address that the provider will
use to send funds to the user's address from.

###### 3.4.4.1.3.1. Success

On success, the server MUST return the `transferId` associated with the pending transfer, as well as the initial status of the transfer and the address that funds will be
sent from. If supported and configured, the endpoint MUST also report the initial status of the transfer to the client-specified webhook.

###### 3.4.4.1.3.2. Failure

This endpoint may fail for a number of reasons; the error code returned by the endpoint will vary depending on failure reason.

###### 3.4.4.1.3.2.1. `KycExpired`

If a user's KYC has expired for their current geo, the server MUST reject the transfer and return a `KycExpired` error.

###### 3.4.4.1.3.2.2. `TransferNotAllowed`

If a transfer is not allowed for a generic reason (such as unacceptable transfer parameters) the server MUST reject the transfer and return a `TransferNotAllowed` error.

###### 3.4.4.1.3.2.3. `InvalidQuote`

If the quote associated with `quoteId` is expired, or if no quote is found for the user with a matching `quoteId`, or if the provided quote is for the
wrong type of transfer, the server MUST reject the transfer and return an `InvalidQuote` error.

###### 3.4.4.1.3.2.4. `ResourceNotFound`

If the selected `fiatAccountId` is not found for the current user, the server MUST reject the transfer and return a `ResourceNotFound` error.

###### 3.4.4.1.3.2.5. `InvalidFiatAccount`

If the selected `fiatAccountId` corresponds to an existing fiat account for the user, but that fiat account is not valid for use with this transfer, the server MUST
return an `InvalidFiatAccount` error

#### 3.4.4.2. `POST /transfer/out`

The `POST /transfer/out` endpoint is used to initiate a new transfer out from crypto to fiat.

##### 3.4.4.2.1. Parameters

###### 3.4.4.2.1.1. Headers

* `Idempotency-Key: <idempotency-key>`
  - An idempotency key, generated by the client

###### 3.4.4.2.1.2. Request Body

The request body must contain the following fields:

* `fiatAccountId`: {`string`} [REQUIRED]
  - The fiat account ID to use for the transfer.
* `quoteId`: {`string`} [REQUIRED]
  - Identifier of the quote to use for the transfer.

##### 3.4.4.2.2. Responses

###### 3.4.4.2.2.1. HTTP `200`

On a successfully initiated transfer out request, the server MUST respond with an HTTP `200` status code, along with the following response body:

```
{
	transferId: `string`,
	transferStatus: `TransferStatusEnum`,
	transferAddress: `string`
}
```

###### 3.4.4.2.2.2. HTTP `400`

If the transfer parameters are invalid, or if the transfer is not possible for reasons *other* than the fiat account not existing, the server MUST
return an HTTP `400` error code, along with the following response body:

```
{
	error: `ErrorEnum`
}
```

###### 3.4.4.2.2.3. HTTP `404`

If the selected fiat account does not exist, the server MUST respond with an HTTP `404` error code, along with the following response body:

```
{
	error: `ErrorEnum.ResourceNotFound`
}
```

###### 3.4.4.2.2.4. Idempotency Key Errors

The server MUST follow the semantics outlined in this [draft proposal](https://datatracker.ietf.org/doc/html/draft-ietf-httpapi-idempotency-key-header-00) with
respect to idempotency key errors.

##### 3.4.4.2.3. Semantics

This endpoint allows a user to initiate a new transfer out request. The server MUST support idempotency keys, and MUST NOT accept any requests which lack them.
If a user provides a `fiatAccountId` that refers to an account they have on file that is allowed for the transfer, and the transfer parameters are acceptable,
and the user has non-expired KYC on file, and a quote with a matching `quoteId` exists for the user and has not expired, the server MUST respond with an HTTP `200` and initiate the transfer.
For the transfer, the quote with `quoteId` MUST be honored, meaning the same exchange rate and fees that were issued with the quote MUST be used, as
well as the amount, fiat type, and crypto type.

When a new transfer is initiated, the server MUST
generate a transfer ID that the client can use to monitor the progress of the transfer. If the client has enabled webhooks the server
MUST call the user-specified webhook before returning an HTTP `200`. The server MUST also return a `transferAddress` representing the address that the user must send
funds to in order to complete the transfer.

###### 3.4.4.2.3.1. Success

On success, the server MUST return the `transferId` associated with the pending transfer, as well as the initial status of the transfer and the address to send
funds to for the transfer. If supported and configured, the endpoint MUST also report the initial status of the transfer to the client-specified webhook.

###### 3.4.4.2.3.2. Failure

This endpoint may fail for a number of reasons; the error code returned by the endpoint will vary depending on failure reason.

###### 3.4.4.2.3.2.1. `KycExpired`

If a user's KYC has expired for their current geo, the server MUST reject the transfer and return a `KycExpired` error.

###### 3.4.4.2.3.2.2. `TransferNotAllowed`

If a transfer is not allowed for a generic reason (such as unacceptable transfer parameters) the server MUST reject the transfer and return a `TransferNotAllowed` error.

###### 3.4.4.2.3.2.3. `InvalidQuote`

If the quote associated with `quoteId` is expired, or if no quote is found for the user with a matching `quoteId`, or if the provided quote is for the
wrong type of transfer, the server MUST reject the transfer and return an `InvalidQuote` error.

###### 3.4.4.2.3.2.4. `ResourceNotFound`

If the selected `fiatAccountId` is not found for the current user, the server MUST reject the transfer and return a `ResourceNotFound` error.

###### 3.4.4.2.3.2.5. `InvalidFiatAccount`

If the selected `fiatAccountId` corresponds to an existing fiat account for the user, but that fiat account is not valid for use with this transfer, the server MUST
return an `InvalidFiatAccount` error

#### 3.4.4.3. `GET /transfer/:transferId/status`

The `GET /transfer/:transferId/status` endpoint is used to get the status of an ongoing or completed transfer, as well as metadata about
the transfer.

##### 3.4.4.3.1. Parameters

###### 3.4.4.3.1.1. Path Parameters

* `transferId`: {`string`} [REQUIRED]
  - The transfer ID for the transfer whose status to return.

##### 3.4.4.3.2. Responses

###### 3.4.4.3.2.1. HTTP `200`

On success, the server MUST return an HTTP `200` status code, along with a response body with the following schema:

```
{
	status: `TransferStatusEnum`,
	transferType: `TransferTypeEnum`,
	fiatType: `FiatTypeEnum`,
	cryptoType: `CryptoTypeEnum`,
	amountProvided: `string`,
	amountReceived: `string`,
	fee?: `string`,
	fiatAccountId: `string`,
	transferId: `string`,
	transferAddress: `string`,
	txHash?: `string`
}
```

###### 3.4.4.3.2.2. HTTP `404`

If the user has no transfer on file with the provided `transferId`, the server MUST return an HTTP `200` status code,
along with a response body with the following schema:

```
{
	error: `ErrorEnum.ResourceNotFound`
}
```

##### 3.4.4.3.3. Semantics

This endpoint is meant to be used by clients to monitor the status of an ongoing or completed transfer request, as well
as retrieve details about the transfer. Note that servers MUST also make this status information available via webhooks,
if the client has configured them.

###### 3.4.4.3.3.1. Success

If the user has a transfer on file with the corresponding `transferId`, the server MUST respond with an HTTP
`200` status code. The fields in the success response body correspond to the details of the submitted transfer. In particular,
`amountProvided` refers to the amount of fiat or crypto that the user has provided for the transfer. `amountReceived` refers to the amount
of crypto or fiat that the server will be crediting to the user. `fee`, if present refers to the fee, if any, associated with the transfer,
denominated in fiat or crypto, depending on the transfer type. `fee` is given as a string-ified numerical amount (e.g. `"1.0"`).

If the queried transfer represents a transfer in, and if the transfer has progressed to the point where the provider has sent crypto funds to the user,
the `txHash` field MUST be present, and represent the hash of the transaction in which the provider sent the user crypto funds.
The `txHash` MUST correspond to a valid transaction hash on the Celo blockchain, and its syntax must match the following regex: `/^0x([A-Fa-f0-9]{64})$/`;
namely, it must be exactly the string `0x` followed by 64 hexadecimal characters.

###### 3.4.4.3.3.2. Failure

This endpoint MUST fail when the user has no transfer on file with the provided `transferId`.

###### 3.4.4.3.3.2.1. `ResourceNotFound`

If the user has no transfer on file with the provided `transferId`, the server MUST return a `ResourceNotFound` error.

# 4. Transfer State Machines

The FiatConnect specification requires that both transfers in and out progress through a particular series of states that are made
available to the client both by polling the `GET /transfer/:transferId/status` endpoint and via webhooks. Statuses for transfers in and
transfers out must progress according to a specific *state machine*. This state machine is different for both transfers in and transfers out.

## 4.1. Transfers In

Statuses for transfers in must progress through the state machine shown below, ultimately ending at one of the two *terminal* states:
`TransferFailed` or `TransferComplete`. A server SHOULD make a best effort to reach a terminal transfer state within the `settlementTimeUpperBound`
specified for the quote associated with the transfer, if one exists. Note that since a transfer in may fail after the server has received fiat funds
from the user's fiat account but before crypto funds are sent to the user, the server MUST return any fiat funds to the user's fiat account in case
of such a failure.

```mermaid
graph LR
   TransferStarted---->TransferFiatFundsDebited
   TransferFiatFundsDebited---->TransferReceivedFiatFunds
   TransferReceivedFiatFunds---->TransferSendingCryptoFunds
   TransferSendingCryptoFunds---->TransferComplete

   TransferStarted---->TransferFailed
   TransferFiatFundsDebited---->TransferFailed
   TransferReceivedFiatFunds---->TransferFailed
   TransferSendingCryptoFunds---->TransferFailed
```

## 4.2. Transfers Out

Statuses for transfers out must progress through the state machine shown below, ultimately ending at one of the three *terminal* states:
`TransferFailed`, `TransferAmlFailed`, or `TransferComplete`. Note that at the beginning of a transfer out, a server may perform AML checks.
If these AML checks fail, the server MUST progress the state machine to `TransferAmlFailed` state. A server SHOULD
make a best effort to reach a terminal transfer state within the `settlementTimeUpperBound` specified for the quote associated with the transfer,
if one exists.

Transfers out are fundamentally different from transfer in in that they require user interaction partway through the state machine. Once a
transfer out's state moves to `TransferReadyForUserToSendCryptoFunds`, the server should wait for the user to send the proper amount of crypto
funds to the address specified for the transfer. The server MUST wait until the quote's expiration to receive the user's crypto funds before
moving to the `TransferFailed` state, though it MAY wait longer. Once the user has sent the proper amount of funds, the server MUST progress the state to
`TransferReceivedCryptoFunds`, and continue with the transfer out.

Like with transfers in, a transfer out may fail after the server has received the user's crypto funds, but before the server has credited
their fiat account with fiat funds. In case of such a failure, the server MUST return any crypto funds sent by the user back to the user's
crypto address.

```mermaid
graph LR
   TransferStarted---->TransferReadyForUserToSendCryptoFunds
   TransferReadyForUserToSendCryptoFunds---->TransferReceivedCryptoFunds
   TransferReceivedCryptoFunds---->TransferComplete

   TransferStarted---->TransferFailed
   TransferStarted---->TransferAmlFailed
   TransferReadyForUserToSendCryptoFunds---->TransferFailed
   TransferReceivedCryptoFunds---->TransferFailed
```

# 5. Webhooks

As mentioned throughout this document, CICO providers MUST support sending status updates to webhooks, whose URL is configurable by the client.
CICO providers may generate an API token that the client can use to authenticate against the FiatConnect API, allowing the server to know
where to send status updates to. This section outlines the details of webhook request syntax, as well as how the server must sign them to guarantee
the client that the requests are genuine.

## 5.1. Webhook Requests

Webhook requests made from the server MUST be in a standard format, outlined below:

```
{
	eventType: `WebhookEventTypeEnum`,
	provider: `string`,
	eventId: `string,
	timestamp: `string`,
	address: `string`,
	payload: {WebhookKycStatusEventSchema | WebhookTransferInStatusEventSchema | WebhookTransferOutStatusEventSchema}
}
```

The `eventType` field corresponds to the type of event being sent to the webhook. The `provider` field corresponds to the name of the provider
that this webhook event is coming from. This is important to include, since the client's webhook handler may receive events from multiple
different providers. The `eventId` field is a unique event ID generated by the server. The client may use this field as an idempotency key to
ensure that events are not processed more than once. The `timestamp` field represents the exact time this event was initially generated, represented
as time since Epoch in seconds. The `payload` field contains the actual event data, and will vary depending on the selected `eventType`. `address`
is the Celo address of the user this webhook event is referencing.

Servers MUST implement retries with exponential backoff when sending payloads to a client's webhook. Servers MUST retry at least 4 times if the original
request does not return an HTTP `200` status code, but MAY retry more. The waiting period between the first 4 retries SHOULD be roughly 3, 66, 731, 4098 seconds respectively,
though these are rough guidelines. Once a server has received an HTTP `200` status code from the client's webhook, it MUST stop retrying.

### 5.1.1. `WebhookKycStatusEventSchema`

`WebhookKycStatusEventSchema` is the schema that defines webhook payloads for KYC events:

```
{
	kycSchema: `KycSchemaEnum`,
	kycStatus: `KycStatusEnum`
}
```

### 5.1.2. `WebhookTransferInStatusEventSchema`

`WebhookTransferInStatusEventSchema` is the schema that defines webhook payloads for transfer in events. Note that this schema is *identical* to the
one returned from the `GET /transfer/:transferId/status` endpoint.

```
{
	status: `TransferStatusEnum`,
	transferType: `TransferTypeEnum`,
	fiatType: `FiatTypeEnum`,
	cryptoType: `CryptoTypeEnum`,
	amountProvided: `string`,
	amountReceived: `string`,
	fee?: `string`,
	fiatAccountId: `string`,
	transferId: `string`,
	transferAddress: `string`
	txHash?: `string`
}
```

### 5.1.3. `WebhookTransferOutStatusEventSchema`

`WebhookTransferOutStatusEventSchema` is the schema that defines webhook payloads for transfer out events. Note that this schema is nearly identical to the
one returned from the `GET /transfer/:transferId/status` endpoint; it lacks the optional `txHash` field, since it is not relevenat for transfers out.

```
{
	status: `TransferStatusEnum`,
	transferType: `TransferTypeEnum`,
	fiatType: `FiatTypeEnum`,
	cryptoType: `CryptoTypeEnum`,
	amountProvided: `string`,
	amountReceived: `string`,
	fee?: `string`,
	fiatAccountId: `string`,
	transferId: `string`,
	transferAddress: `string`
}
```

## 5.2. Webhook Request Signing

Requests made to a client's webhook handler MUST be signed, in order to prevent non-genuine webhook requests sent by malicious actors from being
interpreted as genuine. To facilitate this, the CICO provider MUST generate a secret key that it shares with the client developer that can be used
to recompute and verify the signature provided by the server.

Server requests to webhooks MUST contain a `FiatConnect-Signature` header containing an HMAC. Using the private key that the CICO provider shares
with the client developer, a webhook can verify that a request is genuine by recomputing the digest and comparing it against the one provided by
the CICO provider.

The `FiatConnect-Signature` header MUST contain two comma-separated key-value pairs. The first is of the form `t=<unix_timestamp>`, and represents
the UNIX timestamp that the request was sent. The second will be of the form `v1=<signature>`, and contain the signature itself. The signature
MUST be computed from the shared webhook private key and a dot-separated string consisting of the UNIX timestamp joined with the request body.
This signature verification design is based off of [Persona's webhook documentation](https://docs.withpersona.com/docs/best-practices).

# 6. Amounts

In the interest of preventing rounding errors, [floating point errors](https://floating-point-gui.de/), or similar errors, numerical amounts 
MUST be communicated as strings for every request parameter and every response field throughout the FiatConnect API. 

Clients and servers alike SHOULD NOT cast fiat or crypto amounts to floating point numbers or integers, but should instead
consider libraries such as [bignumber.js](https://github.com/MikeMcl/bignumber.js/) for handling large numbers and decimals 
to an arbitrary number of decimal places.

## 6.1 Units

Except where otherwise noted, amounts of cryptocurrencies MUST be given in the units of that currency, e.g. `1.5` Celo means
one and a half Celo (not `1.5e-18` Celo, which would be the case if amounts were instead given in 
[wei](https://www.investopedia.com/terms/w/wei.asp#:~:text=What%20Is%20a%20Wei%3F,one%20quintillionth%20of%20an%20ether.)).

Amounts of fiat currencies MUST be given in the units of that currency, e.g. `5.00` USD means five USD.

## 6.2 Decimal Places

Given that amounts are communicated in divisible units-- such as US Dollars, which are divisible into `100` cents, or Celo, which 
is divisible into `10^18` wei-- it is expected that the client or server will sometimes need to communicate non-integer amounts. 
All amounts MUST match the following regex: `/^[0-9]+\.?[0-9]*$/` . Put in words, the required format is:
- one or more digits 0-9
- optionally, a period (used as a decimal mark for non-integer amounts)
- zero or more digits 0-9

## 6.3 Precision

FiatConnect-compliant API's MUST observe the maximum number of decimal places of precision for every cryptocurrency in `CryptoTypeEnum`, and 2 decimal places of
precision for all fiat currencies. (Most, but not all, ERC-20 tokens offer up to 18 decimal places of precision.)

Note that this does NOT mean a CICO provider is required to support transfers of 
extremely small amounts; see [here](#3411322-cryptoamounttoolow).

Note also that client and server MUST include all [significant figures](https://en.wikipedia.org/wiki/Significant_figures)
of an amount, but MAY omit trailing zeroes. For example, "half a US Dollar" could be given as `"0.5"` or `"0.50"`, 
and "one and one-tenth Celo" could be given as `"1.1"`.

# 7. AML Considerations

Anti-Money Laundering (AML) checks are an important part of regulatory compliance in the financial services industry. This proposal, however,
does not require CICO providers to implement such checks. Providers MAY implement their own AML checks if they wish, according to local law.
It is likely that by requiring KYC verification, CICO providers will be able to collect enough relevant information on the user in order to
facilitate AML checks, if required.

# 8. Sandbox Environment

In order to facilitate ease of client integration and testing against FiatConnect-compliant APIs, each FiatConnect API MUST have a corresponding *sandbox*
API available. This sandbox API should be identical in behavior to the production FiatConnect API in every way, except for a number of key differences.

## 8.1. Celo Network

FiatConnect sandbox API implementations MUST operate against the Celo Alfajores network, rather than Mainnet. The Alfajores network operates with tokens
with no monetary value, which allows testing of transfers without transacting real-world value.

## 8.2. Authentication

Sandbox servers MUST recognize a different set of client API keys than the production API, in order to allow clients to register a different set of webhook URLs
than those recognized by the production API.

## 8.3. KYC

All KYC submissions in the sandbox environment MUST eventually result in the `KycStatusEnum.KycApproved` status. Sandbox environments MUST still send status updates
by webhook throughout the process, but verifications will always end in approval.

## 8.4. Fiat Accounts

Sandbox APIs MUST never internally connect to a provided Fiat Account or perform any sort of validation that user-submitted Fiat Account details are "valid".
Sandbox APIs MUST never actually interact with a user's personal fiat accounts.

## 8.5. Transfers

Transfers in sandbox APIs will be much like ones in production APIs, but they will transfer tokens on the Alfajores network, which have no actual value. Sandbox APIs
MUST never debit/credit actual fiat accounts, but they SHOULD receive/send crypto from/to the user's address depending on the type of transfer requested.

# 9. Definitions

This document references a number of definitions, all of which are enumerated in their entirety below. There are two "types" of definitions; those
which are static, and *not* subject to change upon this proposal's acceptance, and those that are dynamic, and meant to be extended by the community.

## 9.1. Static Definitions

### 9.1.1. `KycStatusEnum`

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

### 9.1.2. `ErrorEnum`

An enum listing the error types used by various endpoints.

```
[
	`InvalidSignature`
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
	`KycExpired`,
	`Unauthorized`,
	`SessionExpired`,
	`InvalidParameters`,
	`ContractLoginNotSupported`,
	`NonceInUse`,
	`IssuedTooEarly`,
	`ExpirationTooLong`,
	`InvalidFiatAccount`,
	`InvalidQuote`
]
```

### 9.1.3. `TransferTypeEnum`

An enum listing transfer types.

```
[
	`TransferIn`,
	`TransferOut`
]
```

### 9.1.3. `WebhookEventTypeEnum`

An enum listing payload types for webhook status updates.

```
[
	`WebhookKycStatusEvent`
	`WebhookTransferInStatusEvent`,
	`WebhookTransferOutStatusEvent`
]
```

### 9.1.4. `TransferStatusEnum`

An enum listing the types of transfer statuses recognized by FiatConnect.

```
[
	`TransferStarted`,
	`TransferFiatFundsDebited`,
	`TransferSendingCryptoFunds`,
	`TransferAmlFailed`,
	`TransferReadyForUserToSendCryptoFunds`,
	`TransferReceivedCryptoFunds`,
	`TransferReceivedFiatFunds`,
	`TransferComplete`,
	`TransferFailed`
]
```
## 9.2. Dynamic Definitions

### 9.2.1. `FiatTypeEnum`

An enum listing the types of fiat currencies supported by FiatConnect. All values should be [ISO 4217 Currency Codes](https://en.wikipedia.org/wiki/ISO_4217)

```
[
  `USD`,
  `EUR`,
  `BRL`,
  `GNF`,
  `INR`,
  `NGN`,
  `GHS`,
  `KES`,
  `ZAR`,
  `PHP`,
  `UGX`,
  `GBP`,
  `XOF`,
  `RWF`,
  `CNY`,
  `XAF`,
  `ARS`,
  `BOB`,
  `CLP`,
  `COP`,
  `FKP`,
  `GYD`,
  `PYG`,
  `PEN`,
  `SRD`,
  `UYU`,
  `VES`,
  `MXN`,
  `PAB`,
]
```

### 9.2.2. `CryptoTypeEnum`

An enum listing the types of crypto tokens supported by FiatConnect.

```
[
  `cUSD`,
  `cEUR`,
  `cREAL`,
  `CELO`
]
```

### 9.2.3. `KycSchemaEnum`

An enum listing the KYC schema types recognized by the FiatConnect specification.

```
[
	`PersonalDataAndDocuments`,
	`PersonalDataAndDocumentsDetailed`
]
```

### 9.2.4. `FiatAccountTypeEnum`

An enum listing the *types* of Fiat Accounts recognized by the FiatConnect specification. A Fiat Account Type is a property of each Fiat Account Schema, and
represents what *kind* of account that schema represents.

```
[
	`BankAccount`,
	`MobileMoney`,
	`DuniaWallet`
]
```

### 9.2.5. `FeeTypeEnum`

An enum listing the *types* of fees that providers may require on transfers.

```
[
	`KycFee`,
	`PlatformFee`
]
```

### 9.2.6. `FeeFrequencyEnum`

An enum listing the frequency, or how often, a particular fee needs to be paid.

```
[
	`OneTime`,
	`Recurring`
]
```

### 9.2.7. `FiatAccountSchemaEnum`

```
[
	`AccountNumber`,
	`MobileMoney`,
	`DuniaWallet`,
	`IBANNumber`,
	`IFSCAccount`,
	`PIXAccount`,
]
```

## 9.3. Initial Entity Support

### 9.3.1. KYC Schemas

#### 9.3.1.1. `PersonalDataAndDocuments`

A KYC schema containing personal data about a user, as well as documents such as an ID photo and selfie.

```
{
	firstName: `string`,
	middleName?: `string`,
	lastName: `string`,
	dateOfBirth: {
		day: `string`,
		month: `string`,
		year: `string`
	},
	address: {
		address1: `string`,
		address2?: `string`,
		isoCountryCode: `string`,
		isoRegionCode: `string`,
		city: `string`,
		postalCode?: `string`
	},
	phoneNumber: `string`,
	selfieDocument: `string`,
	identificationDocument: `string`
}
```

The `selfieDocument` and `identificationDocument` fields should be base64 encoded binary blobs representing images.

#### 9.3.1.2. `PersonalDataAndDocumentsDetailed`

`PersonalDataAndDocumentsDetailed` is a more detailed version of the `PersonalDataAndDocuments` KYC Schema,
allowing the user to provide more information for CICO providers which require it.

```
{
	firstName: `string`,
	middleName?: `string`,
	lastName: `string`,
	dateOfBirth: {
		day: `string`,
		month: `string`,
		year: `string`
	},
	address: {
		address1: `string`,
		address2?: `string`,
		isoCountryCode: `string`,
		isoRegionCode: `string`,
		city: `string`,
		postalCode?: `string`
	},
	phoneNumber: `string`,
	email: `string`,
	selfieDocument: `string`,
	identificationDocumentType: `IdentificationDocumentTypeEnum`,
	identificationDocumentFront: `string`,
	identificationDocumentBack?: `string`
}
```

The `phoneNumber` field is REQUIRED and MUST follow the formating of the [E.164 international standard](https://en.wikipedia.org/wiki/E.164). It MUST contain all parts of the phone number including the Country Code.

The `email` field is REQUIRED and MUST be a [valid email](https://en.wikipedia.org/wiki/Email_address#Syntax).

The `identificationDocumentType` field MUST be selected from `IdentificationDocumentTypeEnum`, and is used to represent the kind of document being submitted.

The `selfieDocument`, `identificationDocumentFront` and `identificationDocumentBack` fields MUST be base64 encoded binary blobs representing images.

The `identificationDocumentBack` field is REQUIRED if the `identificationDocumentType` is `IDC` or `DL`. Otherwise, it is OPTIONAL.

##### 9.3.1.2.1. `IdentificationDocumentType`

`IdentificationDocumentType` is used to represent the type of document being submitted with a `PersonalDataAndDocumentsDetailed` KYC schema.

```
[
	`IDC`,
	`PAS`,
	`DL`
]
```

The enum values represent the following forms of identification:
* `IDC`: State-issued identity card
* `PAS`: Passport
* `DL`: Driver's Licenes

### 9.3.2. Fiat Account Schemas

All Fiat Account Schemas supported by FiatConnect MUST contain the `accountName`, `institutionName`, and `fiatAccountType` fields. `accountName` is a friendly, user-definable name for the account.
`institutionName` is a user-friendly name representing the financial institution/organization the account is with, and `fiatAccountType` is a `FiatAccountTypeEnum` value,
representing the type of fiat account this schema represents. The `institutionName` and `accountName` fields are required in order for the API to return obfuscated but distinguishable
account information from the `GET /accounts` endpoint.

####  9.3.2.1. `AccountNumber`

`AccountNumber` is a Fiat Account schema that represents accounts where the only identifying information required is some `accountNumber` string.

```
{
	accountName: `string`,
	institutionName: `string`,
	accountNumber: `string`,
	country: `string`,
	fiatAccountType: `FiatAccountTypeEnum.BankAccount`
}
```

The `country` field should be a [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2) country code. The field is for providers to specify which country this data is meant for. For example, if a provider is expecting an `AccountNumber`
schema for Nigeria, they can set the `allowedValues` field for `country` in the quote endpoint response to `['NG']`. With this information, the client will know
to prompt the user for a Nigeria-specific account number.

Depending on the `allowedValues` field for `country`, the client SHOULD impose restrictions on the type of data the user can provide for the `accountNumber` field. A non-exhaustive list
is below:

* `'NG'`: The account number should be exactly 10 digits long, and only include the numbers 0-9.

#### 9.3.2.2. `MobileMoney`

`MobileMoney` is a fiat account schema pertaining to virtual wallets provided by telecommunications companies in Africa.
The `operator` field represents the name of the mobile operator, and `mobile` is the phone number of the end user. 
The property `mobile` MUST follow the [International format E.164 from ITU-T](https://en.wikipedia.org/wiki/E.164) 
with a plus sign prefix (i.e., +14155552671 for US). Finally, the `country` field MUST be a [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2) country code.

```
{
  accountName: `string`,
  institutionName: `string`,
  mobile: `string`,
  country: `string`,
  operator: `string`,
  fiatAccountType: `FiatAccountTypeEnum.MobileMoney`
}
```

#### 9.3.2.3. `DuniaWallet`

The Dunia wallet is a proprietary wallet for people that have an account on the [**Dunia platform**](https://www.dunia.africa). So, any account on Dunia
platform can be used to consume Fiat Connect services by providing their `mobile` as identifier. The property `mobile` should follow the [International format E.164 from ITU-T](https://en.wikipedia.org/wiki/E.164) (i.e., +14155552671 for US).

```
{
  accountName: `string`,
  institutionName: `string`,
  mobile: `string`,
  fiatAccountType: `FiatAccountTypeEnum.DuniaWallet`
}
```

####  9.3.2.4. `IBANNumber`

`IBANNumber` is a representation of a bank account that is agnostic to the user's home country. The primary identifying field is `iban`, which represents an
[International Bank Account Number](https://en.wikipedia.org/wiki/International_Bank_Account_Number). See [here](https://www.worldfirst.com/uk/help-support/what-is-an-iban-number/) for more context.

```
{
	accountName: `string`,
	institutionName: `string`,
	iban: `string`,
	country: `string`,
	fiatAccountType: `FiatAccountTypeEnum.BankAccount`
}
```

The `country` field MUST be a [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2) country code. The syntax of the `iban` field MUST correspond to that of a valid International
Bank Account Number.

#### 9.3.2.5. `IFSCAccount`

`IFSCAccount` is an account schema that represents transfers for `INR` currency.

```
{
	accountName: `string`,
	institutionName: `string`,
	ifsc: `string`,
	accountNumber: `string`,
	country: `string`,
	fiatAccountType: `FiatAccountTypeEnum.BankAccount`
}
```

The `country` field is a [ISO 3166-1 alpha-2](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2) country code.
The `ifsc` or Indian Financial System Code is an 11-digit alpha-numeric code that is unique for bank branches that offer online money transfer options.
The `accountNumber` field is unique between individuals and no two banks or account holders can have the same account number.
Banks use different starting codes for their branches for differentiation. (In India, bank account numbers usually contain 7 to 21 digits.)

#### 9.3.2.6. `PIXAccount`

`PIXAccount` is a fiat account type for [PIX](https://www.bcb.gov.br/en/financialstability/pix_en), a Brazilian instant payment method created by the Brazilian Central Bank in which funds are transferred between accounts in a few seconds, at any time or day. Pix transfers can be carried out from a checking account, savings account or prepaid payment account.

```
{
  accountName: `string`,
  institutionName: `string`,
  keyType: `PIXKeyTypeEnum`,
  key: `string`,
  fiatAccountType: `FiatAccountTypeEnum.BankAccount`
}
```

##### 9.3.2.6.1. `PIXKeyTypeEnum`

`keyType` MUST be one of the following values:

```
[
  `EMAIL`,
  `CPF`,
  `PHONE`,
  `RANDOM`
]
```

If `keyType` is `EMAIL`, `key` MUST be a [valid email](https://en.wikipedia.org/wiki/Email_address#Syntax).

If `keyType` is CPF, `key` MUST be a [CPF number](https://en.wikipedia.org/wiki/CPF_number).

If `keyType` is `PHONE`, `key` MUST be an 11-digit [Brazilian mobile phone number](https://en.wikipedia.org/wiki/Telephone_numbers_in_Brazil). Area code MUST be included. The string MUST match the regex `/[0-9]{11}/` (a series of 11 digits, 0 through 9).

Otherwise, if `keyType` is `RANDOM`, `key` MUST be a string of 32 characters consisting only of numbers 0-9, [ISO basic Latin alphabetic](https://en.wikipedia.org/wiki/ISO_basic_Latin_alphabet) letters, or [hyphen-minus](https://en.wikipedia.org/wiki/Hyphen-minus). If `keyType` is `RANDOM`, `key` represents a random key generated by the user's bank.



# 10. References

## 10.1. Normative References

### 10.1.1. [RFC2119]

Bradner, S., "Key words for use in RFCs to Indicate Requirement Levels", BCP 14, RFC 2119, DOI 10.17487/RFC2119, March 1997, <https://www.rfc-editor.org/info/rfc2119>.

### 10.1.2. [RFC8174]

Leiba, B., "Ambiguity of Uppercase vs Lowercase in RFC 2119 Key Words", BCP 14, RFC 8174, DOI 10.17487/RFC8174, May 2017, <https://www.rfc-editor.org/info/rfc8174>.

### 10.1.3. [RFC7519]

M. Jones, "JSON Web Token", RFC7519, May 2015, <https://datatracker.ietf.org/doc/html/rfc7519>.

### 10.1.4. [EIP-4361]

EIP-4361: Sign-In with Ethereum, <https://eips.ethereum.org/EIPS/eip-4361>

### 10.1.5. [EIP-55]

EIP-55: Mixed-case checksum address encoding, <https://eips.ethereum.org/EIPS/eip-55>

### 10.1.6. [EIP-191]

EIP-191: Signed Data Standard, <https://eips.ethereum.org/EIPS/eip-191>

### 10.1.7. [EIP-1271]

EIP-1271: Standard Signature Validation Method for Contracts, <https://eips.ethereum.org/EIPS/eip-1271>

### 10.1.8. [EIP-2098]

EIP-2098: Compact Signature Representation, <https://eips.ethereum.org/EIPS/eip-2098>

## 10.2. Informative References

### 10.2.1. Webhook Best Practices

Persona, "Best Practices", <https://docs.withpersona.com/docs/best-practices>.

### 10.2.2. Idempotency Keys

J. Jena, The Idempotency-Key HTTP Header Field, July 2021, <https://datatracker.ietf.org/doc/html/draft-ietf-httpapi-idempotency-key-header-00>

### 10.2.3. ISO 3166-1

ISO 3166-1 <https://en.wikipedia.org/wiki/ISO_3166-1>

### 10.2.4. ISO 3166-2

ISO 3166-2 <https://en.wikipedia.org/wiki/ISO_3166-2>

### 10.2.5. ISO 8601

ISO 8601 <https://en.wikipedia.org/wiki/ISO_8601>
