# SWTC DID方法规范

## 作者

- [沐生](mailto:jccdex@jccdex.com), [梅森](mailto:jpassdeveloper@gmail.com)

## 前言

SWTC DID方法规范符合W3C凭证社区组当前发布的[DID规范](https://www.w3.org/TR/did-1.1/#abstract)的要求。

该DID方法允许任何SWTC地址成为有效标识符。此类标识符无需注册。如果需要密钥管理或附加属性（如"服务端点"），则通过部署在VDR服务进行解析。

## 方法特定标识符

该 DID 方法为 `swtc`，使用此方法的 DID 必须以 `did:swtc` 开头。

身份标识符是经 base58 编码的 `swtc` 链地址，该地址是永久且唯一的。[deriveAddress](http://github.com/swtcca/swtclib/blob/master/packages/keypairs/tssrc/keypairs.ts#L401) 函数可以将任何 `secp256k1` 公钥转换为 SWTC 地址。

一个有效的 `swtc` DID：

```text
did:swtc:j35Zw6UFMpxiNv5j4JyEnzJ6e18C1eex5h
```

## 基本操作

任何用户都可以从 secp256k1 密钥对创建一个 SWTC DID。该过程可离线完成。创建 DID 后，可按需生成 DID 文档，并将其发布到我们实现的 IPFS 服务。

### 创建

创建 DID 的步骤如下：

1. 生成一个 secp256k1 密钥对
2. 将密钥对中的公钥转换为 SWTC 地址。

该 SWTC 地址即为你的 DID。

SWTC JSON-LD 上下文定义如下：

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

### DID 注册/锚定

DID 锚定是指将 DID 文档上传到 IPFS MFS，使得存储在 IPFS 上的 DID 文档可以通过该 DID 进行访问。

### DID 解析

DID 文档的解析是通过向 IPFS 服务以 DID 作为查询进行检索来实现的。如果该 DID 已注册，则会返回一个文档；否则抛出"无链接（no link）"错误。

### DID 文档更新

只需将新的 DID 文档上传到 IPFS MFS，DID 文档即可被更新。

### DID 文档删除

目前不支持`DELETE`方法。你可以通过写入空数据（即用空载荷覆盖该项）来实现删除的效果。

## 密钥管理

### 密钥恢复

向 IPFS 服务写入（上传）需要对用于锚定 DID 的私钥拥有控制权。**你必须妥善保存私钥；如果私钥丢失，你将失去对该 DID 的控制权。**

## 隐私与安全注意事项

### 密钥控制

如"密钥恢复"部分所述，控制用于锚定 DID 的私钥的实体，也就实际控制了该 DID 所解析到的 DID 文档。因此应非常谨慎地确保私钥保密。保证密钥隐私的方法超出本文件范围。

### DID 文档公开资料

通过注册合约锚定的 DID 文档可以包含任意内容，但建议其遵循 [W3C DID 文档规范](https://w3c.github.io/did/)。由于已注册的 DID 可以被任何人解析，因此在更新注册项以指向某个 DID 文档时，应注意不要在文档中暴露任何敏感的个人信息或你不希望公开的信息。

### 敏感个人信息

请将可识别个人身份的信息（PII）保存在链外。我们认为个人信息通常属于敏感内容，且 DID 文档可能会被他人解析。因此应避免在 DID 文档中包含敏感个人信息。现实世界中的个人信息不建议包含在文档中，以避免他人通过解析该个人 DID 文档联系到现实中的某人。

## 代码示例

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
