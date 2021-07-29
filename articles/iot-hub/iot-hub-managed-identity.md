---
title: Azure IoT 中心托管标识 | Microsoft Docs
description: 如何使用托管标识允许从 IoT 中心到其他 Azure 资源的出口连接。
author: miag
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/11/2021
ms.author: miag
ms.openlocfilehash: 2dde5858cef4b7c8fa876e4437c4b89c4125a0d0
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110068910"
---
# <a name="iot-hub-support-for-managed-identities"></a>IoT 中心对托管标识的支持 

托管标识可在 Azure Active Directory (AD) 中以安全方式为 Azure 服务提供一个自动托管标识。 这样，开发人员在管理凭据时便无需提供标识。 有两种类型的托管标识：系统分配和用户分配的托管标识。 IoT 中心支持上述这两者。 

在 IoT 中心，托管标识可用于从 IoT 中心到其他 Azure 服务的出口连接，提供[消息路由](iot-hub-devguide-messages-d2c.md)、[文件上传](iot-hub-devguide-file-upload.md)和[批量设备导入/导出](iot-hub-bulk-identity-mgmt.md)等功能。 本文介绍了如何在 IoT 中心将系统分配的托管标识和用户分配的托管标识用于不同的功能。 


## <a name="prerequisites"></a>先决条件
- 阅读 [Azure 资源的托管标识](./../active-directory/managed-identities-azure-resources/overview.md)文档，了解系统分配的托管标识与用户分配的托管标识之间的差异。

- 如果没有 IoT 中心，请先创建[一个](iot-hub-create-through-portal.md)，然后再继续。


## <a name="system-assigned-managed-identity"></a>系统分配的托管标识 

### <a name="add-and-remove-a-system-assigned-managed-identity-in-azure-portal"></a>在 Azure 门户中添加和删除系统分配的托管标识
1.  登录 Azure 门户并导航到需要的 IoT 中心。
2.  在 IoT 中心门户中导航到“标识”
3.  在“系统分配”选项卡下，选择“打开”并单击“保存”。  
4.  若要从 IoT 中心删除系统分配的托管标识，请选择“关闭”，然后单击“保存”。 

    :::image type="content" source="./media/iot-hub-managed-identity/system-assigned.png" alt-text="显示在何处为 IoT 中心启用系统分配的托管标识的屏幕截图":::        

### <a name="enable-system-assigned-managed-identity-at-hub-creation-time-using-arm-template"></a>使用 ARM 模板在中心创建时启用系统分配的托管标识

