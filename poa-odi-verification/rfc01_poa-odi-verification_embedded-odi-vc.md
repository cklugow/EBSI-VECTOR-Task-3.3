# RFC: Including ODI and PoA in VC

### Authors

* https://github.com/MartinRobomaze/
* https://github.com/marosrbm/
* Yiorgos

### Collaborators

* https://github.com/cklugow/

### Timeline

Created: 2024-11-20
Last updated: 2024-11-20

### Approvers

*

## Overview

Power of Attorney (PoA) was introduced to suit the representation purpose. In order to achieve this it needs to be
coupled with the organization for which the Organizational Digital Identifier is used. In practice, a company nominee should be able to present
with the PoA credential along with ODI so that the verifier can check not only the PoA validity but also the validity of the ODI and the link between these two.

## Credential schemas

### ODI
Issued by a Business Registry (EBSI RTAO/TAO) issuer to a specific Company (EBSI TI). The subject contains the company DID.

Schema:
https://api-pilot.ebsi.eu/trusted-schemas-registry/v3/schemas/zFMNrfecxyCEaLgpusf9CP5aq651BpWUV4BM9x8KXxzNY

### PoA
Issued by a Company (EBSI TI) to a natural person. The subject contains natural person's DID (did:key).

Schema:
https://api-pilot.ebsi.eu/trusted-schemas-registry/v2/schemas/z2sbTT23X2zfsdMCPFMC7GiUwyHE91Lg9BpeEA5uqtT9s


### Prerequisites

The wallet needs to hold a valid ODI and POA verifiable credentials.

## Include ODI when presenting POA

The Verifiable Presenation will contain both the ODI and POA, the wallet should include the ODI if present when returning a POX.
to add a link to ODI VP that is published online. The evidence field should also add digest property so that the verifier may check if the linked document is the same.

### Prerequisites

* ODI VP published via publicly available URL

### PoA Presentation

When requesting the POA the requester should also optionally request the ODI. Using a presentation definition similar to below: 
```json
{
  "id": "request-embedded-poa",
  "input_descriptors": [
    {
      "id": "poa",
      "name": "A POA",
      "purpose": "Check your Power of Attorney (POA)",
      "constraints": {
        "fields": [
          {
            "path": [
              "$.type"
            ],
            "filter": {
              "type": "array",
              "contains": {
                "type": "string",
                "pattern": "PowerOfAttorney"
              }
            }
          }
        ]
      }
    },
    {
      "id": "oid",
      "name": "Organizational Digital Identifier (ODI)",
      "purpose": " ensure the PoA holder may act on behalf of a company",
      "constraints": {
        "fields": [
          {
            "path": [
              "$.type"
            ],
            "optional": true,
            "filter": {
              "type": "array",
              "contains": {
                "type": "string",
                "pattern": "OrganizationalDigitalIdentifier"
              }
            }
          }
        ]
      }
    }
  ]
}
```
### 

#### Example of PoA VC with an included ODI as JWT:

