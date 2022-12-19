# FiatConnect

The FiatConnect API Specification.

## Specification

[fiatconnect-api.md](./fiatconnect-api.md) describes the FiaConnect
API specification.

[swagger.yaml](./swagger.yaml) is an OpenAPI 2.0 specification for
FiatConnect.

## Versioning

We use [semantic versioning](https://semver.org/) to version the
API specification.

There are now at least one cash-in/cash-out (CICO) provider offering a FiatConnect-compliant
API in a production environment, and at least one client integrating against it. As such, we have incremented the
version to `1.0.0` (from a version `0.1.0`). At this point, the API specification SHOULD be considered stable. 

Breaking changes MAY still be introduced, but once accepted, they will be merged to a development branch until CICO 
providers and clients are ready to support them. Once a set of breaking changes is agreed upon, the version will be 
incremented to `2.0.0`, and CICO providers MAY offer a version 2 of their API. At this point, CICO 
providers SHOULD choose a sunset window for their v1 API, and advertise it to all clients consuming their API.

Note that the addition of fiat account schemas and KYC schemas is NOT considered a breaking change, since CICO providers
are welcome to ignore them. As such, FiatConnect clients SHOULD hide quotes requiring any unrecognized fiat account 
schema or KYC schema. 
