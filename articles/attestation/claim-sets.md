---
title: Azure 证明- 声明集
description: Azure 证明的声明集。
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: afe2cf288cd4a15091e8278309b3ecf74a2d35a4
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2021
ms.locfileid: "98572742"
---
# <a name="claim-sets"></a>声明集

在证明 enclave 的过程中使用 Microsoft Azure 证明生成的声明可以分为以下几类：

- **传入声明**：分析证明证据后由 Microsoft Azure 证明生成的声明，策略作者可使用这些声明定义自定义策略中的授权规则

- **传出声明**：Azure 证明生成的声明，包含最终出现在证明令牌中的所有声明

- **属性声明**：作为 Azure 证明的输出创建的声明。 它包含表示证明令牌的属性的所有声明，如报表的编码、报表的有效持续时间等。

### <a name="common-incoming-claims-across-all-attestation-types"></a>所有证明类型的共同传入声明

以下声明由 Azure 证明生成，可用于定义自定义策略中的授权规则：
- **x-ms-ver**：JWT 架构版本（应为“1.0”）
- **x-ms-attestation-type**：表示证明类型的字符串值 
- **x-ms-policy-hash**：Azure 证明计算策略的哈希，计算结果为 BASE64URL(SHA256(UTF8(BASE64URL(UTF8(policy text)))))
- **x-ms-policy-signer**：具有“jwk”成员的 JSON 对象，表示客户上传已签名策略时用于签署其策略的密钥

以下声明被视为已弃用，但受完全支持。 建议使用未弃用的声明名称。

已弃用的声明 | 建议的声明 
--- | --- 
ver | x-ms-ver
tee | x-ms-attestation-type
maa-policyHash | x-ms-policy-hash
policy_hash | x-ms-policy-hash
policy_signer | x-ms-policy-signer

### <a name="common-outgoing-claims-across-all-attestation-types"></a>所有证明类型的共同传出声明