```json
{
  "@context": [
      "https://www.w3.org/2018/credentials/v1"
  ],
  "type": ["VerifiablePresentation"],
  "verifiableCredential": [{
    "@context": [
      "https://www.w3.org/2018/credentials/v1"
    ],
    "credentialSchema": {
      "id": "https://api-pilot.ebsi.eu/trusted-schemas-registry/v3/schemas/z2sbTT23X2zfsdMCPFMC7GiUwyHE91Lg9BpeEA5uqtT9s",
      "type": "FullJsonSchemaValidator2021"
    },
    "credentialStatus": {
      "id": "https://api-conformance.ebsi.eu/trusted-issuers-registry/v5/issuers/did:ebsi:zzhg8kdpKQ1svP7w5hkGZqf/proxies/0x45f01de57aeff6389c482b93c15f44aa58331bb8274dcefb02893745c2686a9d/status/0#0",
      "statusListCredential": "https://api-conformance.ebsi.eu/trusted-issuers-registry/v5/issuers/did:ebsi:zzhg8kdpKQ1svP7w5hkGZqf/proxies/0x45f01de57aeff6389c482b93c15f44aa58331bb8274dcefb02893745c2686a9d/status/0",
      "statusListIndex": "0",
      "statusPurpose": "revocation",
      "type": "StatusList2021Entry"
    },
    "credentialSubject": {
      "dateOfBirth": "1979-11-11",
      "familyName": "YOGI",
      "firstName": "BEAR",
      "id": "did:key:z2dmzD81cgPx8Vki7JbuuMmFYrWPgYoytykUZ3eyqht1j9Kbs5am9syyRGgTSgdrRFLmQeWbFXN9TLw2x5V2HTtZjKgD9iP6iXxAouKZXHHSV3HFnpKX65grm14mx5XJ9daMy22rzRCKxEwjwucNyKibAhaxHX3zEbYyeyPma4e2f4MVD6",
      "nationality": "SVK",
      "poaScope": false,
      "poaType": [
        "Driving company vehicles"
      ]
    },
    "id": "urn:uuid:f1464e6a-c794-4546-a33d-163e482bf02f",
    "issuanceDate": "2024-11-26T15:24:05.394Z",
    "issued": "2024-11-26T15:24:05.394Z",
    "issuer": "did:ebsi:zzhg8kdpKQ1svP7w5hkGZqf",
    "type": [
      "VerifiableCredential",
      "VerifiableAuthorisation",
      "PowerOfAttorney"
    ],
    "validFrom": "2024-11-26T15:24:05.394Z",
    "validUntil": "2024-12-26T15:24:05.394Z",
  },
    "eyJhbGciOiJFUzI1NiIsImtpZCI6ImRpZDplYnNpOnpvOVY0RkNLOUNpS3VXUU1wdWJNekE0IzB4azBZUzhQWUYxTmFIZkxZRzN4MEZmZ2I3X1JsQ0llb2VwXzZRZTRIelUiLCJ0eXAiOiJKV1QifQ.eyJleHAiOjE3MzI5OTUzNjMsImlhdCI6MTczMDQwMzM2MywiaXNzIjoiZGlkOmVic2k6em85VjRGQ0s5Q2lLdVdRTXB1Yk16QTQiLCJqdGkiOiJ1cm46dXVpZDpmMjE5MTg2My0xZTcwLTQ5M2EtOWI4NS1iMjBjMGM2ZDQzOWQiLCJuYmYiOjE3MzA0MDMzNjMsInN1YiI6ImRpZDplYnNpOnoyNVpueVc5ZnZpUXJqanJQMXc4RnFHYSIsInZjIjp7IkBjb250ZXh0IjpbImh0dHBzOi8vd3d3LnczLm9yZy8yMDE4L2NyZWRlbnRpYWxzL3YxIl0sImNyZWRlbnRpYWxTY2hlbWEiOnsiaWQiOiJodHRwczovL2FwaS1waWxvdC5lYnNpLmV1L3RydXN0ZWQtc2NoZW1hcy1yZWdpc3RyeS92Mi9zY2hlbWFzL3pGTU5yZmVjeHlDRWFMZ3B1c2Y5Q1A1YXE2NTFCcFdVVjRCTTl4OEtYeHpOWSIsInR5cGUiOiJGdWxsSnNvblNjaGVtYVZhbGlkYXRvcjIwMjEifSwiY3JlZGVudGlhbFN0YXR1cyI6eyJpZCI6Imh0dHBzOi8vd2FsbGV0LmRldi50cml2ZXJpYS5pby9hcGkvdjEvaXNzdWVyL2M3YTVmOTk1LTk3NmUtMTFlZi04NzhkLTBhNThhOWZlYWMwMi9zdGF0dXMvMCMwIiwic3RhdHVzTGlzdENyZWRlbnRpYWwiOiJodHRwczovL3dhbGxldC5kZXYudHJpdmVyaWEuaW8vYXBpL3YxL2lzc3Vlci9jN2E1Zjk5NS05NzZlLTExZWYtODc4ZC0wYTU4YTlmZWFjMDIvc3RhdHVzLzAiLCJzdGF0dXNMaXN0SW5kZXgiOiIwIiwic3RhdHVzUHVycG9zZSI6InJldm9jYXRpb24iLCJ0eXBlIjoiU3RhdHVzTGlzdDIwMjFFbnRyeSJ9LCJjcmVkZW50aWFsU3ViamVjdCI6eyJpZCI6ImRpZDplYnNpOnoyNVpueVc5ZnZpUXJqanJQMXc4RnFHYSIsImlzc3VpbmdBdXRob3JpdHlOYW1lIjoiUlRBTyIsImlzc3VpbmdDb3VudHJ5IjoiU1ZLIiwibGVnYWxGb3JtIjoiSW5jIiwibGVnYWxOYW1lIjoiQUNNRSJ9LCJpZCI6InVybjp1dWlkOmYyMTkxODYzLTFlNzAtNDkzYS05Yjg1LWIyMGMwYzZkNDM5ZCIsImlzc3VhbmNlRGF0ZSI6IjIwMjQtMTAtMzFUMTk6MzY6MDMuNDQ0WiIsImlzc3VlZCI6IjIwMjQtMTAtMzFUMTk6MzY6MDMuNDQ0WiIsImlzc3VlciI6ImRpZDplYnNpOnpvOVY0RkNLOUNpS3VXUU1wdWJNekE0IiwidHlwZSI6WyJWZXJpZmlhYmxlQ3JlZGVudGlhbCIsIlZlcmlmaWFibGVBdHRlc3RhdGlvbiIsIk9yZ2FuaXphdGlvbmFsRGlnaXRhbElkZW50aWZpZXIiXSwidmFsaWRGcm9tIjoiMjAyNC0xMC0zMVQxOTozNjowMy40NDRaIiwidmFsaWRVbnRpbCI6IjIwMjQtMTEtMzBUMTk6MzY6MDMuNDQ0WiJ9fQ.HE46Lyy1EJCVsdW9Mi1OzpbI04awrdWC8m7xFpPHKvikA6ORuGR9I1UMUuqcAKxDy28H7jNOFSbM363xaANvfQ"  
  ]
}
```

