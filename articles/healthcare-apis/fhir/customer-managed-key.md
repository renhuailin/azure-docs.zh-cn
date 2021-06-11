---
title: 为 Azure API for FHIR 配置客户管理的密钥
description: Azure API for FHIR 中通过 Cosmos DB 支持的自带密钥功能
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 05/04/2021
ms.author: ginle
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0c32725d020043c3cd62828e4bdccfafef2f53aa
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111970181"
---
# <a name="configure-customer-managed-keys-at-rest"></a>配置客户管理的静态密钥

创建新的 Azure API for FHIR 帐户时，默认使用 Microsoft 托管密钥对数据进行加密。 现在，可以使用自己选择和管理的密钥为数据添加第二层加密。

在 Azure 中，通常使用客户的 Azure Key Vault 中的加密密钥来完成此操作。 例如，目前 Azure SQL、Azure 存储和 Cosmos DB 等均提供此功能。 Azure API for FHIR 利用 Cosmos DB 对此功能的支持。 创建帐户时，可以选择指定 Azure Key Vault 密钥 URI。 预配 DB 帐户后，此密钥将被传递给 Cosmos DB。 发出 FHIR 请求后，Cosmos DB 会提取你的密钥，并使用它来加密/解密数据。 

若要开始，请参阅以下链接：

- [为 Azure 订阅注册 Azure Cosmos DB 资源提供程序](../../cosmos-db/how-to-setup-cmk.md#register-resource-provider) 
- [配置 Azure Key Vault 实例](../../cosmos-db/how-to-setup-cmk.md#configure-your-azure-key-vault-instance)
- [将访问策略添加到 Azure Key Vault 实例](../../cosmos-db/how-to-setup-cmk.md#add-access-policy)
- [在 Azure Key Vault 中生成密钥](../../cosmos-db/how-to-setup-cmk.md#generate-a-key-in-azure-key-vault)

## <a name="using-azure-portal"></a>使用 Azure 门户

在 Azure 门户上创建用于 FHIR 帐户的 Azure API 时，你会注意到 "**其他设置**" 选项卡上的 "**数据库设置**" 下的 "**数据加密**" 配置选项。默认情况下，将选择 "服务托管密钥" 选项。

> [!Important]
> 只有在创建了用于 FHIR 的 Azure API 且以后不能更改时，"数据加密" 选项才可用。 但是，如果选择了 " **客户托管密钥** " 选项，则可以查看和更新加密密钥。 


你可以从 KeyPicker 中选择密钥：

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="KeyPicker":::

还可以通过选择 " **客户管理的密钥** " 选项在此处指定 Azure Key Vault 密钥。
 
你还可以在此处输入密钥 URI：

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="创建 Azure API for FHIR":::

> [!Important]
> 确保正确设置 Azure Key Vault 的所有权限。 有关详细信息，请参阅 [向 Azure Key Vault 实例添加访问策略](../../cosmos-db/how-to-setup-cmk.md#add-access-policy)。 此外，请确保在 Key Vault 的属性中启用软删除。 未完成这些步骤将导致部署错误。 有关详细信息，请参阅验证是否已 [在密钥保管库上启用软删除和启用软删除](../../key-vault/general/key-vault-recovery.md?tabs=azure-portal#verify-if-soft-delete-is-enabled-on-a-key-vault-and-enable-soft-delete)。

对于现有的 FHIR 帐户，你可以在 "**数据库**" 边栏选项卡中查看密钥加密选项 (**服务托管的密钥** 或 **客户托管的密钥**) ，如下所示。 选择配置选项后，无法对其进行修改。 但是，可以修改和更新密钥。

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-database.png" alt-text="Database":::

此外，你还可以创建指定密钥的新版本，此后使用该新版本加密数据就不会发生任何服务中断。 也可以删除对密钥的访问权限，从而删除对数据的访问权限。 禁用该密钥后，查询将导致错误。 如果重新启用该密钥，查询将再次成功。

## <a name="using-azure-powershell"></a>使用 Azure PowerShell

借助 Azure Key Vault 密钥 URI，可以通过运行以下 PowerShell 命令来使用 PowerShell 配置 CMK：

```powershell
New-AzHealthcareApisService
    -Name "myService"
    -Kind "fhir-R4"
    -ResourceGroupName "myResourceGroup"
    -Location "westus2"
    -CosmosKeyVaultKeyUri "https://<my-vault>.vault.azure.net/keys/<my-key>"
```

## <a name="using-azure-cli"></a>使用 Azure CLI

与 PowerShell 方法一样，可以通过在 `key-vault-key-uri` 参数下传递 Azure Key Vault URI 并运行以下 CLI 命令来配置 CMK： 

```azurecli-interactive
az healthcareapis service create
    --resource-group "myResourceGroup"
    --resource-name "myResourceName"
    --kind "fhir-R4"
    --location "westus2"
    --cosmos-db-configuration key-vault-key-uri="https://<my-vault>.vault.azure.net/keys/<my-key>"

```
## <a name="using-azure-resource-manager-template"></a>使用 Azure 资源管理器模板

借助 Azure Key Vault 密钥 URI，可以通过将 CMK 传递到“属性”对象中的 keyVaultKeyUri 属性下来配置 CMK 。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "services_myService_name": {
            "defaultValue": "myService",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.HealthcareApis/services",
            "apiVersion": "2020-03-30",
            "name": "[parameters('services_myService_name')]",
            "location": "westus2",
            "kind": "fhir-R4",
            "properties": {
                "accessPolicies": [],
                "cosmosDbConfiguration": {
                    "offerThroughput": 400,
                    "keyVaultKeyUri": "https://<my-vault>.vault.azure.net/keys/<my-key>"
                },
                "authenticationConfiguration": {
                    "authority": "https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db47",
                    "audience": "[concat('https://', parameters('services_myService_name'), '.azurehealthcareapis.com')]",
                    "smartProxyEnabled": false
                },
                "corsConfiguration": {
                    "origins": [],
                    "headers": [],
                    "methods": [],
                    "maxAge": 0,
                    "allowCredentials": false
                }
            }
        }
    ]
}
```

此外，还可以使用以下 PowerShell 脚本部署模板：

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

## <a name="next-steps"></a>后续步骤

本文介绍了如何使用 Azure 门户、PowerShell、CLI 和资源管理器模板配置静态客户管理的密钥。 有关详细信息，请参阅 Azure Cosmos DB FAQ 部分。 
 
>[!div class="nextstepaction"]
>[Cosmos DB：如何设置 CMK](../../cosmos-db/how-to-setup-cmk.md#frequently-asked-questions)