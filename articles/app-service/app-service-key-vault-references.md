---
title: 使用 Key Vault 引用
description: 了解如何使用 Key Vault 引用设置 Azure 应用服务和 Azure Functions。 向应用程序代码提供 Key Vault 机密。
author: mattchenderson
ms.topic: article
ms.date: 06/11/2021
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 15b5974aff53303ca0245fc6100ea22eebc70c6d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121752471"
---
# <a name="use-key-vault-references-for-app-service-and-azure-functions"></a>使用应用服务和 Azure Functions 的 Key Vault 引用

本主题介绍在不需进行任何代码更改的情况下，如何使用应用服务或 Azure Functions 应用程序的 Azure Key Vault 中的机密。 [Azure Key Vault](../key-vault/general/overview.md) 是一项服务，可以提供集中式机密管理，并且可以完全控制访问策略和审核历史记录。

## <a name="granting-your-app-access-to-key-vault"></a>授予应用对 Key Vault 的访问权限

若要从 Key Vault 读取机密，需创建一个保管库并授予应用访问该保管库的权限。

1. 按照 [Key Vault 快速入门](../key-vault/secrets/quick-create-cli.md)中的说明创建一个密钥保管库。

1. 为应用程序创建一个[托管标识](overview-managed-identity.md)。

    默认情况下，Key Vault 引用将使用应用系统分配的标识，但你可以[指定用户分配的标识](#access-vaults-with-a-user-assigned-identity)。

1. 在 Key Vault 中为此前创建的应用程序标识创建一项[访问策略](../key-vault/general/security-features.md#privileged-access)。 在此策略上启用“获取”机密权限。 请勿配置“授权的应用程序”或 `applicationId` 设置，因为这与托管标识不兼容。

### <a name="access-network-restricted-vaults"></a>访问网络受限保管库

如果你的保管库配置有[网络限制](../key-vault/general/overview-vnet-service-endpoints.md)，则你还需要确保该应用程序具有网络访问权。

1. 请确保该应用程序已配置有出站网络功能，正如[应用服务网络功能](./networking-features.md)和 [Azure Functions 网络选项](../azure-functions/functions-networking-options.md)所述。

    试图使用专用终结点的 Linux 应用程序还要求显式将该应用配置为通过虚拟网络路由所有流量。 即将到来的更新中将删除此要求。 若要这样设置，请使用以下 CLI 命令：

    ```azurecli
    az webapp config set --subscription <sub> -g <rg> -n <appname> --generic-configurations '{"vnetRouteAllEnabled": true}'
    ```

2. 请确保该保管库的配置将你的应用访问该保管库时所用的网络或子网纳入考量。

### <a name="access-vaults-with-a-user-assigned-identity"></a>使用用户分配的标识访问保管库

系统分配的标识尚不可用时，某些应用需要在创建时引用机密。 在这些情况下，可以提前创建用户分配的标识并为其提供对保管库的访问权限。

被授予对用户分配的标识的权限后，请执行以下步骤：

1. [将标识分配](./overview-managed-identity.md#add-a-user-assigned-identity)到应用程序（如果尚未分配）。

1. 通过将 `keyVaultReferenceIdentity` 属性设置为用户分配的标识的资源 ID，配置应用以将此标识用于 Key Vault 引用操作。

    ```azurecli-interactive
    userAssignedIdentityResourceId=$(az identity show -g MyResourceGroupName -n MyUserAssignedIdentityName --query id -o tsv)
    appResourceId=$(az webapp show -g MyResourceGroupName -n MyAppName --query id -o tsv)
    az rest --method PATCH --uri "${appResourceId}?api-version=2021-01-01" --body "{'properties':{'keyVaultReferenceIdentity':'${userAssignedIdentityResourceId}'}}"
    ```

此配置将应用于应用的所有引用。

## <a name="reference-syntax"></a>引用语法

Key Vault 引用采用 `@Microsoft.KeyVault({referenceString})` 格式，其中 `{referenceString}` 将替换为下述选项之一：

> [!div class="mx-tdBreakAll"]
> | 引用字符串                                                            | 说明                                                                                                                                                                                 |
> |-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | SecretUri=_secretUri_                                                       | SecretUri 应该是 Key Vault 中机密的完整数据平面 URI，可以选择包括版本，例如 `https://myvault.vault.azure.net/secrets/mysecret/` 或 `https://myvault.vault.azure.net/secrets/mysecret/ec96f02080254f109c51a1f14cdb1931`  |
> | VaultName=_vaultName_;SecretName=_secretName_;SecretVersion=_secretVersion_ | VaultName 是必需的，并且应该是 Key Vault 资源的名称。 SecretName 是必需的，并且应该是目标机密的名称。 SecretVersion 是可选的，但如果存在，则指示要使用的机密的版本。 |

例如，完整的引用将如下所示：

```
@Microsoft.KeyVault(SecretUri=https://myvault.vault.azure.net/secrets/mysecret/)
```

也可使用以下命令：

```
@Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret)
```

## <a name="rotation"></a>旋转

如果引用中未指定版本，则应用将会使用 Key Vault 中存在的最新版本。 在有较新的版本可用时（例如通过轮换事件），应用将会在一天内自动更新并开始使用最新版本。 对应用所做的任何配置更改都将导致立即更新到所有引用的机密的最新版本。

## <a name="source-application-settings-from-key-vault"></a>Key Vault 中的源应用程序设置

Key Vault 引用可以用作[应用程序设置](configure-common.md#configure-app-settings)的值，以便将机密保存在 Key Vault 而不是站点配置中。可以对应用程序设置进行安全的静态加密，但如果需要机密管理功能，则应将它们置于 Key Vault 中。

若要将 Key Vault 引用用于[应用程序设置](configure-common.md#add-or-edit)，请将引用设为设置的值。 应用可以通过密钥正常引用机密。 不需更改代码。

> [!TIP]
> 应该将大多数使用 Key Vault 引用的应用程序设置标记为槽设置，因为你应该为每个环境设置单独的保管库。

### <a name="considerations-for-azure-files-mounting"></a>Azure 文件存储装载注意事项

应用可使用 `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` 应用程序设置将 Azure 文件存储装载为文件系统。 此设置有额外的验证检查，旨在确保该应用可以正常启动。 平台依赖于在 Azure 文件存储中拥有内容共享，而且除非通过 `WEBSITE_CONTENTSHARE` 设置指定名称，否则其会采用默认名称。 对于任何修改这些设置的请求，平台会尝试验证此内容共享是否存在，如果不存在，则会尝试创建共享。 如果找不到或无法创建该内容共享，系统会阻止该请求。

对此设置使用密钥保管库引用时，默认情况下该验证检查会失败，因为在处理传入请求时，无法解析密钥本身。 若要避免此问题，可以通过将 `WEBSITE_SKIP_CONTENTSHARE_VALIDATION` 设置为“1”来跳过验证。 这将跳过所有检查，不会创建内容共享。 您应确保预先创建该内容共享。 

> [!CAUTION]
> 如果跳过验证，并且连接字符串或内容共享无效，该应用将无法正常启动，并且只会显示 HTTP 500 错误。

在创建站点的过程中，也可能会因为未传播托管标识权限，或未设置虚拟网络集成，导致内容共享装载尝试失败。 你可将 Azure 文件存储的设置工作推迟到稍后在部署模板中进行，以适应这种情况。 若要了解更多信息，请参阅 [Azure 资源管理器部署](#azure-resource-manager-deployment)。 “应用服务”会使用默认文件系统直至 Azure 文件存储设置完毕，并且不会复制文件，因此你需要确保在装载 Azure 文件存储之前的过渡期内不会进行任何部署尝试。

### <a name="azure-resource-manager-deployment"></a>Azure 资源管理器部署

通过 Azure 资源管理器模板自动进行资源部署时，可能需要将依赖项按特定的顺序排列，这样才能使该功能发挥作用。 请注意，需将应用程序设置定义为其自己的资源，而不能使用站点定义中的 `siteConfig` 属性。 这是因为，站点需先进行定义，这样才能使用它来创建系统分配标识并将该标识用在访问策略中。

函数应用的示例伪模板可能如下所示：

```json
{
    //...
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            //...
        },
        {
            "type": "Microsoft.Insights/components",
            "name": "[variables('appInsightsName')]",
            //...
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[variables('functionAppName')]",
            "identity": {
                "type": "SystemAssigned"
            },
            //...
            "resources": [
                {
                    "type": "config",
                    "name": "appsettings",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('storageConnectionStringName'))]",
                        "[resourceId('Microsoft.KeyVault/vaults/secrets', variables('keyVaultName'), variables('appInsightsKeyName'))]"
                    ],
                    "properties": {
                        "AzureWebJobsStorage": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('storageConnectionStringResourceId')).secretUriWithVersion, ')')]",
                        "APPINSIGHTS_INSTRUMENTATIONKEY": "[concat('@Microsoft.KeyVault(SecretUri=', reference(variables('appInsightsKeyResourceId')).secretUriWithVersion, ')')]",
                        "WEBSITE_ENABLE_SYNC_UPDATE_SITE": "true"
                        //...
                    }
                },
                {
                    "type": "sourcecontrols",
                    "name": "web",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]",
                        "[resourceId('Microsoft.Web/sites/config', variables('functionAppName'), 'appsettings')]"
                    ],
                }
            ]
        },
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[variables('keyVaultName')]",
            //...
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('functionAppName'))]"
            ],
            "properties": {
                //...
                "accessPolicies": [
                    {
                        "tenantId": "[reference(resourceId('Microsoft.Web/sites/', variables('functionAppName')), '2020-12-01', 'Full').identity.tenantId]",
                        "objectId": "[reference(resourceId('Microsoft.Web/sites/', variables('functionAppName')), '2020-12-01', 'Full').identity.principalId]",
                        "permissions": {
                            "secrets": [ "get" ]
                        }
                    }
                ]
            },
            "resources": [
                {
                    "type": "secrets",
                    "name": "[variables('storageConnectionStringName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
                    ],
                    "properties": {
                        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountResourceId'),'2019-09-01').key1)]"
                    }
                },
                {
                    "type": "secrets",
                    "name": "[variables('appInsightsKeyName')]",
                    //...
                    "dependsOn": [
                        "[resourceId('Microsoft.KeyVault/vaults/', variables('keyVaultName'))]",
                        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
                    ],
                    "properties": {
                        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2019-09-01').InstrumentationKey]"
                    }
                }
            ]
        }
    ]
}
```

> [!NOTE] 
> 在此示例中，源代码管理部署取决于应用程序设置。 这通常是不安全的行为，因为应用设置更新是以异步方式表现的。 不过，由于我们已包括 `WEBSITE_ENABLE_SYNC_UPDATE_SITE` 应用程序设置，因此更新是同步的。 这意味着源代码管理部署只有在应用程序设置已完全更新后才会开始。 有关更多应用设置，请参阅 [Azure 应用服务中的环境变量和应用设置](reference-app-settings.md)。

## <a name="troubleshooting-key-vault-references"></a>排查 Key Vault 引用问题

如果未正常解析某个引用，将改用引用值。 这意味着，对于应用程序设置，将创建一个环境变量，其值采用 `@Microsoft.KeyVault(...)` 语法。 这可能导致应用程序引发错误，因为它需要特定结构的机密。

最常见的原因是 [Key Vault 访问策略](#granting-your-app-access-to-key-vault)配置不当。 但是，原因也可能是机密不再存在，或者引用本身存在语法错误。

如果语法正确，可以通过在门户中检查当前解析状态来查看其他错误原因。 导航到“应用程序设置”，然后选择有问题的引用对应的“编辑”。 在“设置配置”下面，应会看到状态信息，包括所有错误。 缺少这些信息意味着引用语法无效。

也可以使用某个内置检测程序来获取更多信息。

### <a name="using-the-detector-for-app-service"></a>使用应用服务的检测程序

1. 在门户中导航到你的应用。
2. 选择“诊断和解决问题”。
3. 依次选择“可用性和性能”、“Web 应用关闭”。 
4. 找到“Key Vault 应用程序设置诊断”，单击“更多信息”。 


### <a name="using-the-detector-for-azure-functions"></a>使用 Azure Functions 的检测程序

1. 在门户中导航到你的应用。
2. 导航到“平台功能”。
3. 选择“诊断和解决问题”。
4. 依次选择“可用性和性能”、“函数应用关闭或报告错误”。 
5. 单击“Key Vault 应用程序设置诊断”。