#### Example of PoA VC with an included ODI as EnvelopedCredential:

```json
{
  "@context": [
      "https://www.w3.org/ns/credentials/v2"
  ],
  "verifiableCredential": [{
    "@context": [
      "https://www.w3.org/ns/credentials/v2"
    ],
    "credentialSchema": {
      "id": "https://api-pilot.ebsi.eu/trusted-schemas-registry/v3/schemas/z2sbTT23X2zfsdMCPFMC7GiUwyHE91Lg9BpeEA5uqtT9s",
      "type": "FullJsonSchemaValidator2021"
    },
    "credentialStatus": {
      "id": "https://api-conformance.ebsi.eu/trusted-issuers-registry/v5/issuers/did:ebsi:zzhg8kdpKQ1svP7w5hkGZqf/proxies/0x45f01de57aeff6389c482b93c15f44aa58331bb8274dcefb02893745c2686a9d/status/0#0",
      "statusListCredential": "https://api-conformance.ebsi.eu/trusted-issuers-registry/v5/issuers/did:ebsi:zzhg8kdpKQ1svP7w5hkGZqf/proxies/0x45f01de57aeff6389c482b93c15f44aa58331bb8274dcefb02893745c2686a9d/status/0",
      "statusListIndex": "0",
      "statusPurpose": "revocation",
      "type": "StatusList2021Entry"
    },
    "credentialSubject": {
      "dateOfBirth": "1979-11-11",
      "familyName": "YOGI",
      "firstName": "BEAR",
      "id": "did:key:z2dmzD81cgPx8Vki7JbuuMmFYrWPgYoytykUZ3eyqht1j9Kbs5am9syyRGgTSgdrRFLmQeWbFXN9TLw2x5V2HTtZjKgD9iP6iXxAouKZXHHSV3HFnpKX65grm14mx5XJ9daMy22rzRCKxEwjwucNyKibAhaxHX3zEbYyeyPma4e2f4MVD6",
      "nationality": "SVK",
      "poaScope": false,
      "poaType": [
        "Driving company vehicles"
      ]
    },
    "id": "urn:uuid:f1464e6a-c794-4546-a33d-163e482bf02f",
    "issuanceDate": "2024-11-26T15:24:05.394Z",
    "issued": "2024-11-26T15:24:05.394Z",
    "issuer": "did:ebsi:zzhg8kdpKQ1svP7w5hkGZqf",
    "type": [
      "VerifiableCredential",
      "VerifiableAuthorisation",
      "PowerOfAttorney"
    ],
    "validFrom": "2024-11-26T15:24:05.394Z",
    "validUntil": "2024-12-26T15:24:05.394Z",
  },
    {
      "@context": "https://www.w3.org/ns/credentials/v2",
      "id": "data:application/vc+jwt,eyJhbGciOiJFUzI1NiIsImtpZCI6ImRpZDplYnNpOnpvOVY0RkNLOUNpS3VXUU1wdWJNekE0IzB4azBZUzhQWUYxTmFIZkxZRzN4MEZmZ2I3X1JsQ0llb2VwXzZRZTRIelUiLCJ0eXAiOiJKV1QifQ.eyJleHAiOjE3MzI5OTUzNjMsImlhdCI6MTczMDQwMzM2MywiaXNzIjoiZGlkOmVic2k6em85VjRGQ0s5Q2lLdVdRTXB1Yk16QTQiLCJqdGkiOiJ1cm46dXVpZDpmMjE5MTg2My0xZTcwLTQ5M2EtOWI4NS1iMjBjMGM2ZDQzOWQiLCJuYmYiOjE3MzA0MDMzNjMsInN1YiI6ImRpZDplYnNpOnoyNVpueVc5ZnZpUXJqanJQMXc4RnFHYSIsInZjIjp7IkBjb250ZXh0IjpbImh0dHBzOi8vd3d3LnczLm9yZy8yMDE4L2NyZWRlbnRpYWxzL3YxIl0sImNyZWRlbnRpYWxTY2hlbWEiOnsiaWQiOiJodHRwczovL2FwaS1waWxvdC5lYnNpLmV1L3RydXN0ZWQtc2NoZW1hcy1yZWdpc3RyeS92Mi9zY2hlbWFzL3pGTU5yZmVjeHlDRWFMZ3B1c2Y5Q1A1YXE2NTFCcFdVVjRCTTl4OEtYeHpOWSIsInR5cGUiOiJGdWxsSnNvblNjaGVtYVZhbGlkYXRvcjIwMjEifSwiY3JlZGVudGlhbFN0YXR1cyI6eyJpZCI6Imh0dHBzOi8vd2FsbGV0LmRldi50cml2ZXJpYS5pby9hcGkvdjEvaXNzdWVyL2M3YTVmOTk1LTk3NmUtMTFlZi04NzhkLTBhNThhOWZlYWMwMi9zdGF0dXMvMCMwIiwic3RhdHVzTGlzdENyZWRlbnRpYWwiOiJodHRwczovL3dhbGxldC5kZXYudHJpdmVyaWEuaW8vYXBpL3YxL2lzc3Vlci9jN2E1Zjk5NS05NzZlLTExZWYtODc4ZC0wYTU4YTlmZWFjMDIvc3RhdHVzLzAiLCJzdGF0dXNMaXN0SW5kZXgiOiIwIiwic3RhdHVzUHVycG9zZSI6InJldm9jYXRpb24iLCJ0eXBlIjoiU3RhdHVzTGlzdDIwMjFFbnRyeSJ9LCJjcmVkZW50aWFsU3ViamVjdCI6eyJpZCI6ImRpZDplYnNpOnoyNVpueVc5ZnZpUXJqanJQMXc4RnFHYSIsImlzc3VpbmdBdXRob3JpdHlOYW1lIjoiUlRBTyIsImlzc3VpbmdDb3VudHJ5IjoiU1ZLIiwibGVnYWxGb3JtIjoiSW5jIiwibGVnYWxOYW1lIjoiQUNNRSJ9LCJpZCI6InVybjp1dWlkOmYyMTkxODYzLTFlNzAtNDkzYS05Yjg1LWIyMGMwYzZkNDM5ZCIsImlzc3VhbmNlRGF0ZSI6IjIwMjQtMTAtMzFUMTk6MzY6MDMuNDQ0WiIsImlzc3VlZCI6IjIwMjQtMTAtMzFUMTk6MzY6MDMuNDQ0WiIsImlzc3VlciI6ImRpZDplYnNpOnpvOVY0RkNLOUNpS3VXUU1wdWJNekE0IiwidHlwZSI6WyJWZXJpZmlhYmxlQ3JlZGVudGlhbCIsIlZlcmlmaWFibGVBdHRlc3RhdGlvbiIsIk9yZ2FuaXphdGlvbmFsRGlnaXRhbElkZW50aWZpZXIiXSwidmFsaWRGcm9tIjoiMjAyNC0xMC0zMVQxOTozNjowMy40NDRaIiwidmFsaWRVbnRpbCI6IjIwMjQtMTEtMzBUMTk6MzY6MDMuNDQ0WiJ9fQ.HE46Lyy1EJCVsdW9Mi1OzpbI04awrdWC8m7xFpPHKvikA6ORuGR9I1UMUuqcAKxDy28H7jNOFSbM363xaANvfQ",
      "type": "EnvelopedVerifiableCredential"
    }
  ]
}
```

## PoA Verification

TBD