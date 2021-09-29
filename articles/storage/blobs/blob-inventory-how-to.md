---
title: 启用 Azure 存储 Blob 清单报表
description: 了解存储帐户中的容器、Blob、快照和 Blob 版本的概况。
services: storage
author: normesta
ms.service: storage
ms.date: 08/16/2021
ms.topic: how-to
ms.author: normesta
ms.reviewer: klaasl
ms.subservice: blobs
ms.openlocfilehash: 67bd943028ba321aa4fa3a5acca30e80cfc36a32
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128615560"
---
# <a name="enable-azure-storage-blob-inventory-reports"></a>启用 Azure 存储 Blob 清单报表

Azure 存储 Blob 清单功能简述了存储帐户中的容器、Blob、快照和 Blob 版本。 使用清单报表可以了解 Blob 和容器的各种属性，例如总数据大小、新旧程度、加密状态、不可变性策略和法定保留，等等。 该报表概述了有关业务和符合性要求的数据。

若要详细了解 Blob 清单报表，请参阅 [Azure 存储 Blob 清单](blob-inventory.md)。

通过将包含一个或多个规则的策略添加到存储帐户，可以启用 Blob 清单报表。 使用 [Azure 门户](https://portal.azure.com/)来添加、编辑或删除策略。

## <a name="enable-inventory-reports"></a>启用库存报表

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. 登录到 [Azure 门户](https://portal.azure.com/)即可开始操作。

2. 找到存储帐户并显示帐户概览。

3. 在“数据管理”下，选择“Blob 清单”。

4. 选择“添加第一个清单规则”。

   随即显示“添加规则”页。

5. 在“添加规则”页中，为新规则命名。

6. 选择容器。

7. 在“要清点的对象类型”下，选择是为 Blob 还是为容器创建报表。

   如果选择“Blob”，则在“Blob 子类型”下，选择要包括在报表中的 Blob 类型，以及是否在清单报表中包括 Blob 版本和/或快照。

   > [!NOTE]
   > 必须在该帐户上启用版本和快照，才能保存启用了相应选项的新规则。

8. 选择要包含在报表中的字段，以及报表的格式。

9. 选择你需要生成报表的频率。

9. 可以选择添加一个前缀匹配来筛选清单报表中的 Blob。

10. 选择“保存”。

    :::image type="content" source="./media/blob-inventory-how-to/portal-blob-inventory.png" alt-text="显示如何使用 Azure 门户添加 blob 清单规则的屏幕截图":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

<a id="powershell"></a>

可以使用 Azure PowerShell 模块启用静态网站托管。

1. 打开 Windows PowerShell 命令窗口。

2. 确保你已有最新的 Azure PowerShell 模块。 请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-Az-ps)。

3. 运行 `Connect-AzAccount` 命令以登录 Azure 订阅，并按照屏幕上的说明操作。

   ```powershell
   Connect-AzAccount
   ```

4. 如果你的标识与多个订阅相关联，请将你的活动订阅设置为将托管静态网站的存储帐户的订阅。

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   将 `<subscription-id>` 占位符值替换为你的订阅 ID。

5. 获取定义了要使用的存储帐户的存储帐户上下文。

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   - 将 `<resource-group-name>` 占位符值替换为资源组的名称。

   - 将 `<storage-account-name>` 占位符值替换为存储帐户的名称。

6. 使用 [New-AzStorageBlobInventoryPolicyRule](/powershell/module/az.storage/new-azstorageblobinventorypolicyrule) 命令创建清单规则。 每个规则都列出了报表字段。 要查看报表字段的完整列表，请参阅 [Azure 存储 Blob 清单](blob-inventory.md)。

   ```powershell
    $containerName = "my-container"

    $rule1 = New-AzStorageBlobInventoryPolicyRule -Name Test1 -Destination $containerName -Disabled -Format Csv -Schedule Daily -PrefixMatch con1,con2 `
                -ContainerSchemaField Name,Metadata,PublicAccess,Last-modified,LeaseStatus,LeaseState,LeaseDuration,HasImmutabilityPolicy,HasLegalHold

    $rule2 = New-AzStorageBlobInventoryPolicyRule -Name test2 -Destination $containerName -Format Parquet -Schedule Weekly  -BlobType blockBlob,appendBlob -PrefixMatch aaa,bbb `
                -BlobSchemaField name,Last-Modified,Metadata,LastAccessTime

    $rule3 = New-AzStorageBlobInventoryPolicyRule -Name Test3 -Destination $containerName -Format Parquet -Schedule Weekly -IncludeBlobVersion -IncludeSnapshot -BlobType blockBlob,appendBlob -PrefixMatch aaa,bbb `
                -BlobSchemaField name,Creation-Time,Last-Modified,Content-Length,Content-MD5,BlobType,AccessTier,AccessTierChangeTime,Expiry-Time,hdi_isfolder,Owner,Group,Permissions,Acl,Metadata,LastAccessTime

    $rule4 = New-AzStorageBlobInventoryPolicyRule -Name test4 -Destination $containerName -Format Csv -Schedule Weekly -BlobType blockBlob -BlobSchemaField Name,BlobType,Content-Length,Creation-Time

   ```

7. 使用 [Set-AzStorageBlobInventoryPolicy](/powershell/module/az.storage/set-azstorageblobinventorypolicy) 创建 Blob 清单策略。 使用 `-Rule` 参数将规则传递到此命令中。

   ```powershell
   $policy = Set-AzStorageBlobInventoryPolicy -StorageAccount $storageAccount -Rule $rule1,$rule2,$rule3,$rule4  
   ```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

<a id="cli"></a>

可以使用 [Azure 命令行接口 (CLI)](/cli/azure/) 来启用静态网站托管。

1. 首先，打开 [Azure Cloud Shell](../../cloud-shell/overview.md)，或者，如果已在本地[安装](/cli/azure/install-azure-cli) Azure CLI，请打开命令控制台应用程序，如 Windows PowerShell。

2. 如果你的标识与多个订阅相关联，请将你的活动订阅设置为将托管静态网站的存储帐户的订阅。

   ```azurecli
      az account set --subscription <subscription-id>
   ```

   将 `<subscription-id>` 占位符值替换为你的订阅 ID。

3. 在 JSON 文档中定义策略的规则。 下面显示了名为 `policy.json` 的示例 JSON 文件的内容。

    ```json
    {
    "enabled": true,
    "type": "Inventory",
    "rules": [
      {
        "enabled": true,
        "name": "inventoryPolicyRule2",
        "destination": "mycontainer",
        "definition": {
          "filters": {
            "blobTypes": [
              "blockBlob"
            ],
            "prefixMatch": [
              "inventoryprefix1",
              "inventoryprefix2"
            ],
            "includeSnapshots": true,
            "includeBlobVersions": true
          },
          "format": "Csv",
          "schedule": "Daily",
          "objectType": "Blob",
          "schemaFields": [
            "Name",
            "Creation-Time",
            "Last-Modified",
            "Content-Length",
            "Content-MD5",
            "BlobType",
            "AccessTier",
            "AccessTierChangeTime",
            "Snapshot",
            "VersionId",
            "IsCurrentVersion",
            "Metadata"
          ]
        }
      }
     ]
   }

   ```

4. 使用 [az storage account blob-inventory-policy](/cli/azure/storage/account/blob-inventory-policy#az_storage_account_blob_inventory_policy_create) create 命令创建 Blob 清单策略。 使用 `--policy` 参数提供 JSON 文档的名称。

   ```azurecli
   az storage account blob-inventory-policy create -g myresourcegroup --account-name mystorageaccount --policy @policy.json
   ```

---

## <a name="next-steps"></a>后续步骤

- [计算每个容器的 Blob 计数和总大小](calculate-blob-count-size.md)
- [管理 Azure Blob 存储生命周期](./lifecycle-management-overview.md)
