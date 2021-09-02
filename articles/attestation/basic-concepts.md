---
title: Azure 证明基本概念
description: Azure 证明的基本概念。
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.custom: references_regions
ms.openlocfilehash: 5ac431f489f279ea09407e1a34c234911d71f12f
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123259739"
---
# <a name="basic-concepts"></a>基本概念

下面是一些与 Microsoft Azure 证明相关的基本概念。

## <a name="json-web-token-jwt"></a>JSON Web 令牌 (JWT)

[JSON Web 令牌](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims) (JWT) 是一个开放的标准 [RFC7519](https://tools.ietf.org/html/rfc7519) 方法，用于在各方之间作为 JavaScript 对象标记 (JSON) 对象安全地传输信息。 可以验证和信任此信息，因为该信息已进行数字签名。 可以使用机密或公钥/私钥对对 JWT 进行签名。

## <a name="json-web-key-jwk"></a>JSON Web 密钥 (JWK)

[JSON Web 密钥](https://tools.ietf.org/html/rfc7517) (JWK) 是一个表示加密密钥的 JSON 数据结构。 此规范还定义了一个 JWK Set JSON 数据结构，该结构表示一组 JWK。

## <a name="attestation-provider"></a>证明提供程序

证明提供程序属于名为 Microsoft.Attestation 的 Azure 资源提供程序。 资源提供程序是提供 Azure 证明 REST 协定的服务终结点，并使用 [Azure 资源管理器](../azure-resource-manager/management/overview.md)进行部署。 每个证明提供程序都遵循特定的可发现策略。 证明提供程序是使用每种证明类型的默认策略创建的（请注意，VBS enclave 没有默认策略）。 有关 SGX 的默认策略的更多详细信息，请参阅[证明策略的示例](policy-examples.md)。

### <a name="regional-shared-provider"></a>区域共享提供程序

Azure 证明在每个可用区域中提供了一个区域共享提供程序。 客户可选择使用区域共享提供程序进行证明，也可使用自定义策略创建自己的提供程序。 任何 Azure AD 用户都可访问共享提供程序，并且不能更改与这类提供程序关联的策略。

| 区域 | 证明 Uri | 
|--|--|
| 美国东部 | `https://sharedeus.eus.attest.azure.net` | 
| 美国西部 | `https://sharedwus.wus.attest.azure.net` | 
| 英国南部 | `https://shareduks.uks.attest.azure.net` | 
| 英国西部| `https://sharedukw.ukw.attest.azure.net  ` | 
| 加拿大东部 | `https://sharedcae.cae.attest.azure.net` | 
| 加拿大中部 | `https://sharedcac.cac.attest.azure.net` | 
| 北欧 | `https://sharedneu.neu.attest.azure.net` | 
| 西欧| `https://sharedweu.weu.attest.azure.net` | 
| 美国东部 2 | `https://sharedeus2.eus2.attest.azure.net` | 
| 美国中部 | `https://sharedcus.cus.attest.azure.net` | 

## <a name="attestation-request"></a>证明请求

证明请求是由客户端应用程序发送到证明提供程序的序列化 JSON 对象。 SGX enclave 的请求对象有两个属性： 
- “Quote”-“Quote”属性的值是一个字符串，包含经 Base64URL 编码的证明引用的表示形式
- “EnclaveHeldData”-“EnclaveHeldData”属性的值是一个字符串，包含经 Base64URL 编码的 Enclave Held Data 的表示形式。

Azure 证明将验证提供的“Quote”，然后确保所提供的 Enclave Held Data 的 SHA256 哈希以引用中 reportData 字段的前 32 个字节表示。 

## <a name="attestation-policy"></a>证明策略

证明策略用于处理证明证据，并可由客户进行配置。 Azure 证明的核心是一个策略引擎，可处理构成证据的声明。 策略用于确定 Azure 证明是否应基于证据（或不基于证据）颁发证明令牌，从而对该证明者予以认可（或不认可）。 因此，如果未通过所有策略，将不颁发 JWT 令牌。

如果证明提供程序中的默认策略不能满足需要，客户将能够在 Azure 证明支持的任何区域创建自定义策略。 策略管理是 Azure 证明向客户提供的一项重要功能。 策略特定于证明类型，可用于标识 enclave，或将声明添加到输出令牌或修改输出令牌中的声明。 

有关策略示例，请参阅[证明策略的示例](policy-examples.md)。

## <a name="benefits-of-policy-signing"></a>策略签名的优点

证明策略最终确定是否将由 Azure 证明颁发证明令牌。 策略还确定要在证明令牌中生成的声明。 因此，至关重要的是，由服务评估的策略实际上是管理员编写的策略，且未经外部实体篡改或修改。 

信任模型定义了用于定义和更新策略的证明提供程序的授权模型。  支持两种模型：一种模型基于 Azure AD 授权，一种基于拥有客户管理的加密密钥（称为独立的模型）。  独立模型将启用 Azure 证明，以确保客户提交的策略不会被篡改。

在独立的模型中，管理员创建一个证明提供程序，在文件中指定一组受信任签名 X.509 证书。 然后，管理员可以将签名的策略添加到证明提供程序。 处理证明请求时，Azure 证明将使用标头中“jwk”或“x5c”参数表示的公钥来验证策略的签名。  Azure 证明还将验证请求头中的公钥是否位于与证明提供程序关联的受信任签名证书的列表中。 通过这种方式，信赖方（Azure 证明）就可以信任使用它知道的 X.509 证书签名的策略。 

有关示例，请参阅[策略签名者证书的示例](policy-signer-examples.md)。

## <a name="attestation-token"></a>证明令牌

Azure 证明响应将是一个 JSON 字符串，其值包含 JWT。 Azure 证明将打包声明并生成签名的 JWT。 使用主题名称与证明提供程序的 AttestUri 元素匹配的自签名证书执行签名操作。

Get OpenID Metadata API 返回 [OpenID Connect 发现协议](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig)指定的 OpenID Configuration 响应。 此 API 检索有关 Azure 证明正在使用的签名证书的元数据。

为 SGX enclave 生成的 JWT 示例：

```
{
  "alg": "RS256",
  "jku": "https://tradewinds.us.attest.azure.net/certs",
  "kid": <self signed certificate reference to perform signature verification of attestation token,
  "typ": "JWT"
}.{
  "aas-ehd": <input enclave held data>,
  "exp": 1568187398,
  "iat": 1568158598,
  "is-debuggable": false,
  "iss": "https://tradewinds.us.attest.azure.net",
  "maa-attestationcollateral": 
    {
      "qeidcertshash": <SHA256 value of QE Identity issuing certs>,
      "qeidcrlhash": <SHA256 value of QE Identity issuing certs CRL list>,
      "qeidhash": <SHA256 value of the QE Identity collateral>,
      "quotehash": <SHA256 value of the evaluated quote>, 
      "tcbinfocertshash": <SHA256 value of the TCB Info issuing certs>, 
      "tcbinfocrlhash": <SHA256 value of the TCB Info issuing certs CRL list>, 
      "tcbinfohash": <SHA256 value of the TCB Info collateral>
     },
  "maa-ehd": <input enclave held data>,
  "nbf": 1568158598,
  "product-id": 4639,
  "sgx-mrenclave": <SGX enclave mrenclave value>,
  "sgx-mrsigner": <SGX enclave msrigner value>,
  "svn": 0,
  "tee": "sgx"
  "x-ms-attestation-type": "sgx", 
  "x-ms-policy-hash": <>,
  "x-ms-sgx-collateral": 
    {
      "qeidcertshash": <SHA256 value of QE Identity issuing certs>,
      "qeidcrlhash": <SHA256 value of QE Identity issuing certs CRL list>,
      "qeidhash": <SHA256 value of the QE Identity collateral>,
      "quotehash": <SHA256 value of the evaluated quote>, 
      "tcbinfocertshash": <SHA256 value of the TCB Info issuing certs>, 
      "tcbinfocrlhash": <SHA256 value of the TCB Info issuing certs CRL list>, 
      "tcbinfohash": <SHA256 value of the TCB Info collateral>
     },
  "x-ms-sgx-ehd": <>, 
  "x-ms-sgx-is-debuggable": true,
  "x-ms-sgx-mrenclave": <SGX enclave mrenclave value>,
  "x-ms-sgx-mrsigner": <SGX enclave msrigner value>, 
  "x-ms-sgx-product-id": 1, 
  "x-ms-sgx-svn": 1,
  "x-ms-ver": "1.0",
  "x-ms-sgx-config-id": "000102030405060708090a0b0c0d8f99000102030405060708090a0b0c860e9a000102030405060708090a0b7d0d0e9b000102030405060708090a740c0d0e9c",
  "x-ms-sgx-config-svn": 3451,
  "x-ms-sgx-isv-extended-product-id": "8765432143211234abcdabcdef123456",
  "x-ms-sgx-isv-family-id": "1234567812344321abcd1234567890ab"
}.[Signature]
```

上面使用的某些声明被视为已弃用，但完全受支持。  建议将来所有的代码和工具都使用未弃用的声明名称。 有关详细信息，请参阅[由 Azure 证明颁发的声明](claim-sets.md)。

以下声明将仅出现在为基于 Intel® Xeon® 可缩放处理器的服务器平台生成的证明令牌中。 如果未为 SGX enclave 配置[密钥分离和共享支持](https://github.com/openenclave/openenclave/issues/3054)，则不会出现这些声明

**x-ms-sgx-config-id**

**x-ms-sgx-config-svn**

**x-ms-sgx-isv-extended-product-id**

**x-ms-sgx-isv-family-id**

## <a name="encryption-of-data-at-rest"></a>静态数据加密

为了保护客户数据，Azure 证明在 Azure 存储中保留其数据。 Azure 存储在静态数据写入数据中心时对其进行加密，并对其进行解密以供客户访问。 使用 Microsoft 管理的加密密钥进行加密。 

除了保护 Azure 存储中的数据外，Azure 证明还利用 Azure 磁盘加密 (ADE) 对服务 VM 进行加密。 对于在 Azure 机密计算环境的 enclave 中运行的 Azure 证明，当前不支持 ADE 扩展。 在这种情况下，为了防止数据存储在内存中，将禁用页面文件。 

在 Azure 证明实例本地硬盘驱动器上不会保留任何客户数据。


## <a name="next-steps"></a>后续步骤

- [如何创作证明策略并对其签名](author-sign-policy.md)
- [使用 PowerShell 设置 Azure 证明](quickstart-powershell.md)
