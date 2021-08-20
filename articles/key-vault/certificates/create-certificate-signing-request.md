---
title: 在 Azure Key Vault 中创建和合并 CSR
description: 了解如何在 Azure Key Vault 中创建和合并 CSR。
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: sebansal
ms.openlocfilehash: 77a46beceda451d456dfc4f576f855ebf7312e50
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112463506"
---
# <a name="create-and-merge-a-csr-in-key-vault"></a>在 Key Vault 中创建和合并 CSR

Azure Key Vault 支持存储任何证书颁发机构 (CA) 颁发的数字证书。 它支持使用私钥/公钥对创建证书签名请求 (CSR)。 CSR 可以由任何 CA（内部企业 CA 或外部公共 CA）签名。 CSR 是你发送给 CA 以请求数字证书的消息。

若要详细了解证书的常规信息，请参阅 [Azure Key Vault 证书](./about-certificates.md)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="add-certificates-in-key-vault-issued-by-partnered-cas"></a>在 Key Vault 中添加合作 CA 颁发的证书

Key Vault 与以下证书颁发机构合作，以简化证书的创建。

|提供程序|证书类型|配置设置  
|--------------|----------------------|------------------|  
|DigiCert|Key Vault 提供 DigiCert 的 OV 或 EV SSL 证书| [集成指南](./how-to-integrate-certificate-authority.md)
|GlobalSign|Key Vault 提供 GlobalSign 的 OV 或 EV SSL 证书| [集成指南](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

## <a name="add-certificates-in-key-vault-issued-by-non-partnered-cas"></a>在 Key Vault 中添加非合作 CA 颁发的证书

请按照以下步骤添加来自未与 Key Vault 合作的 CA 的证书。 （例如，GoDaddy 不是受信任的 Key Vault CA。）

# <a name="portal"></a>[门户](#tab/azure-portal)

1. 转到要向其添加证书的密钥保管库。
1. 在“属性”页上，选择“证书”。
1. 选择“生成/导入”选项卡。
1. 在“创建证书”屏幕上，选择以下值：
    - **证书创建方法**：生成。
    - **证书名称**：ContosoManualCSRCertificate。
    - 证书颁发机构 (CA) 类型：非集成 CA 颁发的证书。
    - 使用者：`"CN=www.contosoHRApp.com"`。
     > [!NOTE]
     > 如果使用在值中具有逗号 (,) 的相对可分辨名称 (RDN)，请将包含特殊字符的值括在双引号中。 
     >
     > “使用者”的示例条目：`DC=Contoso,OU="Docs,Contoso",CN=www.contosoHRApp.com`
     >
     > 在本示例中，RDN `OU` 包含在名称中有逗号的值。 `OU` 生成的输出是“Docs, Contoso”。
1. 根据需要选择“其他值”，然后选择“创建”，将证书添加到“证书”列表 。

    ![证书属性的屏幕截图](../media/certificates/create-csr-merge-csr/create-certificate.png)  

1. 在“证书”列表中，选择该新证书。 证书的当前状态为“已禁用”，因为它尚未由 CA 颁发。
1. 在“证书操作”选项卡上，选择“下载 CSR” 。

   ![突出显示“下载 CSR”按钮的屏幕截图。](../media/certificates/create-csr-merge-csr/download-csr.png)

1. 让 CA 为 CSR (.csr) 签名。
1. 对请求签名后，在“证书操作”选项卡上选择“合并已签名的请求”，以将已签名的证书添加到 Key Vault 。

现已成功合并证书请求。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 创建证书策略。 由于在此场景中选择的 CA 是非合作 CA，因此“IssuerName”设置为“未知”，Key Vault 不会注册或续订证书 。

   ```azure-powershell
   $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
   ```
     > [!NOTE]
     > 如果使用在值中具有逗号 (,) 的相对可分辨名称 (RDN)，请对全值或值集使用单引号，并使用单引号将并将包含特殊字符的值括在双引号中。 
     >
     >“SubjectName”的示例条目：`$policy = New-AzKeyVaultCertificatePolicy -SubjectName 'OU="Docs,Contoso",DC=Contoso,CN=www.contosoHRApp.com' -ValidityInMonths 1  -IssuerName Unknown` 在此示例中，`OU` 值作为“Docs, Contoso”读取。 此格式适用于包含逗号的所有值。
     > 
     > 在本示例中，RDN `OU` 包含在名称中有逗号的值。 `OU` 生成的输出是“Docs, Contoso”。

1. 创建 CSR。

   ```azure-powershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

1. 让 CA 为 CSR 签名。 `$csr.CertificateSigningRequest` 是证书的基本编码 CSR。 可以将此 blob 转储到证书颁发者的证书请求网站中。 此步骤因 CA 而异。 了解 CA 有关如何执行此步骤的指南。 此外，还可以使用 certreq 或 openssl 之类的工具来对 CSR 进行签名，并完成证书生成过程。

1. 在 Key Vault 中合并已签名的请求。 对证书请求签名后，可以将其与在 Azure Key Vault 中创建的初始私钥/公钥对合并。

    ```azure-powershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

现已成功合并证书请求。

---

## <a name="add-more-information-to-the-csr"></a>向 CSR 添加更多信息

若要在创建 CSR 时添加更多信息，请在“SubjectName”中定义它。 你可能希望添加以下信息：
- 国家/地区
- 城市/区域
- 省/自治区/直辖市
- 组织
- 组织单位

示例

   ```azure-powershell
   SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
   ```

> [!NOTE]
> 如果你请求的是带有其他信息的域验证 (DV) 证书，则 CA 如果无法验证请求中的所有信息，则可能会拒绝该请求。 如果你请求组织验证 (OV) 证书，则这些附加信息可能更合适。

## <a name="faqs"></a>常见问题解答

- 如何监视或管理我的 CSR？

     请参阅[监视和管理证书创建](./create-certificate-scenarios.md)。

- 如果出现此情况该怎么办 - 错误类型“指定的 X.509 证书内容中最终实体证书的公钥与指定私钥的公共部分不一致。请检查证书是否有效”？

     如果未将已签名的 CSR 与启动的同一 CSR 请求合并，则会发生此错误。 创建的每个新 CSR 都有一个私钥，当你合并已签名的请求时，该私钥必须匹配。

- 当 CSR 合并时，它会合并整个链吗？

     是的，它会合并整个链，前提是用户返回 p7b 文件进行合并。

- 如果颁发的证书在 Azure 门户中处于禁用状态怎么办？

     查看“证书操作”选项卡以查看该证书的错误消息。

- 如果出现此情况该怎么办 - 错误类型“提供的使用者名称不是有效的 X500 名称”？

     如果“SubjectName”包含任何特殊字符，则可能发生此错误。 请参阅 Azure 门户和 PowerShell 说明中的注释。

- 错误类型“已使用用于获取证书的 CSR。请尝试使用新的 CSR 生成新证书”。
     转到证书的“高级策略”部分，检查是否关闭了“续订时重用密钥”选项。
---

## <a name="next-steps"></a>后续步骤

- [身份验证、请求和响应](../general/authentication-requests-and-responses.md)
- [Key Vault 开发人员指南](../general/developers-guide.md)
- [Azure Key Vault REST API 参考](/rest/api/keyvault)
- [保管库 - 创建或更新](/rest/api/keyvault/vaults/createorupdate)
- [保管库 - 更新访问策略](/rest/api/keyvault/vaults/updateaccesspolicy)