以下声明由 [IETF JWT](https://tools.ietf.org/html/rfc7519) 定义，并在响应对象中由 Azure 证明使用：

- **“jti”(JWT ID) 声明**
- **“iss”（证书颁发者）声明**
- **“iat”（颁发时间）声明**
- **“exp”（到期时间）声明**
- **“nbf”（不早于）声明**

以下声明由 [IETF EAT](https://tools.ietf.org/html/draft-ietf-rats-eat-03#page-9) 定义，并在响应对象中由 Azure 证明使用：
- **“Nonce 声明”(nonce)**

## <a name="claims-specific-to-sgx-enclaves"></a>特定于 SGX enclave 的声明

### <a name="incoming-claims-specific-to-sgx-attestation"></a>特定于 SGX 证明的传入声明

以下声明由 SGX 证明的服务生成，可用于定义自定义策略中的授权规则：
- **x-ms-sgx-is-debuggable**：布尔值，指示 enclave 是否已启用调试
- **x-ms-sgx-product-id**
- **x-ms-sgx-mrsigner**：Quote 的“mrsigner”字段的十六进制编码值
- **x-ms-sgx-mrenclave**：Quote 的“mrenclave”字段的十六进制编码值
- **x-ms-sgx-svn**：在 Quote 中编码的安全版本号 

### <a name="outgoing-claims-specific-to-sgx-attestation"></a>特定于 SGX 证明的传出声明

以下声明由服务生成并包含在 SGX 证明的响应对象中：
- **x-ms-sgx-is-debuggable**：布尔值，指示 enclave 是否已启用调试
- **x-ms-sgx-product-id**
- **x-ms-ver**
- **x-ms-sgx-mrsigner**：Quote 的“mrsigner”字段的十六进制编码值
- **x-ms-sgx-mrenclave**：Quote 的“mrenclave”字段的十六进制编码值
- **x-ms-sgx-svn**：在 Quote 中编码的安全版本号 
- **x-ms-sgx-ehd**：格式为 BASE64URL(enclave held data) 的 Enclave Held Data
- **x-ms-sgx-collateral**：JSON 对象，描述用于执行证明的附件。 x-ms-sgx-collateral 声明的值是具有以下键/值对的嵌套 JSON 对象：
    - **qeidcertshash**：QE 标识颁发证书的 SHA256 值
    - **qeidcrlhash**：QE 标识颁发证书 CRL 列表的 SHA256 值
    - **qeidhash**：QE 标识附件的 SHA256 值
    - **quotehash**：计算出的 Quote 的 SHA256 值
    - **tcbinfocertshash**：TCB 信息颁发证书的 SHA256 值
    - **tcbinfocrlhash**：TCB 信息颁发证书 CRL 列表的 SHA256 值
    - **tcbinfohash**：JSON 对象，描述用于执行证明的附件

以下声明被视为已弃用，但受完全支持，并且将来将继续被包含在内。 建议使用未弃用的声明名称。

已弃用的声明 | 建议的声明
--- | --- 
$is-debuggable | x-ms-sgx-is-debuggable
$sgx-mrsigner | x-ms-sgx-mrsigner
$sgx-mrenclave | x-ms-sgx-mrenclave
$product-id | x-ms-sgx-product-id
$svn | x-ms-sgx-svn
$tee | x-ms-attestation-type
maa-ehd | x-ms-sgx-ehd
aas-ehd | x-ms-sgx-ehd
maa-attestationcollateral | x-ms-sgx-collateral

## <a name="claims-issued-specific-to-trusted-platform-module-tpm-attestation"></a>颁发的声明（特定于受信任的平台模块 (TPM) 证明）

### <a name="incoming-claims-can-also-be-used-as-outgoing-claims"></a>传入声明（也可用作传出声明）

- **aikValidated**：布尔值，包含证明标识密钥 (AIK) 证书是否已经过验证的信息。
- **aikPubHash**：包含 base64(SHA256(DER 格式的 AIK 公钥)) 的字符串。
- **tpmVersion**： 包含受信任的平台模块 (TPM) 主版本的整数值。
- **secureBootEnabled**：布尔值，指示是否启用安全启动。
- **iommuEnabled**：布尔值，指示是否启用输入输出内存管理单元 (Iommu)。
- **bootDebuggingDisabled**：布尔值，指示是否禁用启动调试。
- **notSafeMode**：布尔值，指示 Windows 是否未在安全模式下运行。
- **notWinPE**：布尔值，指示 Windows 是否未在 WinPE 模式下运行。
- **vbsEnabled**：布尔值，指示是否启用 VBS。
- **vbsReportPresent**：布尔值，指示 VBS enclave 报表是否可用。
- **enclaveAuthorId**：包含 enclave 创作者 ID 的 Base64Url 编码值的字符串值 - enclave 主模块的创作者标识符。
- **enclaveImageId**：包含 enclave 映像 ID 的 Base64Url 编码值的字符串值 - enclave 主模块的映像标识符。
- **enclaveOwnerId**：包含 enclave 所有者 ID 的 Base64Url 编码值的字符串值 - enclave 的所有者标识符。
- **enclaveFamilyId**：包含 enclave 系列 ID 的 Base64Url 编码值的字符串值。 Enclave 的主模块的系列标识符。
- **enclaveSvn**：包含 enclave 主模块的安全版本号的整数值。
- **enclavePlatformSvn**：包含承载 enclave 的平台的安全版本号的整数值。
- **enclaveFlags**：EnclaveFlags 声明是一个整数值，其中包含用于描述 enclave 的运行时策略的标志。
  
### <a name="outgoing-claims-specific-to-tpm-attestation"></a>特定于 TPM 证明的传出声明

- **policy_hash**：字符串值，其中包含由 BASE64URL(SHA256(BASE64URL(UTF8(Policy text)))) 计算的策略文本的 SHA256 哈希值。
- **policy_signer**：包含具有公钥的 JWK，或签名策略标头中存在的证书链。
- **ver（版本）** :包含报表说明的字符串值。 当前为 1.0。
- **cnf（确认）Claim**：“Cnf”声明用于指定所有权证明密钥。 RFC 7800 中定义的确认声明包含表示为 JSON Web 密钥 (JWK) 对象 (RFC 7517) 的证明 enclave 密钥的公共部分。
- **rp_data（信赖方数据）** ：在请求中指定的信赖方数据（如有），由信赖方用作 nonce，以保证报表的新鲜度。
- **“jti”(JWT ID) 声明**：“jti”(JWT ID) 声明为 JWT 提供唯一标识符。 标识符值的赋值方式可确保几乎不会将相同的值意外分配给不同的数据对象。

### <a name="property-claims"></a>属性声明

- **report_validity_in_minutes**：表示令牌有效时间的整数声明。
  - **默认值（时间）** ：一天，以分钟为单位。
  - **最大值（时间）** ：一年，以分钟为单位。
- **omit_x5c**：布尔声明，指示 Azure 证明是否应忽略用于证明服务真实性的证书。 如果为 true，则 x5t 将添加到证明令牌。 如果为 false（默认值），则 x5c 将添加到证明令牌。

## <a name="next-steps"></a>后续步骤
- [如何创作证明策略并对其签名](author-sign-policy.md)
- [使用 PowerShell 设置 Azure 证明](quickstart-powershell.md)
