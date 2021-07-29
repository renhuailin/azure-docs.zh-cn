---
title: 使用 Azure 存储清单管理 blob 数据（预览版）
description: Azure 存储清单是一种工具，可帮助你大致了解存储帐户中的所有 blob 数据。
services: storage
author: twooley
ms.service: storage
ms.date: 04/01/2021
ms.topic: conceptual
ms.author: twooley
ms.reviewer: klaasl
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: e3619917a6b64440e60bfc664603ffe0b32ab895
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110095295"
---
# <a name="use-azure-storage-blob-inventory-to-manage-blob-data-preview"></a>使用 Azure 存储 blob 清单来管理 blob 数据（预览版）

Azure 存储 blob 清单功能概述了存储帐户中 blob 数据。 使用清单报表来了解总数据大小、期限、访问层等。 该报表概述了有关业务和符合性要求的数据。 启用后，每天会自动创建清单报表。

## <a name="availability"></a>可用性

常规用途版本 2 (GPv2) 和高级块 blob 存储帐户均支持 blob 清单。 在启用或不启用[分层命名空间](data-lake-storage-namespace.md)功能的情况下均支持此功能。

> [!IMPORTANT]
> Blob 库存目前处于预览阶段。 有关 beta 版本、预览版或尚未正式发布的版本的 Azure 功能所适用的法律条款，请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

### <a name="preview-regions"></a>预览区域

Blob 清单预览版在以下区域中的存储帐户上可用：

- 法国中部
- 加拿大中部
- 加拿大东部
- 美国东部
- 美国东部 2
- 西欧

### <a name="pricing-and-billing"></a>定价和计费

在预览期间，不会对清单报表收费。 此功能正式发布时，将会确定定价。

## <a name="enable-inventory-reports"></a>启用库存报表