若要在资源预配时在 IoT 中心启用系统分配的托管标识，请使用下面的 Azure 资源管理器 (ARM) 模板。 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": 
    {
      "iotHubName": {
        "type": "string",
        "metadata": {
          "description": "Name of iothub resource"
        }
      },
      "skuName": {
        "type": "string",
        "defaultValue": "S1",
        "metadata": {
          "description": "SKU name of iothub resource, by default is Standard S1"
        }
      },
      "skuTier": {
        "type": "string",
        "defaultValue": "Standard",
        "metadata": {
          "description": "SKU tier of iothub resource, by default is Standard"
        }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location of iothub resource. Please provide any of supported-regions of iothub"
        }
      }
    },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "createIotHub",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Devices/IotHubs",
              "apiVersion": "2021-03-31",
              "name": "[parameters('iotHubName')]",
              "location": "[parameters('location')]",
              "identity": {
                "type": "SystemAssigned"
              },
              "sku": {
              "name": "[parameters('skuName')]",
              "tier": "[parameters('skuTier')]",
              "capacity": 1
              }
            }
          ] 
        }
      }
    }
  ]
}
```

替换资源 `name`、`location`、`SKU.name` 和 `SKU.tier` 的值后，可以在 Azure CLI 中使用以下命令在现有资源组中部署资源：

```azurecli-interactive
az deployment group create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json> --parameters iotHubName=<valid-iothub-name> skuName=<sku-name> skuTier=<sku-tier> location=<any-of-supported-regions>
```

创建资源后，可以使用 Azure CLI 检索分配给中心的系统分配服务标识：

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```
## <a name="user-assigned-managed-identity"></a>用户分配的托管标识 
本部分介绍如何使用 Azure 门户在 VM 中添加和删除用户分配的托管标识。
1.  首先，需要将用户分配的托管标识创建为独立资源。 可以按照[此处](./../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)的说明创建用户分配的托管标识。
2.  转到 IoT 中心，导航到 IoT 中心门户中的“标识”。
3.  在“用户分配”选项卡下，单击“添加用户分配的托管标识”。  选择要添加到中心的用户分配的托管标识，然后单击“选择”。 
4.  可以从 IoT 中心中删除用户分配的标识。 选择要删除的用户分配的标识，然后单击“删除”按钮。 请注意，仅从 IoT 中心删除标识，并且此删除操作不会将用户分配的标识作为资源删除。 若要将用户分配的标识作为资源删除，请按照[此处](./../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#delete-a-user-assigned-managed-identity)的说明进行操作。

    :::image type="content" source="./media/iot-hub-managed-identity/user-assigned.png" alt-text="显示如何为 IoT 中心添加用户分配的托管标识的屏幕截图":::        


### <a name="enable-user-assigned-managed-identity-at-hub-creation-time-using-arm-template"></a>使用 ARM 模板在创建 IoT 中心时启用用户分配的托管标识
可以使用下面这个示例模板通过用户分配的托管标识来创建中心。 此模板创建一个用户分配的标识，其名称为 [iothub]-identity 并被分配给已创建的 IoT 中心。 你可以更改模板以根据需要添加多个用户分配的标识。
 
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "iotHubName": {
      "type": "string",
      "metadata": {
        "description": "Name of iothub resource"
      }
    },
  "skuName": {
    "type": "string",
    "defaultValue": "S1",
    "metadata": {
      "description": "SKU name of iothub resource, by default is Standard S1"
    }
  },
  "skuTier": {
    "type": "string",
    "defaultValue": "Standard",
    "metadata": {
      "description": "SKU tier of iothub resource, by default is Standard"
    }
  },
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]",
    "metadata": {
      "description": "Location of iothub resource. Please provide any of supported-regions of iothub"
    }
  }
},
  "variables": {
    "identityName": "[concat(parameters('iotHubName'), '-identity')]"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "createIotHub",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
              "name": "[variables('identityName')]",
              "apiVersion": "2018-11-30",
              "location": "[resourceGroup().location]"
            },
            {
              "type": "Microsoft.Devices/IotHubs",
              "apiVersion": "2021-03-31",
              "name": "[parameters('iotHubName')]",
              "location": "[parameters('location')]",
              "identity": {
                "type": "UserAssigned",
                "userAssignedIdentities": {
                  "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('identityName'))]": {}
                }
              },
              "sku": {
                "name": "[parameters('skuName')]",
                "tier": "[parameters('skuTier')]",
                "capacity": 1
              },
              "dependsOn": [
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('identityName'))]"
              ]
            }
          ]
        }
      }
    }
  ]
}
```
```azurecli-interactive
az deployment group create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json> --parameters iotHubName=<valid-iothub-name> skuName=<sku-name> skuTier=<sku-tier> location=<any-of-supported-regions>
```

创建资源后，可以使用 Azure CLI 检索中心内的用户分配的托管标识：

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```
## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>从 IoT 中心到其他 Azure 资源的出口连接
在 IoT 中心，托管标识可用于从 IoT 中心到其他 Azure 服务的出口连接，以实现[消息路由](iot-hub-devguide-messages-d2c.md)、[文件上传](iot-hub-devguide-file-upload.md)和[批量设备导入/导出](iot-hub-bulk-identity-mgmt.md)。 针对与客户拥有的终结点（包括存储帐户、事件中心和服务总线终结点）的每个 IoT 中心出口连接，你可以选择使用哪个托管标识。 

### <a name="message-routing"></a>消息路由
在本部分中，我们以[消息路由](iot-hub-devguide-messages-d2c.md)到事件中心自定义终结点为例。 这同样适用于其他路由自定义终结点。 

1.  首先，我们需要在 Azure 门户中访问事件中心，以便向托管标识分配正确的访问权限。 在事件中心中，导航到“访问控制 (IAM)”选项卡，然后单击“添加”，然后单击“添加角色分配”。  
3.  选择“事件中心数据发送者作为角色”。

    > [!NOTE] 
    > 对于“存储帐户”，请选择“存储 Blob 数据参与者”（[非参与者或存储帐户参与者](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)）作为角色。 对于服务总线，选择“服务总线数据发送者”作为“角色”。 

4.  对于用户分配的服务标识，请在“将访问权限分配到”下面选择“用户分配的托管标识”。  在下拉列表中选择订阅和用户分配的托管标识。 单击“保存”按钮  。

    :::image type="content" source="./media/iot-hub-managed-identity/eventhub-iam-user-assigned.png" alt-text="分配了用户的 IoT 中心消息路由":::

