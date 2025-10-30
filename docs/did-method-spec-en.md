# SWTC DID Method Specification

## Authors

- [Sheng Mu](mailto:jccdex@jccdex.com), [Sen Mei](mailto:jpassdeveloper@gmail.com)

## Introduction

The SWTC DID Method Specification complies with the requirements of the current [DID Specification](https://www.w3.org/TR/did-1.1/#abstract) published by the W3C Credentials Community Group.

## Abstract

This DID method allows any SWTC address to serve as a valid identifier. Such identifiers do not require registration. If key management or additional attributes (such as "service endpoints") are needed, they are resolved via services deployed on the VDR.

## Method Specific Identifier

The DID method is `swtc` and a DID that uses this method must begin with `did:swtc`.

The identity identifier is base58 encoded `swtc` chain address that is permanent and unique. The [deriveAddress](http://github.com/swtcca/swtclib/blob/master/packages/keypairs/tssrc/keypairs.ts#L401) function can convert any `secp256k1` public key into an SWTC address.

A valid `swtc` DID:

```text
did:swtc:j35Zw6UFMpxiNv5j4JyEnzJ6e18C1eex5h
```

### Overview

The `swtc` DID method uses IPFS as verifiable data registry for DID Documents. The DID Document format is like:

```json
{
  "@context": [
    "https://www.w3.org/ns/did/v1",
    "https://www.w3.org/2018/credentials/v1",
    {
      "version": "https://jdid.cn/did/v1"
    }
  ],
  "id": "did:swtc:j35Zw6UFMpxiNv5j4JyEnzJ6e18C1eex5h",
  "created": "2025-10-28T06:50:40.208Z",
  "updated": "2025-10-28T06:50:40.229Z",
  "version": "1.0.0",
  "authentication": [
    "did:swtc:j35Zw6UFMpxiNv5j4JyEnzJ6e18C1eex5h#key-1"
  ],
  "assertionMethod": [
    "did:swtc:j35Zw6UFMpxiNv5j4JyEnzJ6e18C1eex5h#key-1"
  ],
  "verificationMethod": [
    {
      "id": "did:swtc:j35Zw6UFMpxiNv5j4JyEnzJ6e18C1eex5h#key-1",
      "type": "EcdsaSecp256k1VerificationKey2019",
      "controller": "did:swtc:j35Zw6UFMpxiNv5j4JyEnzJ6e18C1eex5h",
      "publicKeyBase58": "28PPwsFZJUscJo563Aa69SzcwPHuDf7qEacG5JSMH8D4h"
    }
  ],
  "service": [
    {
      "id": "did:swtc:j35Zw6UFMpxiNv5j4JyEnzJ6e18C1eex5h#profile",
      "type": "Profile",
      "serviceEndpoint": {
        "nickname": "Alice",
        "preferredAvatar": "https://example.com/avatar.png"
      }
    },
    {
      "id": "did:swtc:j35Zw6UFMpxiNv5j4JyEnzJ6e18C1eex5h#ipfs-storage",
      "type": "IpfsStorage",
      "serviceEndpoint": {
        "ipns": "ipns://k2k4r8ntjlp1cmgped39eq1fi4yze6fsr8og1kcmjhamgs3ubwkfldei",
        "previousCid": ""
      }
    },
    {
      "id": "did:swtc:j35Zw6UFMpxiNv5j4JyEnzJ6e18C1eex5h#nft-golden-sands-1",
      "standard": "jingtumNFT",
      "tokenName": "Golden Sands",
      "chainId": 315,
      "tokenId": "64656E2053616E647320E98791E6B29900000000000000000000000000000066",
      "status": "Active",
      "credential": {
        "@context": [
          "https://www.w3.org/2018/credentials/v1",
          {
            "version": "https://jdid.cn/did/v1",
            "chainId": "https://jdid.cn/did/v1#chainId",
            "tokenName": "https://jdid.cn/did/v1#tokenName",
            "tokenId": "https://jdid.cn/did/v1#tokenId",
            "owner": "https://jdid.cn/did/v1#owner",
            "status": "https://jdid.cn/did/v1#status"
          }
        ],
        "type": [
          "VerifiableCredential",
          "NFTOwnership"
        ],
        "credentialSubject": {
          "id": "did:swtc:j35Zw6UFMpxiNv5j4JyEnzJ6e18C1eex5h",
          "chainId": 315,
          "tokenName": "Golden Sands",
          "tokenId": "64656E2053616E647320E98791E6B29900000000000000000000000000000066",
          "owner": "j35Zw6UFMpxiNv5j4JyEnzJ6e18C1eex5h",
          "status": "Active"
        },
        "issuanceDate": "2025-10-28T06:50:40.208Z",
        "proof": {
          "type": "EcdsaSecp256k1Signature2019",
          "created": "2025-10-28T06:50:40Z",
          "verificationMethod": "did:swtc:j35Zw6UFMpxiNv5j4JyEnzJ6e18C1eex5h#key-1",
          "proofPurpose": "assertionMethod",
          "jws": "eyJhbGciOiJFUzI1NksiLCJiNjQiOmZhbHNlLCJjcml0IjpbImI2NCJdfQ..MEQCIFRg-QrqHLWbXSOvWUN2nbUMwp00FVhmP_f3Ug5B8ZZvAiAYUNX80YlCanMaPBFO21ccM1pKFALzv7U6Z2RPpDqDWw"
        },
        "issuer": "did:swtc:j35Zw6UFMpxiNv5j4JyEnzJ6e18C1eex5h"
      }
    }
  ]
}
```

## Basic operatios

Any user can create an SWTC DID from a secp256k1 keypair. This process is performed offline. After the DID is created, a DID Document can be generated as needed and published to our IPFS service implementation.

### Create

The creation of a DID follows a few step:

1. Generate a secp256k1 keypair
2. Convert public key from keypair into an SWTC address.

The swtc address is your DID.

The Definition of SWTC JSON_LD Context is:

```json
{
  "@context": [
    "https://www.w3.org/ns/did/v1",
    "https://www.w3.org/2018/credentials/v1",
    {
      "version": "https://jdid.cn/did/v1"
    }
  ],
  "id": "did:swtc:j35Zw6UFMpxiNv5j4JyEnzJ6e18C1eex5h",
  "authentication": [
    "did:swtc:j35Zw6UFMpxiNv5j4JyEnzJ6e18C1eex5h#key-1"
  ],
  "assertionMethod": [
    "did:swtc:j35Zw6UFMpxiNv5j4JyEnzJ6e18C1eex5h#key-1"
  ],
  "verificationMethod": [
    {
      "id": "did:swtc:j35Zw6UFMpxiNv5j4JyEnzJ6e18C1eex5h#key-1",
      "type": "EcdsaSecp256k1VerificationKey2019",
      "controller": "did:swtc:j35Zw6UFMpxiNv5j4JyEnzJ6e18C1eex5h",
      "publicKeyBase58": "28PPwsFZJUscJo563Aa69SzcwPHuDf7qEacG5JSMH8D4h"
    }
  ]
}
```

### DID Registration/Anchoring

DID anchoring means uploading the DID document to IPFS MFS so that the DID document stored on IPFS can be accessed via the DID.

### DID Resolve

DID Document resolution is achieved by querying the IPFS service with a DID. If that DID is registered, a document will be returned. Otherwise throw no link error.

### DID Document Updating

Just upload new DID document to IPFS MFS so that the DID document will be updated.

## Key Management

### Key Recovery

Writing to the IPFS service requires control over the private key used to anchor the DID. **You must securely store private key, if it is lost, you will lose control of the DID.**

## Privacy and Security Considerations

### Key Control

As mentioned in the Key Recovery section, the entity which controls the private key which anchored the DID also effectively controls the DID Document which the DID resolves to. Thus great care should be taken to ensure that the private key is kept private. Methods for ensuring key privacy are outside the scope of this document.

### DID Document Public Profile

The DID Document anchored with the registry contract can contain any content, though it is recommended that it conforms to the [W3C DID Document Specificaiton](https://w3c.github.io/did/). As registered DIDs can be resolved by anyone, care should be taken to only update the registry to resolve to DID Documents which DO NOT expose any sensitive personal information, or information which you may not wish to be public.

### Sensitive Personal Information

Keep personally-identifiable information (PII) off-ledger. We consider that personal information is mostly sensitive content and DID documents may be parsed by others. Therefore, care should be taken to ensure that the DID document does not contain any sensitive personal information. Personal information in the real world is not recommended for inclusion in the document. So others cannot contact someone in the real world by parsing this personal DID document.

## Code Examples

### Create or update

upload did document to ipfs storage.

```javascript
import {
  Secp256k1DidKeypair,
  getKeyDoc,
  DidService,
  SwtcDid,
  SwtcDidDocument,
  SwtcDidPublish,
  SwtcDidResolver,
  SwtcNftVC
} from "@jccdex/did";
import { IpfsClient } from "@jccdex/ipfs-rpc-client";
import { Keypairs } from "@swtc/keypairs";

const client = new IpfsClient({
  baseURL: "https://wodecards.wh.jccdex.cn:8550"
});
const publish = new SwtcDidPublish(client);

// secp256k1 private key
const key = "637674430F5F8736F3F86367F8393E00EF29603B6D82E62B1E4B3AC56F5A6478";
const kp = Keypairs.deriveKeypair(key);
// swtc address
const address = Keypairs.deriveAddress(kp.publicKey);
const ipns = "ipns://k2k4r8ntjlp1cmgped39eq1fi4yze6fsr8og1kcmjhamgs3ubwkfldei";
const did = SwtcDid.fromIdentifier(address);
const keypair = Secp256k1DidKeypair.fromPrivateKey(key);
const id = `${did.toString()}#key-1`;
const keyDoc = getKeyDoc(did.toString(), keypair.keypair(), "", id);

// create a swtc did document
const didDoc = new SwtcDidDocument(did.toString());

// create a profile service
const profile = DidService.generateProfile({
  id: did.toString() + "#profile",
  nickname: "Alice",
  preferredAvatar: "https://example.com/avatar.png"
});

// create a ipfs storage service
const ipfsStorage = DidService.generateIpfsStorage({
  id: did.toString() + "#ipfs-storage",
  ipns,
  previousCid: ""
});

// create a swtc nft verifiable credential
const swtcVC = new SwtcNftVC();
swtcVC.setSubject({
  id: did.toString(),
  chainId: 315,
  tokenName: "Golden Sands",
  tokenId:
    "64656E2053616E647320E98791E6B29900000000000000000000000000000066",
  owner: address,
  status: "Active"
});

// sign swtc nft vc
await swtcVC.sign({
  keyDoc
});

didDoc.setVersion("1.0.0")
  .addAuthentication(id)
  .addAssertionMethod(id)
  .addVerificationMethod({
    id,
    type: keyDoc.type,
    controller: did.toString(),
    publicKeyBase58: keypair.base58PublicKey()
  })
  .addService(profile)
  .addService(ipfsStorage)
  .addService(
    DidService.generateSwtcNft({
      id: did.toString() + "#nft-golden-sands-1",
      standard: "jingtumNFT",
      tokenName: "Golden Sands",
      chainId: 315,
      tokenId:
        "64656E2053616E647320E98791E6B29900000000000000000000000000000066",
      status: "Active",
      credential: swtcVC.toJSON()
    })
  )
  .setUpdated();

// upload did document to ipfs service.
const res = await publish.upload(did.toString(), didDoc, key);
console.log("Publish DID Result:", res);

```

### Read (Resolve)

Resolved from ipfs storage.

```javascript
const resolver = new SwtcDidResolver(client);
const resolved = await resolver.resolve(did.toString());
console.log("Resolved DID Document:", JSON.stringify(resolved, null, 2));

```

### Verify VC

verify a verifiable credential

```javascript
const resolved = await resolver.resolve(did.toString());
const swtcNftService = resolved.service.find((s) => s.credential);

const vc = SwtcNftVC.fromJSON(swtcNftService.credential);
const verifyResult = await vc.verify({
  resolver
});
console.log("VC Verify Result:", JSON.stringify(verifyResult, null, 2));

```
