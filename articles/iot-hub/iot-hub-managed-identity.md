---
title: Azure IoT 中心托管标识 | Microsoft Docs
description: 如何使用托管标识允许从 IoT 中心到其他 Azure 资源的出口连接。
author: miag
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/24/2021
ms.author: miag
ms.openlocfilehash: 4d5a518bc517b950f5366ba53eadb7284121a5e3
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866406"
---
# <a name="iot-hub-support-for-managed-identities"></a>IoT 中心对托管标识的支持 

托管标识在 Azure AD 中以安全的方式为 Azure 服务提供自动托管标识。 这样，开发人员无需提供标识即可管理凭据。 有两种类型的托管标识：系统分配和用户分配的托管标识。 IoT 中心支持这两者。 

在 IoT 中心，托管标识可用于从 IoT 中心到其他 Azure 服务的出口连接，以实现[消息路由](iot-hub-devguide-messages-d2c.md)、[文件上传](iot-hub-devguide-file-upload.md)和[批量设备导入/导出](iot-hub-bulk-identity-mgmt.md)等功能。 在本文中，你将了解如何在 IoT 中心使用系统分配的和用户分配的托管标识来实现不同的功能。 


## <a name="prerequisites"></a>先决条件
- 阅读 [Azure 资源托管标识](./../active-directory/managed-identities-azure-resources/overview.md)这一文档，了解系统分配的托管标识和用户分配的托管标识之间的区别。

- 如果没有 IoT 中心，请先[创建一个](iot-hub-create-through-portal.md)，然后再继续。


## <a name="system-assigned-managed-identity"></a>系统分配的托管标识 

### <a name="add-and-remove-a-system-assigned-managed-identity-in-azure-portal"></a>在 Azure 门户中添加和删除系统分配的托管标识
1.  登录 Azure 门户并导航到所需的 IoT 中心。
2.  在 IoT 中心门户中导航到“标识”
3.  在“系统分配”选项卡下，选择“打开”，然后单击“保存”  。
4.  若要从 IoT 中心删除系统分配的托管标识，请选择“关闭”，然后单击“保存” 。

    :::image type="content" source="./media/iot-hub-managed-identity/system-assigned.png" alt-text="显示在何处为 IoT 中心启用系统分配的托管标识的屏幕截图":::        

### <a name="enable-system-assigned-managed-identity-at-hub-creation-time-using-arm-template"></a>在创建中心时使用 ARM 模板启用系统分配的托管标识

若要在预配资源时在 IoT 中心启用系统分配的托管标识，请使用下面的 Azure 资源管理器 (ARM) 模板。 

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

创建资源后，可使用 Azure CLI 检索分配给中心的系统分配的托管标识：

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```
## <a name="user-assigned-managed-identity"></a>用户分配的托管标识 
在本部分中，你将了解如何使用 Azure 门户从 IoT 中心添加和删除用户分配的托管标识。
1.  首先，需要创建一个用户分配的托管标识作为独立资源。 为此，可按照[创建用户分配的托管标识](./../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md#create-a-user-assigned-managed-identity)中的说明操作。
2.  转到 IoT 中心，导航到 IoT 中心门户中的“标识”。
3.  在“用户分配”选项卡下，单击“添加用户分配的托管标识” 。 选择要添加到中心的用户分配的托管标识，然后单击“选择”。 
4.  可从 IoT 中心删除用户分配的标识。 选择要删除的用户分配的标识，然后单击“删除”按钮。 请注意，仅可从 IoT 中心删除用户分配的标识，并且此删除操作不会将用户分配的标识作为资源删除。 要将用户分配的标识作为资源删除，请按照[删除用户分配的托管标识](./../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md#delete-a-user-assigned-managed-identity)中的说明操作。

    :::image type="content" source="./media/iot-hub-managed-identity/user-assigned.png" alt-text="显示如何为 IoT 中心添加用户分配的托管标识的屏幕截图":::        


### <a name="enable-user-assigned-managed-identity-at-hub-creation-time-using-arm-template"></a>在创建中心时使用 ARM 模板启用用户分配的托管标识
下面的示例模板可用于创建具有用户分配的托管标识的中心。 此模板会创建一个名为 [iothub-name-provided]-identity 的用户分配的标识，并将其分配到创建的 IoT 中心。 你可以更改此模板，根据需要添加多个用户分配的标识。
 
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

创建资源后，可以使用 Azure CLI 检索分配给中心的用户分配的托管标识：

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```
## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>从 IoT 中心到其他 Azure 资源的出口连接
在 IoT 中心，托管标识可用于从 IoT 中心到其他 Azure 服务的出口连接，以实现[消息路由](iot-hub-devguide-messages-d2c.md)、[文件上传](iot-hub-devguide-file-upload.md)和[批量设备导入/导出](iot-hub-bulk-identity-mgmt.md)等功能。 可为每个指向客户拥有的终结点的 IoT 中心出站连接选择要使用的托管标识。 

