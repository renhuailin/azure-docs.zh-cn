---
title: 将密钥保管库与 DigiCert 证书颁发机构集成
description: 本文介绍如何将密钥保管库与 DigiCert 证书颁发机构集成，以便为网络预配、管理和部署证书。
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 06/02/2020
ms.author: sebansal
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9cec61df061c7a504da412851349064273507173
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110678849"
---
# <a name="integrating-key-vault-with-digicert-certificate-authority"></a>将密钥保管库与 DigiCert 证书颁发机构集成

Azure Key Vault 使你能轻松地为网络预配、管理和部署数字证书，并支持应用程序的安全通信。 数字证书是一种电子凭据，用于在电子交易中建立身份证明。 

Azure 密钥保管库用户可以直接从其密钥保管库生成 DigiCert 证书。 密钥保管库与 DigiCert 证书颁发机构之间存在受信任的合作关系。 这种合作关系可确保 DigiCert 颁发的证书的端到端证书生命周期管理。

若要详细了解证书的常规信息，请参阅 [Azure Key Vault 证书](./about-certificates.md)。

如果没有 Azure 订阅，可以在开始之前[创建一个免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

若要完成本文中的过程，你需要拥有：
* 一个 Key Vault。 可以使用现有的密钥保管库，或者遵循以下快速入门之一中的步骤进行创建：
   - [使用 Azure CLI 创建密钥保管库](../general/quick-create-cli.md)
   - [使用 Azure PowerShell 创建密钥保管库](../general/quick-create-powershell.md)
   - [使用 Azure 门户创建密钥保管库](../general/quick-create-portal.md)
*   已激活的 DigiCert CertCentral 帐户。 [注册](https://www.digicert.com/account/signup/) CertCentral 帐户。
*   帐户中的管理员级别权限。


### <a name="before-you-begin"></a>准备阶段

确保你的 DigiCert CertCentral 帐户提供以下信息：
-   CertCentral 帐户 ID
-   组织 ID
-   API 密钥

## <a name="add-the-certificate-authority-in-key-vault"></a>在密钥保管库中添加证书颁发机构 
从 DigiCert CertCentral 帐户收集上述信息后，可以将 DigiCert 添加到密钥保管库中的证书颁发机构列表。

### <a name="azure-portal"></a>Azure 门户

1.  若要添加 DigiCert 证书颁发机构，请转到要将其添加到的密钥保管库。 
2.  在密钥保管库属性页中，选择“证书”。
3.  选择“证书颁发机构”选项卡：:::image type="content" source="../media/certificates/how-to-integrate-certificate-authority/select-certificate-authorities.png" alt-text="屏幕截图显示正在选择“证书颁发机构”选项卡。":::
4.  选择“添加”：:::image type="content" source="../media/certificates/how-to-integrate-certificate-authority/add-certificate-authority.png" alt-text="屏幕截图显示了“证书颁发机构”选项卡上的“添加”按钮。":::
5.  在“创建证书颁发机构”下，输入以下值：
    -   名称：可识别的颁发者名称。 例如 DigicertCA。
    -   提供商：DigiCert。 
    -   帐户 ID：你的 DigiCert CertCentral 帐户 ID。
    -   帐户密码：你在 DigiCert CertCentral 帐户中生成的 API 密钥。
    -   组织 ID：DigiCert CertCentral 帐户中的组织 ID。 

1. 选择“创建”。
   
DigicertCA 现在位于证书颁发机构列表中。


### <a name="azure-powershell"></a>Azure PowerShell

可以使用 Azure PowerShell 通过命令或脚本创建和管理 Azure 资源。 Azure 托管 Azure Cloud Shell（一个可通过 Azure 门户在浏览器中使用的交互式 shell 环境）。

如果选择在本地安装并使用 PowerShell，则需要使用 Azure AZ PowerShell 模块 1.0.0 或更高版本来完成此过程。 键入 `$PSVersionTable.PSVersion` 即可确定版本。 如果需要升级，请参阅[安装 Azure AZ PowerShell 模块](/powershell/azure/install-az-ps)。 如果在本地运行 PowerShell，则还需运行 `Login-AzAccount` 以创建与 Azure 的连接：

```azurepowershell-interactive
Login-AzAccount
```

1.  使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 创建 Azure 资源组。 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

    ```azurepowershell-interactive
    New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
    ```

2. 创建具有唯一名称的密钥保管库。 在这里，`Contoso-Vaultname` 是密钥保管库的名称。

   - 保管库名称：`Contoso-Vaultname`
   - 资源组名称：`ContosoResourceGroup`
   - **位置**：`EastUS`

    ```azurepowershell-interactive
    New-AzKeyVault -Name 'Contoso-Vaultname' -ResourceGroupName 'ContosoResourceGroup' -Location 'EastUS'
   ```

3. 为 DigiCert CertCentral 帐户中的以下值定义变量：

   - **帐户 ID** 
   - **组织 ID** 
   - **API 密钥** 

   ```azurepowershell-interactive
   $accountId = "myDigiCertCertCentralAccountID"
   $org = New-AzKeyVaultCertificateOrganizationDetail -Id OrganizationIDfromDigiCertAccount
   $secureApiKey = ConvertTo-SecureString DigiCertCertCentralAPIKey -AsPlainText –Force
   ```

4. 设置颁发者。 这样做将在密钥保管库中添加 Digicert 作为证书颁发机构。 [详细了解参数。](/powershell/module/az.keyvault/Set-AzKeyVaultCertificateIssuer)
   ```azurepowershell-interactive
   Set-AzKeyVaultCertificateIssuer -VaultName "Contoso-Vaultname" -Name "TestIssuer01" -IssuerProvider DigiCert -AccountId $accountId -ApiKey $secureApiKey -OrganizationDetails $org -PassThru
   ```

5. 直接在密钥保管库的 DigiCert 中设置证书的策略并颁发证书：

   ```azurepowershell-interactive
   $Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "TestIssuer01" -ValidityInMonths 12 -RenewAtNumberOfDaysBeforeExpiry 60
   Add-AzKeyVaultCertificate -VaultName "Contoso-Vaultname" -Name "ExampleCertificate" -CertificatePolicy $Policy
   ```

该证书现在由 DigiCert 证书颁发机构在指定的密钥保管库中颁发。

## <a name="troubleshoot"></a>疑难解答

如果颁发的证书在 Azure 门户中处于“禁用”状态，请查看“证书操作”以查看该证书的 DigiCert 错误消息：

:::image type="content" source="../media/certificates/how-to-integrate-certificate-authority/certificate-operation-select.png" alt-text="显示“证书操作”选项卡的屏幕截图。":::

错误消息：“请执行合并以完成此证书请求。”
   
合并由证书颁发机构签名的 CSR 以完成请求。 有关合并 CSR 的信息，请参阅[创建和合并 CSR](./create-certificate-signing-request.md)。

有关详细信息，请参阅[密钥保管库 REST API 参考中的“证书操作”](/rest/api/keyvault)。 有关建立权限的信息，请参阅[保管库 - 创建或更新](/rest/api/keyvault/vaults/createorupdate)和[保管库 - 更新访问策略](/rest/api/keyvault/vaults/updateaccesspolicy)。

## <a name="frequently-asked-questions"></a>常见问题

- 能否使用密钥保管库生成 DigiCert 通配符证书？ 
   
  是的，但这取决于你对 DigiCert 帐户的配置。
- 如何使用 DigiCert 创建 OV SSL 或 EV SSL 证书？
 
   密钥保管库支持创建 OV 和 EV SSL 证书。 创建证书时，选择“高级策略配置”，然后指定证书类型。 支持的值：OV SSL、EV SSL
   
   如果 DigiCert 帐户允许，可以在密钥保管库中创建这种类型的证书。 对于这种类型的证书，验证由 DigiCert 执行。 如果验证失败，DigiCert 支持团队可以提供帮助。 通过在 `subjectName` 中定义信息，可以在创建证书时添加信息。

  例如 `SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"`。
   
- 通过集成创建 DigiCert 证书的时间是否比直接从 DigiCert 获取证书长？
   
   否。 创建证书时，验证过程可能需要一些时间。 DigiCert 控制该过程。


## <a name="next-steps"></a>后续步骤

- [身份验证、请求和响应](../general/authentication-requests-and-responses.md)
- [Key Vault 开发人员指南](../general/developers-guide.md)
