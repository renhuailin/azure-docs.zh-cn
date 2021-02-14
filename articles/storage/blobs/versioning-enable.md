---
title: 启用和管理 blob 版本控制
titleSuffix: Azure Storage
description: 了解如何在 Azure 门户中或使用 Azure 资源管理器模板启用 blob 版本控制。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/09/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 5b6bd16eacf4b1bbb7b93f5500813e7fa9dc7eef
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/10/2021
ms.locfileid: "100095829"
---
# <a name="enable-and-manage-blob-versioning"></a>启用和管理 blob 版本控制

可以启用 Blob 存储版本控制，以便在修改或删除 blob 时，自动维护以前版本的 blob。 启用 blob 版本控制后，可以还原较早版本的 blob，以在数据被错误修改或删除的情况下恢复数据。

本文介绍如何使用 Azure 门户或 Azure 资源管理器模板启用或禁用存储帐户的 blob 版本控制。 若要了解有关 blob 版本控制的详细信息，请参阅 [blob 版本控制](versioning-overview.md)。

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="enable-blob-versioning"></a>启用 Blob 版本控制

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

为 Azure 门户中的存储帐户启用 blob 版本控制：

1. 导航到门户中的存储帐户。
1. 在 " **Blob 服务**" 下，选择 " **数据保护**"。
1. 在 " **版本控制** " 部分，选择 " **已启用**"。

:::image type="content" source="media/versioning-enable/portal-enable-versioning.png" alt-text="显示如何在 Azure 门户中启用 blob 版本控制的屏幕截图":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 为存储帐户启用 blob 版本控制，请首先安装 [Az. storage](https://www.powershellgallery.com/packages/Az.Storage) module 版本2.3.0 或更高版本。 然后调用 [AzStorageBlobServiceProperty](/powershell/module/az.storage/update-azstorageblobserviceproperty) 命令来启用版本控制，如下面的示例中所示。 请记住，将尖括号中的值替换为自己的值：

```powershell
# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable versioning.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -IsVersioningEnabled $true
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要为具有 Azure CLI 的存储帐户启用 blob 版本控制，请首先安装 Azure CLI 版本2.2.0 或更高版本。 然后调用 [az storage account blob-service-properties update](/cli/azure/ext/storage-blob-preview/storage/account/blob-service-properties#ext_storage_blob_preview_az_storage_account_blob_service_properties_update) 命令来启用版本控制，如下面的示例中所示。 请记住，将尖括号中的值替换为自己的值：

```azurecli
az storage account blob-service-properties update \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --enable-versioning true
```

# <a name="template"></a>[模板](#tab/template)

若要启用具有模板的 blob 版本控制，请创建一个模板，并将 **IsVersioningEnabled** 属性 **设置为 true**。 以下步骤说明了如何在 Azure 门户中创建模板。

1. 在 Azure 门户中，选择“创建资源”。
1. 在“搜索市场”中键入“模板部署”，然后按 **ENTER**。 
1. 选择 " **模板部署**"，选择 " **创建**"，然后选择 **"在编辑器中生成自己的模板"**。
1. 在模板编辑器中，粘贴以下 JSON。 将 `<accountName>` 占位符替换为存储帐户的名称。
1. 保存模板。
1. 指定帐户的资源组，然后选择 " **购买** " 按钮部署模板并启用 blob 版本控制。

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts/blobServices",
                "apiVersion": "2019-06-01",
                "name": "<accountName>/default",
                "properties": {
                    "IsVersioningEnabled": true
                }
            }
        ]
    }
    ```

有关 Azure 门户中的模板部署资源的详细信息，请参阅 [部署具有 Azure 门户的资源](../../azure-resource-manager/templates/deploy-portal.md)。

---

## <a name="modify-a-blob-to-trigger-a-new-version"></a>修改 blob 以触发新版本

下面的代码示例演示如何使用适用于 .NET 的 Azure 存储客户端库版本 [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) 或更高版本触发新版本的创建。 运行此示例之前，请确保已为存储帐户启用了版本控制。

该示例创建一个块 blob，然后更新该 blob 的元数据。 更新 blob 的元数据会触发新版本的创建。 该示例将检索初始版本和当前版本，并显示只有当前版本包括元数据。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_UpdateVersionedBlobMetadata":::

## <a name="list-blob-versions"></a>列出 blob 版本

若要列出具有 .NET v12 客户端库的 blob 版本或快照，请在 "**版本**" 字段中指定 [BlobStates](/dotnet/api/azure.storage.blobs.models.blobstates)参数。

下面的代码示例演示如何通过适用于 .NET 的 Azure 存储客户端库版本 [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) 或更高版本列出 blob 版本。 运行此示例之前，请确保已为存储帐户启用了版本控制。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobVersions":::

## <a name="next-steps"></a>后续步骤

- [Blob 版本控制](versioning-overview.md)
- [Azure 存储 Blob 的软删除](./soft-delete-blob-overview.md)