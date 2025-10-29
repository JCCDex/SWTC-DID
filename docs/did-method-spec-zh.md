# SWTC DID方法规范

## 作者

- [JCCDex](jccdex@jccdex.com), [JPassword](jpassdeveloper@gmail.com), [Sen Mei](jpassdeveloper@gmail.com) and [Sheng Mu](gin.musheng@gmail.com)

## 前言

SWTC DID方法规范符合W3C凭证社区组当前发布的[DID规范](https://www.w3.org/TR/did-1.1/#abstract)的要求。

## 摘要

该DID方法允许任何SWTC地址成为有效标识符。此类标识符无需注册。如果需要密钥管理或附加属性（如“服务端点”），则通过部署在VDR服务进行解析。

### 概览

`swtc` DID 方法使用IPFS作为DID文档的可验证数据存储。DID文档的格式示例如下：

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

## CRUD操作示例

### 创建或更新did文档

上传did文档到ipfs服务。

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

### 读取did文档

从ipfs服务读取did文档。

```javascript
const resolver = new SwtcDidResolver(client);
const resolved = await resolver.resolve(did.toString());
console.log("Resolved DID Document:", JSON.stringify(resolved, null, 2));

```

### 验证VC

验证可验证凭证。

```javascript
const resolved = await resolver.resolve(did.toString());
const swtcNftService = resolved.service.find((s) => s.credential);

const vc = SwtcNftVC.fromJSON(swtcNftService.credential);
const verifyResult = await vc.verify({
  resolver
});
console.log("VC Verify Result:", JSON.stringify(verifyResult, null, 2));

```
