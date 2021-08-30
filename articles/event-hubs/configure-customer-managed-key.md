---
title: 配置自己的密钥用于加密 Azure 事件中心静态数据
description: 本文介绍如何配置自己的密钥以用于加密 Azure 事件中心静态数据。
ms.topic: conceptual
ms.date: 05/04/2021
ms.openlocfilehash: cddeb2e11dc631e6eb9b43f6606d7ca4b41a6e35
ms.sourcegitcommit: b044915306a6275c2211f143aa2daf9299d0c574
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113032860"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>使用 Azure 门户配置客户管理的密钥以用于加密 Azure 事件中心静态数据
Azure 事件中心提供了通过 Azure 存储服务加密 (Azure SSE) 对静态数据进行加密的功能。 事件中心服务使用 Azure 存储来存储数据。 使用 Azure 存储存储的所有数据都使用 Microsoft 托管密钥进行加密。 如果你使用自己的密钥（也称为创建自己的密钥 (BYOK) 或客户管理的密钥），则仍使用 Microsoft 托管密钥对数据进行加密，但另外，将使用客户管理的密钥对 Microsoft 托管密钥进行加密。 使用此功能可以创建、轮换、禁用用于加密 Microsoft 托管密钥的客户管理的密钥，以及撤销对这些密钥的访问权限。 启用 BYOK 功能是在命名空间中执行的一次性设置过程。

> [!IMPORTANT]
> - 事件中心高级层和专用层支持 BYOK 功能 。
> - 只能为新的或空的命名空间启用加密。 如果命名空间包含事件中心，则加密操作将会失败。

可以使用 Azure Key Vault 管理密钥并审核密钥使用情况。 可以创建自己的密钥并将其存储在 Key Vault 中，或者使用 Azure Key Vault API 来生成密钥。 有关 Azure 密钥保管库的详细信息，请参阅[什么是 Azure 密钥保管库？](../key-vault/general/overview.md)

本文介绍了如何使用 Azure 门户配置包含客户管理的密钥的密钥保管库。 若要了解如何使用 Azure 门户创建 Key Vault，请参阅[快速入门：使用 Azure 门户创建 Azure Key Vault](../key-vault/general/quick-create-portal.md)。

> [!IMPORTANT]
> 为 Azure 事件中心使用客户管理的密钥需要为 Key Vault 配置两个必需的属性。 它们具有以下特点：“软删除”和“不清除”。 在 Azure 门户中创建新的 Key Vault 时，默认会启用这些属性。 但是，如果需要针对现有的 Key Vault 启用这些属性，必须使用 PowerShell 或 Azure CLI。

## <a name="enable-customer-managed-keys"></a>启用客户管理的密钥
若要在 Azure 门户中启用客户管理的密钥，请执行以下步骤。 如果使用的是专用层，请首先导航到事件中心专用层群集。