5.  对于系统分配的托管标识，在“将访问权限分配到”下面选择“用户、组或服务主体”，然后在下拉列表中选择 IoT 中心的资源名称。  单击“保存” 。

    :::image type="content" source="./media/iot-hub-managed-identity/eventhub-iam-system-assigned.png" alt-text="分配了系统的 IoT 中心消息路由":::

    如果需要通过 VNet 限制与自定义终结点的连接，则需要打开受信任的 Microsoft 第一方例外，以允许 IoT 中心访问特定终结点。 例如，如果要添加事件中心自定义终结点，请导航到事件中心中的“防火墙和虚拟网络”选项卡，并启用“允许从所选网络进行访问”选项 。 在“异常”列表中，勾选“允许受信任的 Microsoft 服务访问事件中心”复选框 。 单击“保存”按钮  。 这也适用于存储帐户和服务总线。 详细了解[IoT 中心对虚拟网络的支持](./virtual-network-support.md)。

    > [!NOTE]
    > 在将事件中心作为自定义终结点添加到 IoT 中心之前，需要完成上述步骤以向托管标识分配适当的访问权限。 稍等几分钟，让系统完成角色分配的传播。 

6. 接下来，转到 IoT 中心。 在中心内，导航到“消息路由”，然后单击“自定义终结点”。  单击“添加”，然后选择要使用的终结点类型。 本部分使用事件中心作为示例。
7.  在页面底部，选择首选的“身份验证类型”。 在本部分，我们将使用“用户分配”作为示例。 在下拉列表中，选择首选的用户分配的托管标识，然后单击“创建”。

    :::image type="content" source="./media/iot-hub-managed-identity/eventhub-routing-endpoint.png" alt-text="分配了用户的 IoT 中心事件中心":::

8. 自定义终结点已成功创建。 
9. 创建后，你仍可以更改身份验证类型。 选择要更改身份验证类型的自定义终结点，然后单击“更改身份验证类型”。

    :::image type="content" source="./media/iot-hub-managed-identity/change-authentication-type.png" alt-text="IoT 中心身份验证类型":::

10. 选择要为此终结点更新的新身份验证类型，然后单击“保存”。

### <a name="file-upload"></a>文件上传
IoT 中心的[文件上传](iot-hub-devguide-file-upload.md)功能允许设备将文件上传到客户拥有的存储帐户。 若要正常上传文件，IoT 中心需要连接到存储帐户。 类似于消息路由，你可以为 Azure 存储帐户与 IoT 中心的出口连接选择首选的身份验证类型和托管标识。 

1. 在 Azure 门户中，导航到存储帐户的“访问控制 (IAM)”选项卡，然后在“添加角色分配”部分下，单击“添加”  。
2. 选择“存储 Blob 数据参与者”（非“参与者”或“存储帐户参与者”）作为角色。
3. 对于用户分配的服务标识，请在“将访问权限分配到”下面选择“用户分配的托管标识”。 在下拉列表中选择订阅和用户分配的托管标识。 单击“保存”按钮  。
4. 对于系统分配的托管标识，在“将访问权限分配到”下面选择“用户、组或服务主体”，然后在下拉列表中选择 IoT 中心的资源名称。  单击“保存” 。

    如果需要通过 VNet 限制与存储帐户的连接，则需要打开受信任的 Microsoft 第一方例外，以允许 IoT 中心访问存储帐户。 在存储帐户资源页面上，导航到“防火墙和虚拟网络”选项卡，并启用“允许从所选网络进行访问”选项 。 在“异常”列表中，勾选“允许受信任的 Microsoft 服务访问此存储帐户”复选框 。 单击“保存”按钮  。 详细了解[IoT 中心对虚拟网络的支持](./virtual-network-support.md)。 


    > [!NOTE]
    > 在使用托管标识将存储帐户保存到 IoT 中心进行文件上传之前，你需要完成上述步骤以便为托管帐户分配合适的访问权限。 稍等几分钟，让系统完成角色分配的传播。 
 
5. 在 IoT 中心的“资源”页上，导航到“文件上传”选项卡。
6. 在显示的页面上，选择要在 blob 存储中使用的容器，根据需要配置“文件通知设置”、“SAS TTL”、“默认 TT”和“最大传送计数”。 选择首选的身份验证类型，然后单击“保存”。

    :::image type="content" source="./media/iot-hub-managed-identity/file-upload.png" alt-text="在 IoT 中心上传 msi 文件":::

### <a name="bulk-device-importexport"></a>批量设备导入/导出

IoT 中心支持从/向客户提供的存储 blob 批量[导入/导出设备](iot-hub-bulk-identity-mgmt.md)的信息。 若要使用此功，需要从 IoT 中心连接到存储帐户。 