通过向存储帐户添加策略来启用 blob 清单报表。 使用 [Azure 门户](https://portal.azure.com/)来添加、编辑或删除策略。

1. 导航到 [Azure 门户](https://portal.azure.com/)
1. 选择一个存储帐户
1. 在“Blob 服务”下，选择“Blob 清单” 
1. 确保已选中“启用 Blob 清单”
1. 选择“添加规则”
1. 为新规则命名
1. 为清单报表选择“Blob 类型”
1. 添加前缀匹配来筛选清单报表
1. 选择是否要在清单报表中“包含 blob 版本”和“包含快照” 。 必须在该帐户上启用版本和快照，才能保存启用了相应选项的新规则。
1. 选择“保存”

:::image type="content" source="./media/blob-inventory/portal-blob-inventory.png" alt-text="显示如何使用 Azure 门户添加 blob 清单规则的屏幕截图":::

清单策略完全读取或写入。 不支持部分更新。

> [!IMPORTANT]
> 如果为存储帐户启用了防火墙规则，清单请求可能会遭到阻止。 可以通过针对受信任的 Microsoft 服务提供例外来取消阻止这些请求。 有关详细信息，请参阅[配置防火墙和虚拟网络](../common/storage-network-security.md#exceptions)中的“例外”部分。

每天自动安排 blob 清单运行。 完成清单运行可能需要长达 24 小时。 清单报表通过使用一个或多个规则添加清单策略来配置。

## <a name="inventory-policy"></a>清单策略

清单策略是 JSON 文档中规则的集合。

```json
{
    "destination": "destinationContainer",
    "enabled": true,
    "rules": [
    {
        "enabled": true,
        "name": "inventoryrule1",
        "definition": {. . .}
    },
    {
        "enabled": true,
        "name": "inventoryrule2",
        "definition": {. . .}
    }]
}
```

通过选择 Azure 门户的“Blob 清单”部分中的“代码视图”选项卡，查看清单策略的 JSON 。

| 参数名称 | 参数类型        | 注释 | 必需？ |
|----------------|-----------------------|-------|-----------|
| destination    | String                | 将在其中生成所有清单文件的目标容器。 目标容器必须已经存在。 | 是 |
| enabled        | 布尔               | 用于禁用整个策略。 如果设置为 true，则启用规则级别的字段将重写此参数。 禁用后，将禁用所有规则的清单。 | 是 |
| 规则          | 规则对象的数组 | 一个策略至少需要包含一个规则。 最多支持 10 个规则。 | 是 |

## <a name="inventory-rules"></a>清单规则

规则捕获用于生成清单报表的筛选条件和输出参数。 每个规则都会创建一个清单报表。 规则可以有重叠的前缀。 Blob 可以出现在多个清单中，具体取决于规则定义。

策略中的每个规则具有多个参数：

| 参数名称 | 参数类型                 | 注释 | 必需？ |
|----------------|--------------------------------|-------|-----------|
| name           | String                         | 规则名称最多只能包含 256 个字母数字字符（区分大小写）。 名称在策略中必须唯一。 | 是 |
| enabled        | 布尔                        | 允许启用或禁用规则的标志。 默认值为 **true**。 | 是 |
| 定义     | JSON 清单规则定义 | 每个定义均由规则筛选器集组成。 | 是 |

全局“已启用 blob 清单”标志优先于规则中的 enabled 参数。

### <a name="rule-filters"></a>规则筛选器

有多个筛选器可用于自定义 blob 清单报表：

| 筛选器名称         | 筛选器类型                     | 注释 | 必需？ |
|---------------------|---------------------------------|-------|-----------|
| blobTypes           | 预定义枚举值的数组 | 对于已启用分层命名空间的帐户，有效值为 `blockBlob` 和 `appendBlob`，对于其他帐户，有效值为 `blockBlob``appendBlob` 和 `pageBlob`。 | 是 |
| prefixMatch         | 要匹配的前缀字符串数组，最多包含 10 个字符串。 前缀必须以容器名称开头，例如“container1/foo” | 如果未定义 prefixMatch 或提供空前缀，规则将应用到存储帐户中的所有 Blob。 | 否 |
| includeSnapshots    | 布尔                         | 指定清单是否应包含快照。 默认值为 **false**。 | 否 |
| includeBlobVersions | 布尔                         | 指定清单是否应包含 blob 版本。 默认值为 **false**。 | 否 |

通过选择 Azure 门户的“Blob 清单”部分中的“代码视图”选项卡，查看清单规则的 JSON 。 筛选器在规则定义中指定。

```json
{
    "destination": "destinationContainer",
    "enabled": true,
    "rules": [
    {
        "enabled": true,
        "name": "inventoryrule1",
        "definition":
        {
            "filters":
            {
                "blobTypes": ["blockBlob", "appendBlob", "pageBlob"],
                "prefixMatch": ["inventorycontainer1", "inventorycontainer2/abcd", "etc"]
            }
        }
    },
    {
        "enabled": true,
        "name": "inventoryrule2",
        "definition":
        {
            "filters":
            {
                "blobTypes": ["pageBlob"],
                "prefixMatch": ["inventorycontainer-disks-", "inventorycontainer4/"],
                "includeSnapshots": true,
                "includeBlobVersions": true
            }
        }
    }]
}
```

## <a name="inventory-output"></a>清单输出

每个清单运行都会在指定的清单目标容器中生成一组 CSV 格式的文件。 清单输出在以下路径下生成：`https://<accountName>.blob.core.windows.net/<inventory-destination-container>/YYYY/MM/DD/HH-MM-SS/`其中：

- accountName 是你的 Azure Blob 存储帐户名称
- inventory-destination-container 是在清单策略中指定的目标容器
- YYYY/MM/DD/HH-MM-SS 是清单开始运行的时间

### <a name="inventory-files"></a>清单文件

每个清单运行都会生成以下文件：

- **清单 CSV 文件**：每个清单规则的逗号分隔值 (CSV) 文件。 每个文件都包含匹配的对象及其元数据。 每个 CSV 格式的文件中的第一行始终是架构行。 下图显示了在 Microsoft Excel 中打开的清单 CSV 文件。

   :::image type="content" source="./media/blob-inventory/csv-file-excel.png" alt-text="在 Microsoft Excel 中打开的清单 CSV 文件的屏幕截图":::

- **清单文件**：一个 manifest.json 文件，其中包含为运行中的每个规则生成的清单文件的详细信息。 清单文件还会捕获用户提供的规则定义以及该规则的清单的路径。

- **校验和文件**：一个 manifest.checksum 文件，其中包含 manifest.json 文件内容的 MD5 校验和。 manifest.checksum 文件的生成将标记清单运行的完成。

## <a name="inventory-completed-event"></a>清单已完成事件

订阅清单已完成事件，以便在清单运行完成时收到通知。 创建清单校验和文件时，会生成此事件。 如果清单运行在开始运行之前因用户错误失败，则也会发生清单已完成事件。 例如，无效策略或“目标容器不存在”错误将触发事件。 将事件发布到 Blob 清单主题。

示例事件：

```json
{
  "topic": "/subscriptions/3000151d-7a84-4120-b71c-336feab0b0f0/resourceGroups/BlobInventory/providers/Microsoft.EventGrid/topics/BlobInventoryTopic",
  "subject": "BlobDataManagement/BlobInventory",
  "eventType": "Microsoft.Storage.BlobInventoryPolicyCompleted",
  "id": "c99f7962-ef9d-403e-9522-dbe7443667fe",
  "data": {
    "scheduleDateTime": "2020-10-13T15:37:33Z",
    "accountName": "inventoryaccountname",
    "policyRunStatus": "Succeeded",
    "policyRunStatusMessage": "Inventory run succeeded, refer manifest file for inventory details.",
    "policyRunId": "b5e1d4cc-ee23-4ed5-b039-897376a84f79",
    "manifestBlobUrl": "https://inventoryaccountname.blob.core.windows.net/inventory-destination-container/2020/10/13/15-37-33/manifest.json"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-10-13T15:47:54Z"
}
```

## <a name="known-issues"></a>已知问题

本部分介绍了 Azure 存储 blob 清单功能的限制和已知问题。

### <a name="inventory-job-fails-to-complete"></a>清单作业无法完成

对于已启用数百万个 blob 和分层命名空间的帐户，清单作业可能不会在24小时内完成。 如果发生这种情况，则不会创建任何清单文件。

## <a name="next-steps"></a>后续步骤

- [计算每个容器的 Blob 计数和总大小](calculate-blob-count-size.md)
- [管理 Azure Blob 存储生命周期](storage-lifecycle-management-concepts.md)