1. 选择要在其上启用 BYOK 的命名空间。
1. 在事件中心命名空间的“设置”页上，选择“加密”。  
1. 选择“客户管理的密钥加密(静态)”，如下图所示。 

    ![启用客户管理的密钥](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>设置密钥保管库与密钥
启用客户管理的密钥后，需要将客户管理的密钥关联到 Azure 事件中心命名空间。 事件中心仅支持 Azure Key Vault。 如果启用了上一部分所述的“使用客户管理的密钥进行加密”选项，则需要将密钥导入 Azure Key Vault。 此外，必须为密钥配置“软删除”和“不清除”。 可以使用 [PowerShell](../key-vault/general/key-vault-recovery.md) 或 [CLI](../key-vault/general/key-vault-recovery.md) 配置这些设置。

1. 若要创建新的密钥保管库，请遵循 Azure Key Vault [快速入门](../key-vault/general/overview.md)。 有关导入现有密钥的详细信息，请参阅[关于密钥、机密和证书](../key-vault/general/about-keys-secrets-certificates.md)。
1. 若要在创建保管库时启用“软删除”和“清除保护”，请使用 [az keyvault create](/cli/azure/keyvault#az_keyvault_create) 命令。

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. 若要向现有保管库（已启用“软删除”）添加“清除保护”，请使用 [az keyvault update](/cli/azure/keyvault#az_keyvault_update) 命令。

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. 遵循以下步骤创建密钥：
    1. 若要创建新密钥，请从“设置”  下的“密钥”  菜单中选择“生成/导入”  。
        
        ![选择“生成/导入”按钮](./media/configure-customer-managed-key/select-generate-import.png)
    1. 将“选项”  设置为“生成”  并提供密钥名称。

        ![创建密钥](./media/configure-customer-managed-key/create-key.png) 
    1. 现在，可以从下拉列表中选择要与事件中心命名空间关联以用于加密的密钥。 

        ![从密钥保管库中选择密钥](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. 填写密钥详细信息，然后单击“选择”。 这将允许使用密钥（客户管理的密钥）来加密 Microsoft 托管密钥。 


## <a name="rotate-your-encryption-keys"></a>轮换加密密钥
可以使用 Azure Key Vault 轮换机制来轮换密钥保管库中的密钥。 还可以设置激活和过期日期以自动轮换密钥。 事件中心服务将检测新密钥版本，并自动开始使用新版本。

## <a name="revoke-access-to-keys"></a>撤销对密钥的访问权限
撤销对加密密钥的访问权限不会从事件中心中清除数据。 但是，将无法从事件中心命名空间访问数据。 可以通过使用访问策略或删除密钥来撤销加密密钥。 在[保护对密钥保管库的访问](../key-vault/general/security-features.md)中详细了解访问策略以及如何保护密钥保管库。

撤销加密密钥后，已加密的命名空间中的事件中心服务将无法正常运行。 如果启用了对密钥的访问或者还原了已删除的密钥，则事件中心服务将选取密钥，使你能够从已加密的事件中心命名空间访问数据。

## <a name="set-up-diagnostic-logs"></a>设置诊断日志 
为启用了 BYOK 的命名空间设置诊断日志可提供有关操作所需的信息。 可以启用这些日志，稍后将其流式传输到事件中心，或通过 log analytics 进行分析或流式传输到存储，以执行自定义分析。 要了解有关诊断日志的详细信息，请参阅 [Azure 诊断日志概述](../azure-monitor/essentials/platform-logs-overview.md)。

## <a name="enable-user-logs"></a>启用用户日志
按照以下步骤为客户管理的密钥启用日志。

1. 在 Azure 门户中，导航到已启用 BYOK 的命名空间。
1. 在“监视”下，选择“诊断设置” 。

    ![选择“诊断设置”](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. 选择“+ 添加诊断设置”。 

    ![选择“添加诊断设置”](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. 提供一个“名称”，并选择要将日志流式传输到的位置。
1. 选择“CustomerManagedKeyUserLogs”并单击“保存”。  此操作可在命名空间上启用 BYOK 的日志。

    ![选择“客户托管的密钥用户日志”选项](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>日志架构 
所有日志均以 JavaScript 对象表示法 (JSON) 格式存储。 每个条目均包含字符串字段，这些字段采用下表所述的格式。 

| 名称 | 说明 |
| ---- | ----------- | 
| TaskName | 失败的任务的说明。 |
| ActivityId | 用于跟踪的内部 ID。 |
| category | 定义任务的分类。 例如，如果密钥保管库中的密钥处于禁用状态，则它将是信息类别；如果密钥无法展开，则可能发生错误。 |
| ResourceId | Azure Resource Manager 资源 ID |
| keyVault | 密钥保管库的名称。 |
| key | 用于加密事件中心命名空间的密钥名称。 |
| 版本 | 所使用的密钥的版本。 |
| operation | 对密钥保管库中的密钥执行的操作。 例如，禁用/启用密钥、包装或展开 |
| code | 与操作关联的代码。 示例：错误代码 404 表示找不到密钥。 |
| message | 与操作关联的任何错误消息 |

下面是客户管理的密钥的日志示例：

```json
{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111-1111-1111-1111-111111111111",
   "category": "error"
   "resourceId": "/SUBSCRIPTIONS/11111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "1111111111111111111111111111111",
   "operation": "wrapKey",
   "code": "404",
   "message": "Key not found: ehbyok0/111111111111111111111111111111",
}



{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111111111-1111-1111-1111111111111",
   "category": "info"
   "resourceId": "/SUBSCRIPTIONS/111111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "111111111111111111111111111111",
   "operation": "disable" | "restore",
   "code": "",
   "message": "",
}
```

## <a name="use-resource-manager-template-to-enable-encryption"></a>使用资源管理器模板启用加密
本部分介绍了如何使用 **Azure 资源管理器模板** 执行以下任务。 

1. 使用托管服务标识创建“事件中心命名空间”。
2. 创建 **密钥保管库** 并向服务标识授予对密钥保管库的访问权限。 
3. 使用密钥保管库信息（密钥/值）更新事件中心命名空间。 


### <a name="create-an-event-hubs-cluster-and-namespace-with-managed-service-identity"></a>使用托管服务标识创建事件中心群集和命名空间
本部分介绍了如何使用 Azure 资源管理器模板和 PowerShell 创建具有托管服务标识的 Azure 事件中心命名空间。 

1. 创建 Azure 资源管理器模板，以创建具有托管服务标识的事件中心命名空间。 为文件命名：CreateEventHubClusterAndNamespace.json： 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/clusters",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('clusterName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Dedicated",
                "capacity":1
             }
          },
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             },
             "dependsOn":[
                "[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             ]
          }
       ],
       "outputs":{
          "EventHubNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.EventHub/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. 创建一个模板参数文件，名为：CreateEventHubClusterAndNamespaceParams.json。 

    > [!NOTE]
    > 请替换以下值： 
    > - `<EventHubsClusterName>` - 事件中心群集的名称    
    > - `<EventHubsNamespaceName>` - 事件中心命名空间的名称
    > - `<Location>` - 事件中心命名空间的位置

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    
    ```
3. 运行以下 PowerShell 命令以部署用于创建事件中心命名空间的模板。 然后检索该事件中心命名空间的 ID 供稍后使用。 在运行该命令之前，请将 `{MyRG}` 替换为资源组的名称。  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateEventHubClusterAndNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateEventHubClusterAndNamespace.json -TemplateParameterFile ./CreateEventHubClusterAndNamespaceParams.json

    $EventHubNamespaceId = $outputs.Outputs["eventHubNamespaceId"].value
    ```
 
### <a name="grant-event-hubs-namespace-identity-access-to-key-vault"></a>向事件中心命名空间标识授予对密钥保管库的访问权限

1. 运行以下命令，以便在启用 **清除保护** 和 **软删除** 的情况下创建密钥保管库。 

    ```powershell
    New-AzureRmKeyVault -Name {keyVaultName} -ResourceGroupName {RGName}  -Location {location} -EnableSoftDelete -EnablePurgeProtection    
    ```     
    
    （或者）    
    
    运行以下命令来更新 **现有的密钥保管库**。 在运行该命令之前，请指定资源组和密钥保管库名称的值。 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. 设置密钥保管库访问策略，使事件中心命名空间的托管标识可以访问密钥保管库中的密钥值。 使用在上一部分创建的事件中心命名空间的 ID。 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $EventHubNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-event-hubs-namespace-with-customer-managed-key-from-key-vault"></a>使用密钥保管库中客户管理的密钥加密事件中心命名空间中的数据
到目前为止，你已完成以下步骤： 

1. 创建了具有托管标识的高级命名空间。
2. 创建了密钥保管库，并向托管标识授予了对该密钥保管库的访问权限。 

在此步骤中，将使用密钥保管库信息更新事件中心命名空间。 

1. 创建包含以下内容的名为 CreateEventHubClusterAndNamespace.json 的 JSON 文件： 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          },
          "keyVaultUri":{
             "type":"string",
             "metadata":{
                "description":"URI of the KeyVault."
             }
          },
          "keyName":{
             "type":"string",
             "metadata":{
                "description":"KeyName."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]",
                "encryption":{
                   "keySource":"Microsoft.KeyVault",                   
                   "keyVaultProperties":[
                      {
                         "keyName":"[parameters('keyName')]",
                         "keyVaultUri":"[parameters('keyVaultUri')]"
                      }
                   ]
                }
             }
          }
       ]
    }
    ``` 

2. 创建一个模板参数文件：UpdateEventHubClusterAndNamespaceParams.json。 

    > [!NOTE]
    > 请替换以下值： 
    > - `<EventHubsClusterName>` - 事件中心群集的名称。        
    > - `<EventHubsNamespaceName>` - 事件中心命名空间的名称
    > - `<Location>` - 事件中心命名空间的位置
    > - `<KeyVaultName>` - 你的密钥保管库的名称
    > - `<KeyName>` - 密钥保管库中密钥的名称

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          },
          "keyName":{
             "value":"<KeyName>"
          },
          "keyVaultUri":{
             "value":"https://<KeyVaultName>.vault.azure.net"
          }
       }
    }
    ```             
