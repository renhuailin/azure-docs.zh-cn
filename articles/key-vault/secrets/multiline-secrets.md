---
title: 将多行机密存储在 Azure Key Vault 中
description: 本教程介绍如何使用 Azure CLI 和 PowerShell 从 Azure Key Vault 中设置多行机密
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 03/17/2021
ms.author: mbaldwin
ms.openlocfilehash: e320795d5e8623dea9b97ac6371a0ffc6e6117f1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608418"
---
# <a name="store-a-multi-line-secret-in-azure-key-vault"></a>将多行机密存储在 Azure Key Vault 中

[Azure CLI 快速入门](quick-create-cli.md)和 [Azure PowerShell 快速入门](quick-create-powershell.md)演示了如何存储单行机密。   还可以使用 Key Vault 来存储多行机密，如 JSON 文件或 RSA 私钥。

多行机密无法通过命令行传递给 Azure CLI [az keyvault secret set](/cli/azure/keyvault/secret#az_keyvault_secret_set) 命令或 Azure PowerShell [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret) cmdlet。 而是必须首先将多行机密存储为文本文件。 

例如，可以创建一个名为“secretfile.txt”的文本文件，其中包含以下行：

```bash
This is my
multi-line
secret
```

然后，可以使用 `--file` 参数将此文件传递给 Azure CLI [az keyvault secret set](/cli/azure/keyvault/secret#az_keyvault_secret_set) 命令。

```azurecli-interactive
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "MultilineSecret" --file "secretfile.txt"
```

使用 Azure PowerShell 时，必须先使用 [Get-Content](/powershell/module/microsoft.powershell.management/get-content) cmdlet 来读取文件，然后使用 [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring) 将其转换为安全字符串。 

```azurepowershell-interactive
$RawSecret =  Get-Content "secretfile.txt" -Raw
$SecureSecret = ConvertTo-SecureString -String $RawSecret -AsPlainText -Force
```

最后，使用 [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret) cmdlet 存储机密。

```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName "<your-unique-keyvault-name>" -Name "MultilineSecret" -SecretValue $SecureSecret
```

在任一情况下，都可以使用 Azure CLI [az keyvault secret show](/cli/azure/keyvault/secret#az_keyvault_secret_show) 命令或 Azure PowerShell [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret) cmdlet 查看存储的机密。

```azurecli-interactive
az keyvault secret show --name "MultilineSecret" --vault-name "<your-unique-keyvault-name>" --query "value"
```

将返回嵌入了换行符的机密：

```bash
"This is\nmy multi-line\nsecret"
```

## <a name="next-steps"></a>后续步骤

- 阅读 [Azure Key Vault 概述](../general/overview.md)
- 请参阅 [Azure CLI 快速入门](quick-create-cli.md)
- 请参阅 [Azure CLI az keyvault 命令](/cli/azure/keyvault)
- 请参阅 [Azure PowerShell 快速入门](quick-create-powershell.md)
- 请参阅 [Azure PowerShell Az KeyVault cmdlet](/powershell/module/az.keyvault#key-vault)