## <a name="configure-message-routing-with-managed-identities"></a>使用托管标识配置消息路由
本部分将使用指向事件中心自定义终结点的[消息路由](iot-hub-devguide-messages-d2c.md)作为示例。 这同样适用于其他路由自定义终结点。 

1.  首先，我们需要转到 Azure 门户中的事件中心，向托管标识分配正确的访问权限。 在事件中心，导航到“访问控制(IAM)”选项卡，依次单击“添加”和“添加角色分配”  。
3.  选择“事件中心数据发送者”作为角色。

    > [!NOTE] 
    > 对于存储帐户，请选择“存储 Blob 数据参与者”（[不是参与者或存储帐户参与者](../storage/blobs/assign-azure-role-data-access.md)）作为角色。 对于服务总线，请选择“服务总线数据发送者”作为角色 。

4.  对于用户分配的托管标识，请在“将访问权限分配到”下方选择“用户分配的托管标识” 。 在下拉列表中选择订阅和用户分配的托管标识。 单击“保存”按钮  。

    :::image type="content" source="./media/iot-hub-managed-identity/eventhub-iam-user-assigned.png" alt-text="具有用户分配的托管标识的 IoT 中心消息路由":::

5.  对于系统分配的托管标识，请在“将访问权限分配到”下方，选择“用户、组或服务主体”，然后在下拉列表中选择 IoT 中心的资源名称 。 单击“保存”  。

    :::image type="content" source="./media/iot-hub-managed-identity/eventhub-iam-system-assigned.png" alt-text="具有系统分配的托管标识的 IoT 中心消息路由":::

    如果需要通过 VNet 限制与自定义终结点的连接，则需要启用 Microsoft 受信任的第一方例外，使 IoT 中心能够访问特定终结点。 例如，如果要添加事件中心自定义终结点，请导航到事件中心的“防火墙和虚拟网络”选项卡，并启用“允许从所选网络访问”选项 。 在“异常”列表中，勾选“允许受信任的 Microsoft 服务访问事件中心”复选框 。 单击“保存”按钮  。 这也适用于存储帐户和服务总线。 详细了解 [IoT 中心的虚拟网络支持](./virtual-network-support.md)。

    > [!NOTE]
    > 在 IoT 中心内将事件中心添加为自定义终结点之前，需要完成上述步骤以为托管标识分配正确的访问权限。 请等待角色分配传播完成。 

6. 接下来，请前往 IoT 中心。 在中心，导航到“消息路由”，然后单击“自定义终结点” 。 单击“添加”，然后选择要使用的终结点类型。 在本部分中，我们使用事件中心作为示例。
7.  在页面底部，选择首选的“身份验证类型”。 在本部分中，我们使用“用户分配”作为示例。 在下拉列表中，选择首选的用户分配的托管标识，然后单击“创建”。

    :::image type="content" source="./media/iot-hub-managed-identity/eventhub-routing-endpoint.png" alt-text="具有用户分配的托管标识的 IoT 中心事件中心":::

8. 至此，已成功创建自定义终结点。 
9. 创建后，仍可更改身份验证类型。 选择要更改身份验证类型的自定义终结点，然后单击“更改身份验证类型”。

    :::image type="content" source="./media/iot-hub-managed-identity/change-authentication-type.png" alt-text="IoT 中心身份验证类型":::

10. 选择要为此终结点更新的新身份验证类型，然后单击“保存”。

## <a name="configure-file-upload-with-managed-identities"></a>使用托管标识配置文件上传
IoT 中心的[文件上传](iot-hub-devguide-file-upload.md)功能允许设备将文件上传到客户拥有的存储帐户。 若要正常上传文件，IoT 中心需要连接到存储帐户。 类似于消息路由，你可以为指向 Azure 存储帐户的 IoT 中心出站连接选择首选身份验证类型和托管标识。 

