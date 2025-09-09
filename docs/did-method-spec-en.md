# SWTC DID Method Specification

## Authors

- [jccdex@jccdex.com](jccdex@jccdex.com) and [jpassdeveloper@gmail.com](jpassdeveloper@gmail.com)

## Introduction

The SWTC DID Method Specification complies with the requirements of the current [DID Specification](https://www.w3.org/TR/did-1.1/#abstract) published by the W3C Credentials Community Group.

## Abstract

This DID method allows any SWTC address, key pair account, or secp256k1 public key to serve as a valid identifier. Such identifiers do not require registration. If key management or additional attributes (such as "service endpoints") are needed, they are resolved via services deployed on the VDR.

### Identifier Controller

By default, each identifier is controlled by itself or its corresponding SWTC address. Each identifier can only be controlled by one SWTC address at any time. The controller can replace itself with any other SWTC address.

## Target System

The target system is the SWTC public blockchain network.

### Advantages

- Identifier creation requires no transaction fees
- Identifier creation is private
- Supports multi-signature (or proxy) wallets as account controllers
- Supports secp256k1 public keys as identifiers (based on the same infrastructure)
- Declaration data is decoupled from the underlying identifier
- Flexible key management support
- Supports verifiable version management

## JSON-LD Context Definition

Since this DID method still supports `publicKeyHex` and `publicKeyBase64` encoding for verification methods, a valid JSON-LD context must be provided for these entries.
To enable JSON-LD processing, the DID document for `did:swtc` should use the following `@context`:

```json
"@context": [
  "https://www.w3.org/2018/credentials/v1",
  "https://w3id.org/security/suites/secp256k1-2019/v1"
]
```

## DID Method Name

The name string used to identify this DID method is: `swtc`

DIDs using this method must begin with the prefix `did:swtc`. According to the DID specification, this string must be lowercase. The format of the DID after the prefix is described below.

## Method-Specific Identifier

The method-specific identifier can be a compressed hexadecimal secp256k1 public key (prefixed with `0x`), or an SWTC address on the target network.

## CRUD Operation Definitions

### Create (Register)

To create an `swtc` DID, generate an SWTC address (i.e., a key pair). No interaction with the SWTC network is required at this stage. Registration is implicit. The private key holder is the entity identified by the DID.

The default DID document for `did:swtc:<SWTC address>` on the mainnet is as follows:

```json
{
  "@context": [
    "https://www.w3.org/ns/did/v1",
    "https://w3id.org/security/suites/secp256k1-2019/v1"
  ],
  "id": "did:swtc:j3AC6DTGusdwoEZGS567wa8cawQovo5QkY",
  "verificationMethod": [
    {
      "id": "did:swtc:j3AC6DTGusdwoEZGS567wa8cawQovo5QkY#key-1",
      "type": "EcdsaSecp256k1VerificationKey2019",
      "controller": "did:swtc:j3AC6DTGusdwoEZGS567wa8cawQovo5QkY",
      "publicKeyHex": "0x02b9e...f4c1"
    }
  ],
  "authentication": [
    "did:swtc:j3AC6DTGusdwoEZGS567wa8cawQovo5QkY#key-1"
  ],
  "assertionMethod": [
    "did:swtc:j3AC6DTGusdwoEZGS567wa8cawQovo5QkY#key-1"
  ]
}
```

### Read (Resolve)

Resolved via DID browser service.

#### Controller Address

Each identifier always has one controller address. By default, this address is the same as the identifier address, and resolution must check for signature consistency.

### Update

Data can be updated via the VDR service.

### Delete (Revoke)

Data can be deleted via the VDR service.