1. 在 Azure 门户中，导航到存储帐户的“访问控制 (IAM)”选项卡，然后在“添加角色分配”部分下，单击“添加”  。
2. 选择“存储 Blob 数据参与者”（非“参与者”或“存储帐户参与者”）作为角色。
3. 对于用户分配的服务标识，请在“将访问权限分配到”下面选择“用户分配的托管标识”。 在下拉列表中选择订阅和用户分配的托管标识。 单击“保存”按钮  。
4. 对于系统分配的托管标识，在“将访问权限分配到”下面选择“用户、组或服务主体”，然后在下拉列表中选择 IoT 中心的资源名称。  单击“保存” 。


### <a name="using-rest-api-or-sdk-for-import-and-export-jobs"></a>使用 REST API 或 SDK 创建导入和导出作业

现在可以使用 Azure IoT REST API 创建导入和导出作业。 需要在请求正文中提供以下属性：

- storageAuthenticationType：将值设置为 identityBased。  
- inputBlobContainerUri：仅在导入作业中设置此属性。
- outputBlobContainerUri：为导入和导出作业设置此属性。
- identity：将值设置为要使用的托管标识。


Azure IoT 中心 SDK 也支持在服务客户端的注册表管理器中使用此功能。 以下代码段演示如何在使用 C# SDK 时启动导入或导出作业。

C# 代码片段

```csharp
    // Create an export job
 
    using RegistryManager srcRegistryManager = RegistryManager.CreateFromConnectionString(hubConnectionString);
 
    JobProperties jobProperties = JobProperties.CreateForExportJob(
        outputBlobContainerUri: blobContainerUri,
        excludeKeysInExport: false,
        storageAuthenticationType: StorageAuthenticationType.IdentityBased,
        identity: new ManagedIdentity
        {
            userAssignedIdentity = userDefinedManagedIdentityResourceId
        });
```
 
```csharp
    // Create an import job
    
    using RegistryManager destRegistryManager = RegistryManager.CreateFromConnectionString(hubConnectionString);
 
    JobProperties jobProperties = JobProperties.CreateForImportJob(
        inputBlobContainerUri: blobContainerUri,
        outputBlobContainerUri: blobContainerUri,
        storageAuthenticationType: StorageAuthenticationType.IdentityBased,
        identity: new ManagedIdentity
        {
            userAssignedIdentity = userDefinedManagedIdentityResourceId
        });
```  

**Python 代码片段**

```python
# see note below
iothub_job_manager = IoTHubJobManager("<IoT Hub connection string>")

# Create an import job
result = iothub_job_manager.create_import_export_job(JobProperties(
    type="import",
    input_blob_container_uri="<input container URI>",
    output_blob_container_uri="<output container URI>",
    storage_authentication_type="identityBased",
    identity=ManagedIdentity(
        user_assigned_identity="<resource ID of user assigned managed identity>"
    )
))

# Create an export job
result = iothub_job_manager.create_import_export_job(JobProperties(
    type="export",
    output_blob_container_uri="<output container URI>",
    storage_authentication_type="identityBased",
    exclude_keys_in_export=True,
    identity=ManagedIdentity(
        user_assigned_identity="<resource ID of user assigned managed identity>"
    ) 
))
```

> [!NOTE]
> - 如果将 storageAuthenticationType 设置为 identityBased 且 userAssignedIdentity 属性不为 null，则作业将使用用户分配的指定托管标识。   
> - 如果未使用 userAssignedIdentity 中指定的用户分配的托管标识配置 IoT 中心，则作业将失败。
> - 如果将 storageAuthenticationType 设置为 identityBased，且 userAssignedIdentity 属性为 null，则作业将使用系统分配的标识。  
> - 如果未使用用户分配的托管标识配置 IoT 中心，则作业将失败。
> - 如果 storageAuthenticationType 设置为 identityBased，并且中心未配置用户分配的托管标识和系统分配的托管标识，则作业将失败。   

## <a name="sdk-samples"></a>SDK 示例
- [.NET SDK 示例](https://aka.ms/iothubmsicsharpsample)
- [Java SDK 示例](https://aka.ms/iothubmsijavasample)
- [Python SDK 示例](https://aka.ms/iothubmsipythonsample)
- Node.js SDK 示例：[批量设备导入](https://aka.ms/iothubmsinodesampleimport)、[批量设备导出](https://aka.ms/iothubmsinodesampleexport)

## <a name="next-steps"></a>后续步骤

使用以下链接详细了解 IoT 中心功能：

* [消息路由](./iot-hub-devguide-messages-d2c.md)
* [文件上传](./iot-hub-devguide-file-upload.md)
* [批量设备导入/导出](./iot-hub-bulk-identity-mgmt.md)
