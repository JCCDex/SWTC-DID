# SWTC DID方法规范

## 作者

- [jccdex@jccdex.com](jccdex@jccdex.com) 和 [jpassdeveloper@gmail.com](jpassdeveloper@gmail.com)

## 前言

SWTC DID方法规范符合W3C凭证社区组当前发布的[DID规范](https://www.w3.org/TR/did-1.1/#abstract)的要求。

## 摘要

该DID方法允许任何SWTC地址、密钥对账户或secp256k1公钥成为有效标识符。此类标识符无需注册。如果需要密钥管理或附加属性（如“服务端点”），则通过部署在VDR服务进行解析。

### 标识符控制者

默认情况下，每个标识符由其自身或其对应的SWTC地址控制。每个标识符在任意时刻只能由一个SWTC地址控制。控制者可以将自身替换为任何其他SWTC地址。

## 目标系统

目标系统是SWTC公链网络。

### 优势

- 标识符创建无需交易费用
- 标识符创建是私密的
- 支持多签名（或代理）钱包作为账户控制者
- 支持secp256k1公钥作为标识符（基于同一基础设施）
- 声明数据与底层标识符解耦
- 灵活支持密钥管理
- 支持可验证的版本管理

## JSON-LD上下文定义

由于该DID方法仍支持用于验证方法的`publicKeyHex`和`publicKeyBase64`编码，因此需要为这些条目提供有效的JSON-LD上下文。
要启用JSON-LD处理，构建`did:swtc`的DID文档时应使用如下`@context`：

```json
"@context": [
  "https://www.w3.org/2018/credentials/v1",
  "https://w3id.org/security/suites/secp256k1-2019/v1"
]
```

## DID方法名称

用于标识此DID方法的名称字符串为：`swtc`

使用此方法的DID必须以前缀`did:swtc`开头。根据DID规范，此字符串必须为小写。前缀后的DID格式如下所述。

## 方法特定标识符

方法特定标识符可以是压缩格式的十六进制secp256k1公钥（以`0x`为前缀），或目标网络上的SWTC地址。

## CRUD操作定义

### 创建（注册）

要创建`swtc` DID，需要生成一个SWTC地址（即密钥对）。此时无需与SWTC网络交互。注册是隐式的。私钥持有者即为DID所标识的实体。

主网上`did:swtc:<SWTC address>`的默认DID文档如下：

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

### 读取（解析）

通过DID浏览器服务解析。

#### 控制者地址

每个标识符始终有一个控制者地址。默认情况下，该地址与标识符地址相同，解析必须检查签名是否一致。

### 更新

可通过VDR服务进行数据更新。

### 删除（撤销）

可通过VDR服务进行数据删除。
