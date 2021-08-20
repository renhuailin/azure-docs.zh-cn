---
title: 快速入门 - 使用 PowerShell 在密钥保管库中设置和检索机密
description: 本快速入门介绍如何使用 Azure PowerShell 在 Azure Key Vault 中创建、检索和删除机密。
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, devx-track-azurepowershell
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: 2e51e781ca988a496e3f1ba61d8b6a4fcc3d7095
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114443497"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-powershell"></a>快速入门：使用 PowerShell 在 Azure Key Vault 中设置和检索机密

Azure Key Vault 是一项云服务，用作安全的机密存储。 可以安全地存储密钥、密码、证书和其他机密。 有关 Key Vault 的详细信息，可以参阅[概述](../general/overview.md)。 在本快速入门中，请使用 PowerShell 创建一个密钥保管库， 然后即可在新创建的保管库中存储机密。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 PowerShell，则本教程需要 Azure PowerShell 模块 5.0.0 版或更高版本。 键入 `$PSVersionTable.PSVersion` 即可查找版本。 如果需要进行升级，请参阅 [Install Azure PowerShell module](/powershell/azure/install-az-ps)（安装 Azure PowerShell 模块）。 如果在本地运行 PowerShell，则还需运行 `Connect-AzAccount` 来创建与 Azure 的连接。

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>创建资源组

[!INCLUDE [Create a resource group](../../../includes/key-vault-powershell-rg-creation.md)]

## <a name="create-a-key-vault"></a>创建密钥保管库

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-kv-creation.md)]

## <a name="give-your-user-account-permissions-to-manage-secrets-in-key-vault"></a>向用户帐户授予管理 Key Vault 中的机密的权限

使用 Azure PowerShell Set-AzKeyVaultAccessPolicy cmdlet 来更新 Key Vault 访问策略并向用户帐户授予机密权限。

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -UserPrincipalName "user@domain.com" -PermissionsToSecrets get,set,delete
```

## <a name="adding-a-secret-to-key-vault"></a>向 Key Vault 添加机密

只需执行几个步骤即可向保管库添加机密。 在此示例中，请添加可供应用程序使用的密码。 此密码名为 **ExamplePassword**，其中存储的值为 **hVFkk965BuUv**。

先键入以下内容，将 **hVFkk965BuUv** 值转换成安全字符串：

```azurepowershell-interactive
$secretvalue = ConvertTo-SecureString "hVFkk965BuUv" -AsPlainText -Force
```

然后，使用 Azure PowerShell [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret) cmdlet 在密钥保管库中创建一个机密，名为 **ExamplePassword**，且值为 **hVFkk965BuUv**：


```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName "<your-unique-keyvault-name>" -Name "ExamplePassword" -SecretValue $secretvalue
```

## <a name="retrieve-a-secret-from-key-vault"></a>从 Key Vault 检索机密

若要查看机密中包含的纯文本形式的值，请执行以下命令：

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName "<your-unique-keyvault-name>" -Name "ExamplePassword" -AsPlainText
```

现在，你已创建 Key Vault 并存储和检索了机密。

## <a name="clean-up-resources"></a>清理资源

 本系列中的其他快速入门和教程是在本快速入门的基础上制作的。 如果打算继续使用其他快速入门和教程，则可能需要保留这些资源。

如果不再需要资源组、Key Vault 和所有相关的资源，可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 命令将其删除。

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了 Key Vault 并在其中存储了一个机密。 若要详细了解 Key Vault 以及如何将其与应用程序集成，请继续阅读以下文章。

- 阅读 [Azure Key Vault 概述](../general/overview.md)
- 了解如何[在密钥保管库中存储多行机密](multiline-secrets.md)
- 请参阅 [Azure PowerShell Key Vault cmdlet](/powershell/module/az.keyvault/#key_vault) 参考
- 请参阅 [Key Vault 安全性概述](../general/security-features.md)
