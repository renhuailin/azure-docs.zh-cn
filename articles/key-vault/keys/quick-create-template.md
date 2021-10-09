---
title: Azure 快速入门 - 使用 Azure 资源管理器模板创建 Azure 密钥保管库和密钥 | Microsoft Docs
description: 快速入门介绍如何使用 Azure 资源管理器模板（ARM 模板）创建 Azure 密钥保管库，并将密钥添加到保管库。
services: key-vault
author: sebansal
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: mvc,subject-armqs, devx-track-azurepowershell
ms.date: 10/14/2020
ms.author: sebansal
ms.openlocfilehash: 8e8a0e931a2c33883bf18ad92a367110d9be093e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128572956"
---
# <a name="quickstart-create-an-azure-key-vault-and-a-key-by-using-arm-template"></a>快速入门：使用 ARM 模板创建 Azure 密钥保管库和密钥 

[Azure Key Vault](../general/overview.md) 是为密钥、密码、证书等机密及其他机密提供安全存储的云服务。 本快速入门重点介绍部署 Azure 资源管理器模板（ARM 模板）以创建密钥保管库和密钥的过程。


## <a name="prerequisites"></a>先决条件

若要完成本文，需要做好以下准备：

- 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 用户需要分配有 Azure 内置角色，推荐角色为参与者。 [在此处了解详细信息](../../role-based-access-control/role-assignments-portal.md)
- 模板需要使用你的 Azure AD 用户对象 ID 来配置权限。 以下过程获取对象 ID (GUID)。

    1. 运行以下 Azure PowerShell 或 Azure CLI 命令：选择“试用”，然后在 shell 窗格中粘贴脚本。 若要粘贴脚本，请右键单击 shell，然后选择“粘贴”。

        # <a name="cli"></a>[CLI](#tab/CLI)
        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --id $upn --query "objectId" &&
        echo "Press [ENTER] to continue ..."
        ```

        # <a name="powershell"></a>[PowerShell](#tab/PowerShell)
        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        Write-Host "Press [ENTER] to continue..."
        ```

        ---

    1. 请记下对象 ID， 本快速入门的下一部分需要使用该 ID。

## <a name="review-the-template"></a>查看模板

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vaultName": {
      "type": "string",
      "metadata": {
        "description": "The name of the key vault to be created."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "The SKU of the vault to be created."
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "The name of the key to be created."
      }
    },
    "keyType": {
      "type": "string",
      "metadata": {
        "description": "The JsonWebKeyType of the key to be created."
      }
    },
    "keyOps": {
      "type": "array",
      "defaultValue": [],
      "metadata": {
        "description": "The permitted JSON web key operations of the key to be created."
      }
    },
    "keySize": {
      "type": "int",
      "defaultValue": 2048,
      "metadata": {
        "description": "The size in bits of the key to be created."
      }
    },
    "curveName": {
      "type": "string",
      "defaultValue": "",
      "metadata": {
        "description": "The JsonWebKeyCurveName of the key to be created."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('vaultName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "enableRbacAuthorization": false,
        "enableSoftDelete": false,
        "enabledForDeployment": false,
        "enabledForDiskEncryption": false,
        "enabledForTemplateDeployment": false,
        "tenantId": "[subscription().tenantId]",
        "accessPolicies": [],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        },
        "networkAcls": {
          "defaultAction": "Allow",
          "bypass": "AzureServices"
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/keys",
      "apiVersion": "2019-09-01",
      "name": "[concat(parameters('vaultName'), '/', parameters('keyName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', parameters('vaultName'))]"
      ],
      "properties": {
        "kty": "[parameters('keyType')]",
        "keyOps": "[parameters('keyOps')]",
        "keySize": "[parameters('keySize')]",
        "curveName": "[parameters('curveName')]"
      }
    }
  ],
  "outputs": {
    "proxyKey": {
      "type": "object",
      "value": "[reference(resourceId('Microsoft.KeyVault/vaults/keys', parameters('vaultName'), parameters('keyName')))]"
    }
  }
}
```

该模板中定义了以下两个资源：

- [Microsoft.KeyVault/vaults](/azure/templates/microsoft.keyvault/vaults)
- Microsoft.KeyVault/vaults/keys

可以在 [Azure 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault&pageNumber=1&sort=Popular)中找到更多 Azure Key Vault 模板示例。

## <a name="parameters-and-definitions"></a>参数和定义

|参数  |定义  |
|---------|---------|
|**keyOps**  | 指定可使用该密钥执行的操作。 如果未指定此参数，可执行所有操作。 此参数的可接受值是 [JSON Web 密钥 (JWK) 规范](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)中定义的一个以逗号分隔的密钥操作列表： <br> `["sign", "verify", "encrypt", "decrypt", " wrapKey", "unwrapKey"]` |
|**CurveName**  |  EC 密钥类型的椭圆曲线名称。 请参阅 [JsonWebKeyCurveName](/rest/api/keyvault/createkey/createkey#jsonwebkeycurvename) |
|**Kty**  |  要创建的密钥类型。 有关有效值，请参阅 [JsonWebKeyType](/rest/api/keyvault/createkey/createkey#jsonwebkeytype) |
|**标记** | 键/值对形式的应用程序特定元数据。  |
|**nbf**  |  指定时间（作为 DateTime 对象），在此时间之前不能使用该密钥。 格式为 Unix 时间戳（UTC 时间 1970 年 1 月 1 日 Unix 纪元之后的秒数）。  |
|**exp**  |  指定过期时间作为 DateTime 对象。 格式为 Unix 时间戳（UTC 时间 1970 年 1 月 1 日 Unix 纪元之后的秒数）。 |

## <a name="deploy-the-template"></a>部署模板
可以使用 [Azure 门户](../../azure-resource-manager/templates/deploy-portal.md)、Azure PowerShell、Azure CLI 或 REST API。 要了解部署方法，请参阅[部署模板](../../azure-resource-manager/templates/deploy-powershell.md)。

## <a name="review-deployed-resources"></a>查看已部署的资源

你可以使用 Azure 门户检查密钥保管库和密钥，或者使用以下 Azure CLI 或 Azure PowerShell 脚本列出创建的密钥。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter your key vault name:" &&
read keyVaultName &&
az keyvault key list --vault-name $keyVaultName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$keyVaultName = Read-Host -Prompt "Enter your key vault name"
Get-AzKeyVaultKey -vaultName $keyVaultName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>清理资源

其他 Key Vault 快速入门和教程是在本快速入门的基础上制作的。 如果打算继续使用后续的快速入门和教程，则可能需要保留这些资源。
如果不再需要资源组，可以将其删除，这将删除 Key Vault 和相关的资源。 使用 Azure CLI 或 Azure PowerShell 删除资源组：

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>后续步骤

在本快速入门中，你使用 ARM 模板创建了密钥保管库和密钥，并验证了部署。 若要详细了解 Key Vault 和 Azure 资源管理器，请继续阅读以下文章。

- 阅读 [Azure Key Vault 概述](../general/overview.md)
- 了解有关 [Azure 资源管理器](../../azure-resource-manager/management/overview.md)的详细信息
- 请参阅 [Key Vault 安全性概述](../general/security-features.md)
