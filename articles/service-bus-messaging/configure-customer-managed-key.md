---
title: 配置自己的密钥以用于加密 Azure 服务总线静态数据
description: 本文介绍了如何配置自己的密钥以用于加密 Azure 服务总线静态数据。
ms.topic: conceptual
ms.date: 02/10/2021
ms.openlocfilehash: 58ce3559a9524abdd3391f9d663651c58a20144d
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122514759"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest"></a>配置客户管理的密钥以加密 Azure 服务总线静态数据
Azure 服务总线高级层提供了通过 Azure 存储服务加密 (Azure SSE) 对静态数据进行加密的功能。 服务总线高级层使用 Azure 存储来存储数据。 使用 Azure 存储存储的所有数据都使用 Microsoft 托管密钥进行加密。 如果你使用自己的密钥（也称为创建自己的密钥 (BYOK) 或客户管理的密钥），则仍使用 Microsoft 托管密钥对数据进行加密，但另外，将使用客户管理的密钥对 Microsoft 托管密钥进行加密。 使用此功能可以创建、轮换、禁用用于加密 Microsoft 托管密钥的客户管理的密钥，以及撤销对这些密钥的访问权限。 启用 BYOK 功能是在命名空间中执行的一次性设置过程。

对于用于服务端加密的客户管理密钥，需要注意一些事项。 
- [Azure 服务总线高级层](service-bus-premium-messaging.md)支持此功能。 不能为标准层服务总线命名空间启用此功能。
- 只能为新的或空的命名空间启用加密。 如果命名空间包含任何队列或主题，则加密操作将会失败。

可以使用 Azure Key Vault 管理密钥并审核密钥使用情况。 可以创建自己的密钥并将其存储在 Key Vault 中，或者使用 Azure Key Vault API 来生成密钥。 有关 Azure 密钥保管库的详细信息，请参阅[什么是 Azure 密钥保管库？](../key-vault/general/overview.md)

## <a name="enable-customer-managed-keys-azure-portal"></a>启用客户管理的密钥（Azure 门户）
若要在 Azure 门户中启用客户管理的密钥，请执行以下步骤：