1. 在 Azure 门户中，导航到存储帐户的“访问控制 (IAM)”选项卡，然后在“添加角色分配”部分下，单击“添加”  。
2. 选择“存储 Blob 数据参与者”（不是参与者或存储帐户参与者）作为角色。
3. 对于用户分配的托管标识，请在“将访问权限分配到”下方选择“用户分配的托管标识”。 在下拉列表中选择订阅和用户分配的托管标识。 单击“保存”按钮  。
4. 对于系统分配的托管标识，请在“将访问权限分配到”下方，选择“用户、组或服务主体”，然后在下拉列表中选择 IoT 中心的资源名称 。 单击“保存”  。

    如果需要通过 VNet 限制与存储帐户的连接，则需要启用 Microsoft 受信任的第一方例外，使 IoT 中心能够访问特定终结点。 在存储帐户资源页上，导航到存储帐户中的“防火墙和虚拟网络”选项卡，并启用“允许从所选网络进行访问”选项 。 在“异常”列表中，勾选“允许受信任的 Microsoft 服务访问此存储帐户”复选框 。 单击“保存”按钮  。 详细了解 [IoT 中心的虚拟网络支持](./virtual-network-support.md)。 


    > [!NOTE]
    > 在使用托管标识在 IoT 中心保存存储帐户以供文件上传之前，需要完成上述步骤来为托管标识分配正确的访问权限。 请等待角色分配传播完成。 
 
5. 在 IoT 中心的“资源”页上，导航到“文件上传”选项卡。
6. 在显示的页面上，选择要在 blob 存储中使用的容器，根据需要配置“文件通知设置、SAS TTL、默认 TT 和最大传送计数”。 选择首选的身份验证类型，然后单击“保存”。 如果在此步骤中遇到错误，请暂时将存储帐户设置为允许从所有网络访问，然后重试。 文件上传配置完成后，可以在存储帐户上配置防火墙。

    :::image type="content" source="./media/iot-hub-managed-identity/file-upload.png" alt-text="具有 MSI 的 IoT 中心文件上传":::

    > [!NOTE]
    > 在文件上传方案中，中心和设备都需要与存储帐户连接。 上述步骤用于使用所需身份验证类型将 IoT 中心连接到存储帐户。 仍然需要使用 SAS URI 将设备连接到存储。 目前，SAS URI 是使用连接字符串生成的。 我们即将添加对使用托管标识生成 SAS URI 的支持。 请按照[文件上传](iot-hub-devguide-file-upload.md)中的步骤操作。

## <a name="configure-bulk-device-importexport-with-managed-identities"></a>使用托管标识配置批量设备导入/导出

IoT 中心支持从/向客户提供的存储 blob 批量[导入/导出](iot-hub-bulk-identity-mgmt.md)设备的信息。 若要使用此功，需要从 IoT 中心连接到存储帐户。 

1. 在 Azure 门户中，导航到存储帐户的“访问控制 (IAM)”选项卡，然后在“添加角色分配”部分下，单击“添加”  。
2. 选择“存储 Blob 数据参与者”（不是参与者或存储帐户参与者）作为角色。
3. 对于用户分配的托管标识，请在“将访问权限分配到”下方选择“用户分配的托管标识”。 在下拉列表中选择订阅和用户分配的托管标识。 单击“保存”按钮  。
4. 对于系统分配的托管标识，请在“将访问权限分配到”下方，选择“用户、组或服务主体”，然后在下拉列表中选择 IoT 中心的资源名称 。 单击“保存”  。


### <a name="using-rest-api-or-sdk-for-import-and-export-jobs"></a>将 REST API 或 SDK 用于导入和导出作业

现在可以将 Azure IoT REST API 或 SDK 用于创建导入和导出作业。 需要在请求正文中提供以下属性：

- **storageAuthenticationType：** 将值设置为 identityBased。 
- **inputBlobContainerUri：** 仅在导入作业中设置此属性。
- **inputBlobContainerUri：** 在导入和导出作业中设置此属性。
- **identity：** 将值设置为要使用的托管标识。


Azure IoT 中心 SDK 也支持在服务客户端的注册表管理器中使用此功能。 以下代码段演示如何在使用 C# SDK 时启动导入或导出作业。

**C# 代码片段**

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
> - 如果将 storageAuthenticationType 设置为 identityBased，并且 userAssignedIdentity 属性不为 null，则作业将使用指定的用户分配的托管标识   。
> - 如果未使用 userAssignedIdentity 中指定的用户分配的托管标识配置 IoT 中心，则作业将失败。
> - 如果将 storageAuthenticationType 设置为 identityBased，并且 userAssignedIdentity 属性为 null，则作业将使用系统分配的托管标识  。
> - 如果未使用用户分配的托管标识配置 IoT 中心，则作业将失败。
> - 如果将 storageAuthenticationType 设置为 identityBased，并且中心上未配置用户分配的托管标识和系统分配的托管标识，则作业将失败   。

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