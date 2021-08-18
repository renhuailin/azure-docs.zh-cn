---
title: “创建自己的密钥”规范 - Azure Key Vault | Microsoft Docs
description: 本文介绍“创建自己的密钥”规范。
services: key-vault
author: mbaldwin
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5dd1e284080f95f961a17e775e8ccf37debc6a62
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114471711"
---
# <a name="bring-your-own-key-specification"></a>“创建自己的密钥”规范

本文介绍将受 HSM 保护的密钥从客户的本地 HSM 导入 Key Vault 的规范。

## <a name="scenario"></a>方案

Key Vault 客户希望在 Azure 外部将密钥安全地从本地 HSM 传输到 HSM 支持的 Azure Key Vault。 导入在 Key Vault 外部生成的密钥的过程通常称为创建自己的密钥 (BYOK)。

要求如下：
* 要传输的密钥永远不会以纯文本形式存在于 HSM 的外部。
* 在 HSM 外部，要传输的密钥始终受 Azure Key Vault HSM 中保存的密钥保护。

## <a name="terminology"></a>术语

|键名|键类型|源|说明|
|---|---|---|---|
|密钥交换密钥 (KEK)|RSA|Azure Key Vault HSM|在 Azure Key Vault 中生成的由 HSM 支持的 RSA 密钥对
包装密钥|AES|供应商 HSM|HSM 在本地上生成的 [临时] AES 密钥
目标密钥|RSA、EC、AES（仅限托管 HSM）|供应商 HSM|要传输到 Azure Key Vault HSM 的密钥

**密钥交换密钥**：客户在将导入 BYOK 密钥的密钥保管库中生成的 HSM 支持的密钥。 此 KEK 必须具有以下属性：

* 它是 RSA-HSM 密钥（4096 位、3072 位或 2048 位）
* 它将具有固定 key_ops（仅限“导入”），将只允许在 BYOK 期间使用
* 必须位于将导入目标密钥的同一保管库中

## <a name="user-steps"></a>用户步骤

要执行密钥传输，用户需要执行以下步骤：

1. 生成 KEK。
2. 检索 KEK 的公钥。
3. 使用 HSM 供应商提供的 BYOK 工具 - 将 KEK 导入目标 HSM，并导出受 KEK 保护的目标密钥。
4. 将受保护的目标密钥导入 Azure Key Vault。

客户使用 HSM 供应商提供的 BYOK 工具和文档完成步骤 3。 会生成一个密钥传输 Blob（“.byok”文件）。


## <a name="hsm-constraints"></a>HSM 约束

现有 HSM 可能会对其管理的密钥应用约束，包括：
* 可能需要将 HSM 配置为允许基于密钥包装的导出
* 可能需要将目标密钥标记为 CKA_EXTRACTABLE，以便 HSM 允许受控导出
* 在某些情况下，可能需要将 KEK 和包装密钥标记为 CKA_TRUSTED。 这允许它用于 HSM 中的包装密钥。

通常，源 HSM 的配置不在此规范的范围内。 Microsoft 期望 HSM 供应商生成 BYOK 工具随附的文档，以包含此类配置步骤。

> [!NOTE]
> 下面所述的步骤 1、2 和 4 可以使用其他接口（如 Azure PowerShell 和 Azure 门户）来执行。 还可以使用 Key Vault SDK 中的等效函数以编程方式执行。

### <a name="step-1-generate-kek"></a>步骤 1：生成 KEK

使用 az keyvault key create 命令创建 KEK，并将密钥操作设置为“导入”。 记下从以下命令返回的密钥标识符 'kid'。

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-retrieve-the-public-key-of-the-kek"></a>步骤 2：检索 KEK 的公钥

下载 KEK 的公钥部分，并将其存储到 PEM 文件中。

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

### <a name="steps-3-generate-key-transfer-blob-using-hsm-vendor-provided-byok-tool"></a>步骤 3：使用 HSM 供应商提供的 BYOK 工具生成密钥传输 blob

客户将使用 HSM 供应商提供的 BYOK 工具创建密钥传输 blob（存储为“.byok”文件）。 KEK 公钥（作为 .pem 文件）将输入此工具。

#### <a name="key-transfer-blob"></a>密钥传输 Blob
长期以来，Microsoft 希望使用 PKCS#11 CKM_RSA_AES_KEY_WRAP 机制将目标密钥传输到 Azure Key Vault，因为此机制生成单个 blob，更重要的是，中间 AES 密钥由两个 HSM 处理，并保证是临时的。 此机制目前在某些 HSM 中不可用，但使用 AES 密钥通过 CKM_AES_KEY_WRAP_PAD 保护目标密钥，结合使用 CKM_RSA_PKCS_OAEP 保护 AES 密钥会产生等效 blob。