3. 运行以下 PowerShell 命令以部署资源管理器模板。 在运行该命令之前，请将 `{MyRG}` 替换为你的资源组名称。 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateEventHubNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateEventHubClusterAndNamespace.json -TemplateParameterFile ./UpdateEventHubClusterAndNamespaceParams.json 
    ```

#### <a name="enable-infrastructure-encryption-for-double-encryption-of-data-in-event-hubs-namespace"></a>在事件中心命名空间启用基础结构加密，对数据进行双重加密
如果需要更高级别的数据安全保证，则可以启用基础结构级别的加密（也称为双重加密）。 

启用基础结构加密后，将对事件中心命名空间中的数据进行两次加密，分别在服务级别和基础架构级别使用两种不同的加密算法和两个不同的密钥。 因此，事件中心数据的基础结构加密可以在其中一种加密算法或密钥可能受到安全威胁的情况下提供保护。

可以通过使用上述 CreateEventHubClusterAndNamespace.json 中的 `requireInfrastructureEncryption` 属性更新 ARM 模板来启用基础架构加密，如下所示。 

```json
"properties":{
   "isAutoInflateEnabled":false,
   "maximumThroughputUnits":0,
   "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]",
   "encryption":{
      "keySource":"Microsoft.KeyVault",
      "requireInfrastructureEncryption":true,
      "keyVaultProperties":[
         {
            "keyName":"[parameters('keyName')]",
            "keyVaultUri":"[parameters('keyVaultUri')]"
         }
      ]
   }
}
```

## <a name="troubleshoot"></a>故障排除
最佳做法是始终启用日志，如前一部分中所示。 启用 BYOK 加密后，它有助于跟踪活动。 它还有助于限制问题的范围。

下面是启用 BYOK 加密时要查找的常见错误代码。

| 操作 | 错误代码 | 数据的生成状态 |
| ------ | ---------- | ----------------------- | 
| 从密钥保管库中删除包装/展开权限 | 403 |    Inaccessible |
| 从授予了包装/展开权限的 AAD 主体中删除 AAD 角色成员身份 | 403 |  Inaccessible |
| 从密钥保管库中删除加密密钥 | 404 | Inaccessible |
| 删除密钥保管库 | 404 | 无法访问（假设启用软删除，这是必需的设置。） |
| 更改加密密钥的过期期限，使其已过期 | 403 |   Inaccessible  |
| 更改 NBF（不早于），使密钥加密密钥不会处于活动状态 | 403 | Inaccessible  |
| 为密钥保管库防火墙选择“允许 MSFT 服务”选项，或阻止对具有加密密钥的密钥保管库进行网络访问 | 403 | Inaccessible |
| 将密钥保管库移到其他租户 | 404 | Inaccessible |  
| 间歇性网络问题或 DNS/AAD/MSI 中断 |  | 可使用缓存数据加密密钥进行访问 |

> [!IMPORTANT]
> 要在使用 BYOK 加密的命名空间上启用异地灾难恢复，用于配对的辅助命名空间必须在专用群集中，并且必须在该群集上启用系统分配的托管标识。 要了解详细信息，请参阅 [Azure 资源的托管标识](../active-directory/managed-identities-azure-resources/overview.md)。

## <a name="next-steps"></a>后续步骤
请参阅以下文章：
- [事件中心概述](event-hubs-about.md)
- [Key Vault 概述](../key-vault/general/overview.md)