---
title: 快速入门 - 使用 Azure PowerShell 设置和查看 Azure Key Vault 证书
description: 本快速入门展示了如何使用 Azure PowerShell 在 Azure Key Vault 中设置和检索证书
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurepowershell
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: 170c115e56232b334527d92623c87bacd377f5aa
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128659831"
---
# <a name="quickstart-set-and-retrieve-a-certificate-from-azure-key-vault-using-azure-powershell"></a>快速入门：使用 Azure PowerShell 在 Azure Key Vault 中设置和检索证书

在本快速入门中，你将使用 Azure PowerShell 在 Azure Key Vault 中创建一个密钥保管库。 Azure Key Vault 是一项云服务，用作安全的机密存储。 可以安全地存储密钥、密码、证书和其他机密。 有关 Key Vault 的详细信息，可以参阅[概述](../general/overview.md)。 Azure PowerShell 用于通过命令或脚本创建和管理 Azure 资源。 完成该操作后，你将存储证书。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 PowerShell，则本教程需要 Azure PowerShell 模块 1.0.0 或更高版本。 键入 `$PSVersionTable.PSVersion` 即可查找版本。 如果需要进行升级，请参阅 [Install Azure PowerShell module](/powershell/azure/install-az-ps)（安装 Azure PowerShell 模块）。 如果在本地运行 PowerShell，则还需运行 `Login-AzAccount` 来创建与 Azure 的连接。

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>创建资源组

[!INCLUDE [Create a resource group](../../../includes/key-vault-powershell-rg-creation.md)]

## <a name="create-a-key-vault"></a>创建密钥保管库

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-kv-creation.md)]

## <a name="add-a-certificate-to-key-vault"></a>将证书添加到 Key Vault

若要向保管库中添加证书，只需再执行几个步骤即可。 此证书可供应用程序使用。 

键入以下命令，使用名为 ExampleCertificate  的策略创建自签名证书：

```azurepowershell-interactive
$Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal

Add-AzKeyVaultCertificate -VaultName "<your-unique-keyvault-name>" -Name "ExampleCertificate" -CertificatePolicy $Policy
```

现在，可以通过 URI 来引用已添加到 Azure Key Vault 的此证书。 使用 **`https://<your-unique-keyvault-name>.vault.azure.net/certificates/ExampleCertificate`** 获取当前版本。 

若要查看以前存储的证书，请使用以下命令：

```azurepowershell-interactive
Get-AzKeyVaultCertificate -VaultName "<your-unique-keyvault-name>" -Name "ExampleCertificate"
```

现在，你已创建了一个密钥保管库，存储了一个证书，并检索了该证书。

**故障排除**：

操作返回了无效的状态代码“禁止”

如果你收到此错误，则表明访问 Azure Key Vault 的帐户没有创建证书的适当权限。

运行以下 Azure PowerShell 命令，以分配适当的权限：

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <KeyVaultName> -ObjectId <AzureObjectID> -PermissionsToCertificates get,list,update,create
```

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个密钥保管库并在其中存储了一个证书。 若要详细了解 Key Vault 以及如何将其与应用程序集成，请继续阅读以下文章。

- 阅读 [Azure Key Vault 概述](../general/overview.md)
- 请参阅 [Azure PowerShell Key Vault cmdlet](/powershell/module/az.keyvault/) 参考
- 请参阅 [Key Vault 安全性概述](../general/security-features.md)