目标密钥明文依赖于密钥类型： 
* 对于 RSA 密钥，为包装在 PKCS#8 [RFC5208] 中的私钥 ASN.1 DER 编码 [RFC3447] 
* 对于 EC 密钥，为包装在 PKCS#8 [RFC5208] 中的私钥 ASN.1 DER 编码 [RFC5915]
* 对于八进制密钥，为密钥的原始字节

然后，使用 CKM_RSA_AES_KEY_WRAP 机制来转换明文密钥的字节：
* 将生成临时 AES 密钥，并使用带有 SHA1 的 RSA-OAEP 通过包装 RSA 密钥加密。
* 编码的明文密钥使用 AES 密钥通过使用填充的 AES 密钥包装进行加密。
* 加密的 AES 密钥和加密的明文密钥连接以生成最终的已加密文本 blob。

传输 blob 的格式主要使用 JSON Web 加密紧凑序列化 (RFC7516) 作为一种将所需元数据传递给服务进行正确解密的工具。

如果使用 CKM_RSA_AES_KEY_WRAP_PAD，传输 blob 的 JSON 序列化为：

```json
{
  "schema_version": "1.0.0",
  "header":
  {
    "kid": "<key identifier of the KEK>",
    "alg": "dir",
    "enc": "CKM_RSA_AES_KEY_WRAP"
  },
  "ciphertext":"BASE64URL(<ciphertext contents>)",
  "generator": "BYOK tool name and version; source HSM name and firmware version"
}

```

* kid = KEK 的密钥标识符。 对于 Key Vault 密钥，如下所示： https://ContosoKeyVaultHSM.vault.azure.net/keys/mykek/eba63d27e4e34e028839b53fac905621
* alg = 算法。 
* dir = 直接模式，即被引用的 kid 用于直接保护已加密文本，该文本是 CKM_RSA_AES_KEY_WRAP 的准确表示
* generator = 信息字段，表示 BYOK 工具的名称和版本以及源 HSM 制造商和模型。 此信息可用于故障排除和支持。

JSON blob 存储在扩展名为“.byok”的文件中，以便在使用 ‘Add-AzKeyVaultKey’ (PSH) 或 ‘az keyvault key import’ (CLI) 命令时，Azure PowerShell/CLI 客户端正确处理。

### <a name="step-4-upload-key-transfer-blob-to-import-hsm-key"></a>步骤 4：上传密钥传输 blob 以导入 HSM 密钥

客户会将密钥传输 Blob（“.byok”文件）传输到联机工作站，然后运行 az keyvault key import 命令，将此 Blob 作为新的 HSM 支持的密钥导入 Key Vault 中。 

要导入 RSA 密钥，请使用以下命令：
```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok --ops encrypt decrypt
```
若要导入 EC 密钥，必须指定密钥类型和曲线名称。

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file --kty EC-HSM --curve-name "P-256" KeyTransferPackage-ContosoFirstHSMkey.byok --ops sign verify
```


执行上述命令时，将导致发送 REST API 请求，如下所示：

```
PUT https://contosokeyvaulthsm.vault.azure.net/keys/ContosoFirstHSMKey?api-version=7.0
```

导入 RSA 密钥时的请求正文：
```json
{
  "key": {
    "kty": "RSA-HSM",
    "key_ops": [
      "decrypt",
      "encrypt"
    ],
    "key_hsm": "<Base64 encoded BYOK_BLOB>"
  },
  "attributes": {
    "enabled": true
  }
}
```

导入 EC 密钥时的请求正文：
```json
{
  "key": {
    "kty": "EC-HSM",
    "crv": "P-256",
    "key_ops": [
      "sign",
      "verify"
    ],
    "key_hsm": "<Base64 encoded BYOK_BLOB>"
  },
  "attributes": {
    "enabled": true
  }
}
```

“key_hsm”值是以 Base64 格式编码的 KeyTransferPackage-ContosoFirstHSMkey.byok 的全部内容。

## <a name="references"></a>参考
- [Key Vault 开发人员指南](../general/developers-guide.md)

## <a name="next-steps"></a>后续步骤
* BYOK 分步说明：[将受 HSM 保护的密钥导入 Key Vault (BYOK)](hsm-protected-keys-byok.md)