1. 导航到你的服务总线高级层命名空间。
2. 在你的服务总线命名空间的“设置”页上，选择“加密”。
3. 选择“客户管理的密钥加密(静态)”，如下图所示。

    ![启用客户管理的密钥](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>设置密钥保管库与密钥

启用客户管理的密钥后，需要将客户管理的密钥关联到 Azure 服务总线命名空间。 服务总线仅支持 Azure Key Vault。 如果启用了上一部分所述的“使用客户管理的密钥进行加密”选项，则需要将密钥导入 Azure Key Vault。 此外，必须为密钥配置“软删除”和“不清除”。 可以使用 [PowerShell](../key-vault/general/key-vault-recovery.md) 或 [CLI](../key-vault/general/key-vault-recovery.md) 配置这些设置。

1. 若要创建新的密钥保管库，请遵循 Azure Key Vault [快速入门](../key-vault/general/overview.md)。 有关导入现有密钥的详细信息，请参阅[关于密钥、机密和证书](../key-vault/general/about-keys-secrets-certificates.md)。

    > [!IMPORTANT]
    > 为 Azure 服务总线使用客户管理的密钥需要为密钥保管库配置两个必需的属性。 它们具有以下特点：“软删除”和“不清除”。 在 Azure 门户中创建新的 Key Vault 时，默认会启用这些属性。 但是，如果需要针对现有的 Key Vault 启用这些属性，必须使用 PowerShell 或 Azure CLI。
1. 若要在创建保管库时启用“软删除”和“清除保护”，请使用 [az keyvault create](/cli/azure/keyvault#az_keyvault_create) 命令。

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. 若要向现有保管库（已启用“软删除”）添加“清除保护”，请使用 [az keyvault update](/cli/azure/keyvault#az_keyvault_update) 命令。

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. 遵循以下步骤创建密钥：
    1. 若要创建新密钥，请从“设置”  下的“密钥”  菜单中选择“生成/导入”  。
        
        ![选择“生成/导入”按钮](./media/configure-customer-managed-key/select-generate-import.png)

    1. 将“选项”  设置为“生成”  并提供密钥名称。

        ![创建密钥](./media/configure-customer-managed-key/create-key.png) 

    1. 现在，可以从下拉列表中选择要与服务总线命名空间关联的用于加密的密钥。 

        ![从密钥保管库中选择密钥](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > 最多可以添加 3 个密钥来实现冗余。 如果某个密钥已过期或不可访问，则会使用其他密钥进行加密。
        
    1. 填写密钥详细信息，然后单击“选择”。 这将允许使用密钥（客户管理的密钥）来加密 Microsoft 托管密钥。 


    > [!IMPORTANT]
    > 如果你想将客户管理的密钥用于异地灾难恢复，请查看本节。 
    >
    > 为了将客户管理的密钥用于 Microsoft 管理的密钥的加密，已在指定的 Azure KeyVault 上为服务总线托管标识设置了一个[访问策略](../key-vault/general/security-features.md)。 这可确保能够控制从 Azure 服务总线命名空间对 Azure KeyVault 的访问。
    >
    > 因此：
    > 
    >   * 如果已经为服务总线命名空间启用了[异地灾难恢复](service-bus-geo-dr.md)，并且你想要启用客户管理的密钥，请执行以下操作： 
    >     * 断开配对
    >     * 为密钥保管库[设置针对主要和辅助命名空间的托管标识的访问策略](../key-vault/general/assign-access-policy-portal.md)。
    >     * 在主要命名空间上设置加密。
    >     * 将主要和辅助命名空间重新配对。
    > 
    >   * 如果想要对已设置客户管理的密钥的服务总线命名空间启用异地灾难恢复，请执行以下操作：
    >     * 为密钥保管库[设置针对辅助命名空间的托管标识的访问策略](../key-vault/general/assign-access-policy-portal.md)。
    >     * 将主要和辅助命名空间配对。

## <a name="managed-identities"></a>托管标识
有两种类型的托管标识可用于分配给服务总线命名空间。

- 系统分配的标识：可以直接在服务总线命名空间中启用托管标识。 在启用系统分配的托管标识时，系统会在 Azure AD 中创建一个与该服务总线命名空间的生命周期相关联的标识。 因此，命名空间被删除时，Azure 会自动删除此标识。 按照设计，只有该 Azure 资源（命名空间）可以使用此标识从 Azure AD 请求令牌。
- 用户分配的标识：也可以将托管标识创建为独立的 Azure 资源，称为用户分配的标识。 可以创建用户分配的托管标识，并将其分配给一个或多个服务总线命名空间。 对于用户分配的托管标识，标识与使用它的资源分开管理。 它们不与命名空间的生命周期相关联。 可以显式删除不再需要的用户分配的标识。     

    有关详细信息，请参阅[什么是 Azure 资源的托管标识？](../active-directory/managed-identities-azure-resources/overview.md)。

## <a name="encrypt-using-system-assigned-identities-template"></a>使用系统分配的标识（模板）进行加密
本节介绍如何执行以下任务： 

1. 创建具有 **托管服务标识** 的 **高级** 服务总线命名空间。
2. 创建 **密钥保管库** 并向服务标识授予对密钥保管库的访问权限。 
3. 使用密钥保管库信息（密钥/值）更新服务总线命名空间。 

### <a name="create-a-premium-service-bus-namespace-with-managed-service-identity"></a>创建具有托管服务标识的高级服务总线命名空间
本部分介绍了如何使用 Azure 资源管理器模板和 PowerShell 创建具有托管服务标识的 Azure 服务总线命名空间。 

1. 创建 Azure 资源管理器模板，以创建具有托管服务标识的服务总线高级层命名空间。 将文件命名为：**CreateServiceBusPremiumNamespace.json**： 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace."
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
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
    
             }
          }
       ],
       "outputs":{
          "ServiceBusNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.ServiceBus/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. 创建一个模板参数文件，名为：**CreateServiceBusPremiumNamespaceParams.json**。 

    > [!NOTE]
    > 请替换以下值： 
    > - `<ServiceBusNamespaceName>` - 你的服务总线命名空间的名称
    > - `<Location>` - 你的服务总线命名空间的位置

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    ```
3. 运行以下 PowerShell 命令以部署用于创建高级服务总线命名空间的模板。 然后检索该服务总线命名空间的 ID 供稍后使用。 在运行该命令之前，请将 `{MyRG}` 替换为资源组的名称。  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateServiceBusPremiumNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateServiceBusPremiumNamespace.json -TemplateParameterFile ./CreateServiceBusPremiumNamespaceParams.json
    
    $ServiceBusNamespaceId = $outputs.Outputs["serviceBusNamespaceId"].value
    ```
 
### <a name="grant-service-bus-namespace-identity-access-to-key-vault"></a>向服务总线命名空间标识授予对密钥保管库的访问权限

1. 运行以下命令，以便在启用 **清除保护** 和 **软删除** 的情况下创建密钥保管库。 

    ```powershell
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName}  -Location "{location}" -EnableSoftDelete -EnablePurgeProtection    
    ```
    
    （或者）
    
    运行以下命令来更新 **现有的密钥保管库**。 在运行该命令之前，请指定资源组和密钥保管库名称的值。 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. 设置密钥保管库访问策略，使服务总线命名空间的托管标识可以访问密钥保管库中的密钥值。 使用在上一部分创建的服务总线命名空间的 ID。 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $ServiceBusNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-service-bus-namespace-with-customer-managed-key-from-key-vault"></a>使用密钥保管库中客户管理的密钥加密服务总线命名空间中的数据 
到目前为止，你已完成以下步骤： 

1. 创建了具有托管标识的高级命名空间。
2. 创建了密钥保管库，并向托管标识授予了对该密钥保管库的访问权限。 

在此步骤中，你将使用密钥保管库信息更新服务总线命名空间。 

1. 创建名为 **UpdateServiceBusNamespaceWithEncryption.json** 的 JSON 文件，其中包含以下内容： 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
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
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
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

2. 创建模板参数文件：**UpdateServiceBusNamespaceWithEncryptionParams.json**。

    > [!NOTE]
    > 请替换以下值： 
    > - `<ServiceBusNamespaceName>` - 你的服务总线命名空间的名称
    > - `<Location>` - 你的服务总线命名空间的位置
    > - `<KeyVaultName>` - 你的密钥保管库的名称
    > - `<KeyName>` - 密钥保管库中密钥的名称  

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
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
    New-AzResourceGroupDeployment -Name UpdateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateServiceBusNamespaceWithEncryption.json -TemplateParameterFile ./UpdateServiceBusNamespaceWithEncryptionParams.json
    ```    

## <a name="encrypt-using-user-assigned-identities-template"></a>使用用户分配的标识（模板）进行加密

1. 创建用户分配的标识。
1. 创建密钥保管库，并通过访问策略向用户分配的标识授予访问权限。
1. 使用托管用户标识和密钥保管库信息创建高级服务总线命名空间。

### <a name="create-a-user-assigned-identity"></a>创建用户分配的标识
按照[创建用户分配的托管标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)一文中的说明创建用户分配的标识。 也可使用 [CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)、[PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)、[Azure 资源管理器模板](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)和 [REST](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md) 创建用户分配的标识。 

> [!NOTE]
> 最多可以将 4 个用户标识分配给命名空间。 删除命名空间或将模板中的 `identity -> type` 传递到 `None` 时，将删除这些关联。 

### <a name="create-a-key-vault-and-grant-access-to-user-assigned-identity"></a>创建密钥保管库并授予对用户分配标识的访问权限 

1. 运行以下命令，以便在启用清除保护和软删除的情况下创建密钥保管库。

    ```azurepowershell-interactive
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName} -Location "{location}" -EnableSoftDelete -EnablePurgeProtection           
    ```
    
    （或者）

    运行以下命令来更新现有的密钥保管库。 在运行该命令之前，请指定资源组和密钥保管库名称的值。

    ```azurepowershell-interactive
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force            
    ```
2. 使用以下 PowerShell 命令获取用户标识的服务主体 ID。 在示例中，`ud1` 是用户分配的标识，用于加密。

    ```azurepowershell-interactive
    $servicePrincipal=Get-AzADServicePrincipal -SearchString "ud1"    
    ```
3. 通过分配访问策略，向用户分配的标识授予对密钥保管库的访问权限。     

    ```azurepowershell-interactive
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $servicePrincipal.Id -PermissionsToKeys get,wrapKey,unwrapKey,list    
    ```

    > [!NOTE]
    > 最多可以添加 3 个密钥，但对于所有密钥，用于加密的用户标识应相同。 目前，仅支持单个加密标识。

### <a name="create-a-premium-service-bus-namespace-with-user-identity-and-key-vault-information"></a>使用用户标识和密钥保管库信息创建高级服务总线命名空间
本节举例说明如何使用 Azure 资源管理器模板执行以下任务。 

- 将用户托管标识分配给服务总线命名空间。

    ```json
                "identity": {
                    "type": "UserAssigned",
                    "userAssignedIdentities": {
                        "[parameters('identity').userAssignedIdentity]": {}
                    }
                },
    ```    
- 通过指定密钥保管库中的密钥和用于访问该密钥的用户托管标识，对命名空间启用加密。 

    ```json
                    "encryption":{
                       "keySource":"Microsoft.KeyVault",
                       "keyVaultProperties":[
                            {
                                "keyName": "[parameters('keyName')]",
                                "keyVaultUri": "[parameters('keyVaultUri')]",
                                "identity": {
                                    "userAssignedIdentity": "[parameters('identity').userAssignedIdentity]"
                                }
                            }
                       ]
                    }
    ```
   

1. 使用以下内容创建一个名为 CreateServiceBusNamespaceWithUserIdentityAndEncryption.json 的 JSON 文件：

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
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
             },
         "identity": {
            "type": "Object",
            "defaultValue": {
                "userAssignedIdentity": ""
            },
            "metadata": {
                "description": "user-assigned identity."
            }
         }
       },
       "resources":[
          {
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2021-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
            "identity": {
                "type": "UserAssigned",
                "userAssignedIdentities": {
                    "[parameters('identity').userAssignedIdentity]": {}
                }
            },
             "properties":{
                "encryption":{
                   "keySource":"Microsoft.KeyVault",
                   "keyVaultProperties":[
                        {
                            "keyName": "[parameters('keyName')]",
                            "keyVaultUri": "[parameters('keyVaultUri')]",
                            "identity": {
                                "userAssignedIdentity": "[parameters('identity').userAssignedIdentity]"
                            }
                        }
                   ]
                }
             }
          }
       ]
    }        
    ```  
1. 创建一个模板参数文件：CreateServiceBusNamespaceWithUserIdentityAndEncryptionParams.json。

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          },
          "keyVaultUri":{
             "value":"https://<KeyVaultName>.vault.azure.net"
          },
          "keyName":{
             "value":"<KeyName>"
          },
          "identity": {
            "value": {
                "userAssignedIdentity": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER MANAGED IDENTITY NAME>"
            }
         }
       }
    }
    ```

    在参数文件中，将占位符替换为相应的值。
    
    | 占位符 | 值 | 
    | ----------- | ----- | 
    | `<ServiceBusNamespaceName>` | 服务总线命名空间的名称。 | 
    | `<Location>` | 要创建命名空间的位置。 | 
    | `<KeyVaultName>` | 密钥保管库的名称。 | 
    | `<KeyName>` | 密钥保管库中密钥的名称。 | 
    | `<AZURE SUBSCRIPTION ID>` | Azure 订阅 ID。 |
    | `<RESOURCE GROUP NAME>` | 用户托管标识的资源组。 | 
    | `<USER MANAGED IDENTITY NAME>` | 用户托管标识的名称。 | 

3. 运行以下 PowerShell 命令以部署资源管理器模板。 在运行该命令之前，请将 `{MyRG}` 替换为你的资源组名称。

    ```azurepowershell-interactive
    New-AzResourceGroupDeployment -Name CreateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./ CreateServiceBusNamespaceWithUserIdentityAndEncryption.json -TemplateParameterFile ./ CreateServiceBusNamespaceWithUserIdentityAndEncryptionParams.json        
    ```


## <a name="use-both-user-assigned-and-system-assigned-identities"></a>同时使用用户分配的标识和系统分配的标识
一个命名空间可同时具有系统分配的标识和用户分配的标识。 在这种情况下，`type` 属性将为 `SystemAssigned`、`UserAssigned`，如下例所示。 

```json
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userIdentity1>" : {}
    }
}
```

在此方案中，可以选择系统分配的标识或用户分配的标识来加密静态数据。  

在资源管理器模板中，如果未指定 `identity` 属性，则使用系统托管标识。 下面是一个示例片段。 

```json
"properties":{
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
```

通过以下示例了解如何使用用户托管标识进行加密。 请注意，`identity` 属性设置为用户托管标识。 

```json
"properties":{
   "encryption":{
      "keySource":"Microsoft.KeyVault",
      "keyVaultProperties":[
         {
            "keyName":"[parameters('keyName')]",
            "keyVaultUri":"[parameters('keyVaultUri')]",
            "identity": {
                "userAssignedIdentity": "[parameters('identity').userAssignedIdentity]"
            }
         }
      ]
   }
}
```

## <a name="enable-infrastructure-double-encryption-of-data"></a>对数据启用基础结构（双重）加密
如果需要更高级别的数据安全保证，则可以启用基础结构级别的加密（也称为双重加密）。 

启用基础结构加密后，将对 Azure 服务总线中的数据进行两次加密，分别在服务级别和基础架构级别使用两种不同的加密算法和两个不同的密钥。 因此，Azure 服务总线数据的基础结构加密可以在其中一种加密算法或密钥可能泄露的情况下提供保护。

可以通过使用上述 UpdateServiceBusNamespaceWithEncryption.json 中的 `requireInfrastructureEncryption` 属性更新 Azure 资源管理器模板来启用基础结构加密，如下所示。 

```json
"properties":{
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

## <a name="rotate-revoke-and-cache-keys"></a>轮换、撤销和缓存密钥

### <a name="rotate-your-encryption-keys"></a>轮换加密密钥

可以使用 Azure Key Vault 轮换机制来轮换密钥保管库中的密钥。 还可以设置激活和过期日期以自动轮换密钥。 服务总线服务将检测新密钥版本，并自动开始使用新版本。

### <a name="revoke-access-to-keys"></a>撤销对密钥的访问权限

撤销对加密密钥的访问权限不会从服务总线中清除数据。 但是，将无法从服务总线命名空间访问数据。 可以通过使用访问策略或删除密钥来撤销加密密钥。 在[保护对密钥保管库的访问](../key-vault/general/secure-your-key-vault.md)中详细了解访问策略以及如何保护密钥保管库。

撤销加密密钥后，已加密的命名空间中的服务总线服务将无法正常运行。 如果启用了对密钥的访问或者还原了已删除的密钥，则服务总线服务将选取密钥，使你能够从已加密的服务总线命名空间访问数据。

### <a name="caching-of-keys"></a>缓存密钥
服务总线实例每 5 分钟轮询一次列出的加密密钥。 服务总线将缓存加密密钥并使用，直到下一次轮询（5 分钟后）。 只要至少有一个加密密钥可用，就可以访问队列和主题。 如果所有列出的密钥在轮询时均无法访问，则所有队列和主题都将变为不可用。 

下面是更多详细信息： 

- 服务总线服务每隔 5 分钟就会轮询命名空间记录中列出的所有由客户管理的密钥：
    - 如果已经旋转了某个密钥，则使用新密钥更新该记录。
    - 如果密钥已被撤销，则从记录中删除该密钥。
    - 如果所有密钥均被撤销，则会将命名空间的加密状态设置为“已撤销”。 将无法从服务总线命名空间访问数据。 

## <a name="considerations-when-using-geo-disaster-recovery"></a>异地灾难恢复使用注意事项

### <a name="geo-disaster-recovery---encryption-with-system-assigned-identities"></a>异地灾难恢复 - 使用系统分配的标识进行加密
为了将客户管理的密钥用于 Microsoft 管理的密钥的加密，已在指定的 Azure KeyVault 上为系统分配的托管标识设置[访问策略](../key-vault/general/secure-your-key-vault.md)。 这可确保能够控制从 Azure 服务总线命名空间对 Azure KeyVault 的访问。

因此：

- 如果已经为服务总线命名空间启用了[异地灾难恢复](service-bus-geo-dr.md)，并且你想要启用客户管理的密钥，请执行以下操作：
    - 断开配对。
    - 为密钥保管库主命名空间和辅助命名空间的系统分配的托管标识[设置访问策略](../key-vault/general/assign-access-policy-portal.md)。
    - 在主要命名空间上设置加密。
    - 将主要和辅助命名空间重新配对。
- 如果想要对已设置客户管理的密钥的服务总线命名空间启用异地灾难恢复，请执行以下步骤： 
    - 为密钥保管库[设置针对辅助命名空间的托管标识的访问策略](../key-vault/general/assign-access-policy-portal.md)。
    - 将主要和辅助命名空间配对。

### <a name="geo-disaster-recovery---encryption-with-user-assigned-identities"></a>异地灾难恢复 - 使用用户分配的标识进行加密
以下是一些建议： 

1.  创建托管标识，并向托管标识分配密钥保管库权限。 
2.  将该标识添加为用户分配的标识，并启用两个命名空间上的标识加密。 
3.  将命名空间配对在一起 

启用异地灾难恢复和使用用户分配的标识加密的条件：

1.  如果辅助命名空间要与启用加密的主命名空间配对，则必须已使用用户分配的标识启用加密。 
2.  即使辅助命名空间具有与已配对的主命名空间关联的用户分配的标识，也无法在此主命名空间上启用加密。
    

## <a name="next-steps"></a>后续步骤
请参阅以下文章：
- [服务总线概述](service-bus-messaging-overview.md)
- [Key Vault 概述](../key-vault/general/overview.md)
