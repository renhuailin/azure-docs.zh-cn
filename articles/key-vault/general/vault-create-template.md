---
title: 使用 ARM 模板创建 Azure 密钥保管库和保管库访问策略
description: 本文介绍如何使用 Azure 资源管理器模板创建 Azure 密钥保管库和保管库访问策略。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 3/14/2021
ms.author: mbaldwin
ms.openlocfilehash: e489efd335d2a46a0e55d4ceb9bdee809ae6c39d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "104595237"
---
# <a name="how-to-create-an-azure-key-vault-and-vault-access-policy-by-using-a-resource-manager-template"></a>如何使用资源管理器模板创建 Azure 密钥保管库和保管库访问策略

[Azure Key Vault](../general/overview.md) 是一项云服务，它为密钥、密码和证书等机密提供了安全的存储。 本文介绍了部署 Azure 资源管理器模板（ARM 模板）以创建密钥保管库的过程。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>先决条件

完成本文中的步骤：

* 如果没有 Azure 订阅，可以在开始之前[创建一个免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。


## <a name="create-a-key-vault-resource-manager-template"></a>创建 Key Vault 资源管理器模板

以下模板显示了创建密钥保管库的基本方式。 某些值在模板中指定。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
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
        "description": "Specifies whether the key vault is a standard vault or a premium vault."
      }
    }
   },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('keyVaultName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "enabledForDeployment": "false",
        "enabledForDiskEncryption": "false",
        "enabledForTemplateDeployment": "false",
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
    }
  ]
}

```

有关 Key Vault 模板设置的详细信息，请参阅 [Key Vault ARM 模板参考](/azure/templates/microsoft.keyvault/vaults)。

> [!IMPORTANT]
> 如果重新部署了模板，则将覆盖密钥保管库中的任何现有访问策略。 建议你使用现有的访问策略填充 `accessPolicies` 属性，以免失去对密钥保管库的访问权限。 

## <a name="add-an-access-policy-to-a-key-vault-resource-manager-template"></a>将访问策略添加到 Key Vault 资源管理器模板

可以向现有密钥保管库部署访问策略，而无需重新部署整个密钥保管库模板。 以下模板显示了创建访问策略的基本方式：

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "objectId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the object ID of a user, service principal or security group in the Azure Active Directory tenant for the vault. The object ID must be unique for the list of access policies. Get it by using Get-AzADUser or Get-AzADServicePrincipal cmdlets."
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to keys in the vault. Valid values are: all, encrypt, decrypt, wrapKey, unwrapKey, sign, verify, get, list, create, update, import, delete, backup, restore, recover, and purge."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to secrets in the vault. Valid values are: all, get, list, set, delete, backup, restore, recover, and purge."
      }
    },
    "certificatePermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to certificates in the vault. Valid values are: all,  create, delete, update, deleteissuers, get, getissuers, import, list, listissuers, managecontacts, manageissuers,  recover, backup, restore, setissuers, and purge."
      }
    },
  "resources": [
     {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "name": "[concat(parameters('keyVaultName'), '/add')]",
      "apiVersion": "2019-09-01",
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[parameters('objectId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]",
              "certificates": "[parameters('certificatePermissions')]"
            }
          }
        ]
      }
    }
  ]
}

```

有关 Key Vault 模板设置的详细信息，请参阅 [Key Vault ARM 模板参考](/azure/templates/microsoft.keyvault/vaults/accesspolicies)。

## <a name="more-key-vault-resource-manager-templates"></a>更多 Key Vault 资源管理器模板

还有其他资源管理器模板可用于 Key Vault 对象：

| 机密 | 键 | 证书 |
|--|--|--|
|<ul><li>[快速入门](../secrets/quick-create-template.md)<li>[引用](/azure/templates/microsoft.keyvault/vaults/secrets)|空值|空值|

可在以下文章中找到更多 Key Vault 模板：[Key Vault 资源管理器参考](/azure/templates/microsoft.keyvault/allversions)。

## <a name="deploy-the-templates"></a>部署模板

可以使用 Azure 门户来部署上述模板，方法是按下面所述使用“在编辑器中生成自己的模板”选项：[从自定义模板部署资源](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)。

还可以将上述模板保存到文件中，并使用以下命令：[New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) 和 [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create)：

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile key-vault-template.json
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file key-vault-template.json
```

## <a name="clean-up-resources"></a>清理资源

如果打算继续使用后续的快速入门和教程，则可以将这些资源保留在原处。 当不再需要资源时，请删除资源组。 如果删除该组，则也会删除密钥保管库和相关资源。 若要使用 Azure CLI 或 Azure PowerShell 删除资源组，请完成以下步骤：

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

## <a name="resources"></a>资源

- 阅读 [Azure Key Vault 概述](../general/overview.md)。
- 了解有关 [Azure Resource Manager](../../azure-resource-manager/management/overview.md) 的详细信息。
- 请参阅 [Azure Key Vault 安全性概述](security-overview.md)

## <a name="next-steps"></a>后续步骤

- [保护对密钥保管库的访问](secure-your-key-vault.md)
- [向密钥保管库进行身份验证](authentication.md)
- [Azure 密钥保管库开发人员指南](developers-guide.md)
