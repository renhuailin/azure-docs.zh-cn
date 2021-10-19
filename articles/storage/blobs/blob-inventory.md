---
title: Azure 存储 Blob 清单
description: Azure 存储清单是一种工具，可帮助你大致了解存储帐户中的所有 blob 数据。
services: storage
author: normesta
ms.service: storage
ms.date: 08/16/2021
ms.topic: conceptual
ms.author: normesta
ms.reviewer: klaasl
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 6962688a574d7f7c11f8cbfc71ccdb29ac3b6445
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129619381"
---
# <a name="azure-storage-blob-inventory"></a>Azure 存储 Blob 清单

Azure 存储 Blob 清单功能提供存储帐户中的容器、Blob、快照和 Blob 版本的概述。 使用清单报表可以了解 Blob 和容器的各种属性，例如总数据大小、新旧程度、加密状态、不可变性策略和法定保留，等等。 该报表概述了有关业务和符合性要求的数据。

## <a name="inventory-features"></a>清单功能

以下列表描述了当前版本的 Azure 存储 Blob 清单提供的特性和功能。

- Blob 和容器的清单报告

  可以生成 Blob 和容器的清单报告。 Blob 的报告可以包含基本 Blob、快照、Blob 版本及其关联的属性，例如创建时间和上次修改时间。 容器的报告描述容器及其关联的属性，例如不可变性策略状态和法定保留状态。

- 自定义架构

  可以选择要在报告中显示的字段。 从支持的字段列表中进行选择。 本文稍后会提供该列表。

- CSV 和 Apache Parquet 输出格式

  可以生成 CSV 或 Apache Parquet 输出格式的清单报告。

- 每份清单报告的清单文件和 Azure 事件网格事件

  将为每份清单报告生成一个清单文件和一个 Azure 事件网格事件。 本文稍后将介绍这些内容。

## <a name="enabling-inventory-reports"></a>启用清单报告

通过在存储帐户中添加带有一个或多个规则的策略来启用 Blob 清单报告。 有关指导，请参阅[启用 Azure 存储 Blob 清单报告](blob-inventory-how-to.md)。

## <a name="upgrading-an-inventory-policy"></a>升级清单策略

如果你是在 2021 年 6 月之前配置了清单的现有 Azure 存储 Blob 清单用户，可以通过加载策略并在进行更改后重新保存策略，来开始使用新功能。 重载策略时，将使用默认值填充该策略的新字段。 如果需要，可以更改这些值。 此外，还可使用以下两项功能。

- 现在，每个规则（而不仅仅是策略）都支持目标容器。

- 现在，会按规则（而不是策略）生成清单文件和 Azure 事件网格事件。

## <a name="inventory-policy"></a>清单策略

清单策略是 JSON 文档中规则的集合。

```json
{
  "enabled": true,
  "rules": [
  {
    "enabled": true,
    "name": "inventoryrule1",
    "destination": "inventory-destination-container",
    "definition": {. . .}
  },
  {
    "enabled": true,
    "name": "inventoryrule2",
    "destination": "inventory-destination-container",
    "definition": {. . .}
  }]
}
```

通过选择 Azure 门户的“Blob 清单”部分中的“代码视图”选项卡，查看清单策略的 JSON 。

| 参数名称 | 参数类型 | 注释 | 必需？ |
|--|--|--|--|
| enabled | boolean | 用于禁用整个策略。 如果设置为 true，则启用规则级别的字段将重写此参数。 禁用后，将禁用所有规则的清单。 | 是 |
| 规则 | 规则对象的数组 | 一个策略至少需要包含一个规则。 每个策略最多支持 100 个规则。 | 是 |

## <a name="inventory-rules"></a>清单规则

规则捕获用于生成清单报表的筛选条件和输出参数。 每个规则都会创建一个清单报表。 规则可以有重叠的前缀。 Blob 可以出现在多个清单中，具体取决于规则定义。

策略中的每个规则具有多个参数：

| 参数名称 | 参数类型 | 注释 | 必需？ |
|--|--|--|--|
| name | string | 规则名称最多只能包含 256 个字母数字字符（区分大小写）。 名称在策略中必须唯一。 | 是 |
| enabled | boolean | 允许启用或禁用规则的标志。 默认值为 **true**。 | 是 |
| 定义 | JSON 清单规则定义 | 每个定义均由规则筛选器集组成。 | 是 |
| destination | 字符串 | 将在其中生成所有清单文件的目标容器。 目标容器必须已经存在。 |

全局“已启用 blob 清单”标志优先于规则中的 enabled 参数。

### <a name="rule-definition"></a>规则定义

| 参数名称 | 参数类型 | 注释 | 必须 |
|--|--|--|--|
| 筛选器 | json | 筛选器确定某个 Blob 或容器是否是清单的一部分。 | 是 |
| format | 字符串 | 确定清单文件的输出。 有效值为 `csv`（表示 CSV 格式）和 `parquet`（表示 Apache Parquet 格式）。| 是 |
| objectType | string | 指示这是适用于 Blob 还是适用于容器的清单规则。 有效值为 `blob` 和 `container`。 |是 |
| schedule | string | 运行此规则所依照的计划。 有效值为 `daily` 和 `weekly`。 | 是 |
| schemaFields | JSON 数组 | 作为清单一部分的架构字段的列表。 | 是 |

### <a name="rule-filters"></a>规则筛选器

有多个筛选器可用于自定义 blob 清单报表：

| 筛选器名称 | 筛选器类型 | 注释 | 必需？ |
|--|--|--|--|
| blobTypes | 预定义枚举值的数组 | 对于已启用分层命名空间的帐户，有效值为 `blockBlob` 和 `appendBlob`，对于其他帐户，有效值为 `blockBlob``appendBlob` 和 `pageBlob`。 此字段不适用于容器的清单（objectType：`container`）。 | 是 |
| prefixMatch | 要匹配的前缀字符串数组，最多包含 10 个字符串。 | 如果未定义 prefixMatch 或提供空前缀，规则将应用到存储帐户中的所有 Blob。 前缀必须是容器名称前缀或容器名称。 例如：`container`、`container1/foo`。 | 否 |
| includeSnapshots | boolean | 指定清单是否应包含快照。 默认值为 `false`。 此字段不适用于容器的清单（objectType：`container`）。 | 否 |
| includeBlobVersions | boolean | 指定清单是否应包含 blob 版本。 默认值为 `false`。 此字段不适用于容器的清单（objectType：`container`）。 | 否 |

通过选择 Azure 门户的“Blob 清单”部分中的“代码视图”选项卡，查看清单规则的 JSON 。 筛选器在规则定义中指定。

```json
{
  "destination": "inventory-destination-container",
  "enabled": true,
  "rules": [
  {
    "definition": {
      "filters": {
        "blobTypes": ["blockBlob", "appendBlob", "pageBlob"],
        "prefixMatch": ["inventorytestcontainer1", "inventorytestcontainer2/abcd", "etc"],
        "includeSnapshots": false,
        "includeBlobVersions": true,
      },
      "format": "csv",
      "objectType": "blob",
      "schedule": "daily",
      "schemaFields": ["Name", "Creation-Time"]
    },
    "enabled": true,
    "name": "blobinventorytest",
    "destination": "inventorydestinationContainer"
  },
  {
    "definition": {
      "filters": {
        "prefixMatch": ["inventorytestcontainer1", "inventorytestcontainer2/abcd", "etc"]
      },
      "format": "csv",
      "objectType": "container",
      "schedule": "weekly",
      "schemaFields": ["Name", "HasImmutabilityPolicy", "HasLegalHold"]
    },
    "enabled": true,
    "name": "containerinventorytest",
    "destination": "inventorydestinationContainer"
    }
  ]
}
```

### <a name="custom-schema-fields-supported-for-blob-inventory"></a>Blob 清单支持的自定义架构字段

- Name（必填）
- Creation-Time
- Last-Modified
- Content-Length
- Content-MD5
- /BlobType
- AccessTier
- AccessTierChangeTime
- Expiry-Time
- hdi_isfolder
- “所有者”
- 组
- 权限
- Acl
- Snapshot（在选择将快照包含到报告中时可用且必填）
- VersionId（在选择将 Blob 版本包含到报告中时可用且必填）
- IsCurrentVersion（在选择将 Blob 版本包含到报告中时可用且必填）
- 元数据
- LastAccessTime

### <a name="custom-schema-fields-supported-for-container-inventory"></a>容器清单支持的自定义架构字段

- Name（必填）
- Last-Modified
- LeaseStatus
- LeaseState
- LeaseDuration
- PublicAccess
- HasImmutabilityPolicy
- HasLegalHold
- 元数据

## <a name="inventory-run"></a>清单运行

每天自动安排 blob 清单运行。 完成清单运行可能需要长达 24 小时。 清单报表通过使用一个或多个规则添加清单策略来配置。

清单策略完全读取或写入。 不支持部分更新。

> [!IMPORTANT]
> 如果为存储帐户启用了防火墙规则，清单请求可能会遭到阻止。 可以通过针对受信任的 Microsoft 服务提供例外来取消阻止这些请求。 有关详细信息，请参阅[配置防火墙和虚拟网络](../common/storage-network-security.md#exceptions)中的“例外”部分。

## <a name="inventory-completed-event"></a>清单已完成事件

针对规则完成清单运行后，将生成 `BlobInventoryPolicyCompleted` 事件。 如果清单运行由于在开始运行之前出现用户错误而失败，则也会发生此事件。 例如，无效的策略，或者在目标容器不存在时出现的错误会触发该事件。 以下 JSON 演示了一个示例 `BlobInventoryPolicyCompleted` 事件。

```json
{
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/BlobInventory/providers/Microsoft.EventGrid/topics/BlobInventoryTopic",
  "subject": "BlobDataManagement/BlobInventory",
  "eventType": "Microsoft.Storage.BlobInventoryPolicyCompleted",
  "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "data": {
    "scheduleDateTime": "2021-05-28T03:50:27Z",
    "accountName": "testaccount",
    "ruleName": "Rule_1",
    "policyRunStatus": "Succeeded",
    "policyRunStatusMessage": "Inventory run succeeded, refer manifest file for inventory details.",
    "policyRunId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "manifestBlobUrl": "https://testaccount.blob.core.windows.net/inventory-destination-container/2021/05/26/13-25-36/Rule_1/Rule_1.csv"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2021-05-28T15:03:18Z"
}
```

下表描述了 `BlobInventoryPolicyCompleted` 事件的架构。

|字段|类型|说明|
|---|---|
|scheduleDateTime|string|计划清单策略的时间。|
|accountName|string|存储帐户名称。|
|ruleName|string|规则名称。|
|policyRunStatus|string|清单运行状态。 可能值为 `Succeeded`、`PartiallySucceeded` 和 `Failed`。|
|policyRunStatusMessage|string|清单运行的状态消息。|
|policyRunId|string|清单运行的策略运行 ID。|
|manifestBlobUrl|string|清单运行的清单文件的 Blob URL。|

## <a name="inventory-output"></a>清单输出

每个清单规则会在该规则的指定清单目标容器中生成一组文件。 清单输出在以下路径下生成：`https://<accountName>.blob.core.windows.net/<inventory-destination-container>/YYYY/MM/DD/HH-MM-SS/<ruleName`其中：

- accountName 是你的 Azure Blob 存储帐户名称。
- inventory-destination-container 是在清单规则中指定的目标容器。
- YYYY/MM/DD/HH-MM-SS 是清单开始运行的时间。
- ruleName 是清单规则名称。

### <a name="inventory-files"></a>清单文件

规则的每个清单运行会生成以下文件：

- 清单文件：规则的清单运行会生成一个或多个 CSV 或 Apache Parquet 格式的文件。 如果匹配的对象计数很大，则会生成多个文件而不是单个文件。 每个此类文件包含匹配的对象及其元数据。 对于 CSV 格式的文件，第一行始终是架构行。 下图显示了在 Microsoft Excel 中打开的清单 CSV 文件。

  :::image type="content" source="./media/blob-inventory/csv-file-excel.png" alt-text="在 Microsoft Excel 中打开的清单 CSV 文件的屏幕截图":::

  > [!NOTE]
  > Apache Parquet 格式的报告以下面的格式显示日期：`timestamp_millis [number of milliseconds since 1970-01-01 00:00:00 UTC`。

- 校验和文件：校验和文件包含 manifest.json 文件内容的 MD5 校验和。 校验和文件的名称为 `<ruleName>-manifest.checksum`。 生成了检验和文件即表示清单规则运行已完成。

- 清单文件：manifest.json 文件包含针对该规则生成的清单文件的详细信息。 该文件的名称为 `<ruleName>-manifest.json`。 此文件还会捕获用户提供的规则定义以及该规则的清单的路径。 以下 JSON 演示了一个示例 manifest.json 文件的内容。

  ```json
  {
  "destinationContainer" : "inventory-destination-container",
  "endpoint" : "https://testaccount.blob.core.windows.net",
  "files" : [
    {
      "blob" : "2021/05/26/13-25-36/Rule_1/Rule_1.csv",
      "size" : 12710092
    }
  ],
  "inventoryCompletionTime" : "2021-05-26T13:35:56Z",
  "inventoryStartTime" : "2021-05-26T13:25:36Z",
  "ruleDefinition" : {
    "filters" : {
      "blobTypes" : [ "blockBlob" ],
      "includeBlobVersions" : false,
      "includeSnapshots" : false,
      "prefixMatch" : [ "penner-test-container-100003" ]
    },
    "format" : "csv",
    "objectType" : "blob",
    "schedule" : "daily",
    "schemaFields" : [
      "Name",
      "Creation-Time",
      "BlobType",
      "Content-Length",
      "LastAccessTime",
      "Last-Modified",
      "Metadata",
      "AccessTier"
    ]
  },
  "ruleName" : "Rule_1",
  "status" : "Succeeded",
  "summary" : {
    "objectCount" : 110000,
    "totalObjectSize" : 23789775
  },
  "version" : "1.0"
  }
  ```

## <a name="pricing-and-billing"></a>定价和计费

清单的定价基于在计费周期内扫描的 Blob 和容器的数量。 例如，假设某个帐户包含 100 万个 Blob，并且 Blob 清单设置为每周运行一次。 在四周后，将已扫描 400 万个 Blob 条目。

Blob 清单计费将从 2021 年 10 月 1 日开始。 届时将会公布区域定价。 对于扫描的每 100 万个 Blob 存储条目，未经过区域调整的基准价格为 0.0025 美元；如果已启用 Data Lake Storage Gen2，则为 0.0035 美元。 创建清单文件后，将会产生额外的标准数据存储和操作费用，因为需要在帐户中存储、读取和写入清单生成的文件。

清单报告完成后，将会产生额外的标准数据存储和操作费用，因为需要在存储帐户中存储、读取和写入清单报告。

如果规则包含的前缀与任何其他规则的前缀重叠，则同一个 Blob 可能会出现在多份清单报告中。 在这种情况下，你需要为两个实例付费。 例如，假设一个规则的 `prefixMatch` 元素设置为 `["inventory-blob-1", "inventory-blob-2"]`，而另一个规则的 `prefixMatch` 元素设置为 `["inventory-blob-10", "inventory-blob-20"]`。 名为 `inventory-blob-200` 的对象将出现在这两份清单报告中。

Blob 的快照和版本也会计入到费用内，即使已将 `includeSnapshots` 和 `includeVersions` 筛选器设置为 `false`，也是如此。 这些筛选器值不影响计费。 它们只可用于筛选报告中显示的内容。

有关 Azure 存储 Blob 清单的定价详细信息，请参阅 [Azure Blob 存储定价](https://azure.microsoft.com/pricing/details/storage/blobs/)。

## <a name="feature-support"></a>功能支持

此表显示了你的帐户如何支持此功能，以及启用某些功能时对支持的影响。

| 存储帐户类型 | Blob 存储（默认支持） | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| 标准常规用途 v2 | ![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png)  <sup>2</sup>              | ![是](../media/icons/yes-icon.png) <sup>2</sup> |
| 高级块 blob | ![是](../media/icons/yes-icon.png)| ![是](../media/icons/yes-icon.png)  <sup>2</sup> | ![是](../media/icons/yes-icon.png)  <sup>2</sup> |

<sup>1</sup> Data Lake Storage Gen2 和网络文件系统 (NFS) 3.0 协议都需要已启用分层命名空间的存储帐户。

<sup>2</sup> 功能在预览级别受支持。

## <a name="known-issues"></a>已知问题

本部分介绍了 Azure 存储 blob 清单功能的限制和已知问题。

### <a name="inventory-job-fails-to-complete-for-hierarchical-namespace-enabled-accounts"></a>对于启用了分层命名空间的帐户，清单作业无法完成

对于启用了数亿个 Blob 和分层命名空间的帐户，清单作业可能无法在 24 小时内完成。 如果发生这种情况，则不会创建任何清单文件。

### <a name="inventory-job-cannot-write-inventory-reports"></a>清单作业无法写入清单报告

对象复制策略可能会阻止清单作业将清单报告写入目标容器。 在报告已部分完成时，其他某些方案可能会存档报告或使其不可变。 这可能导致清单作业失败。

## <a name="next-steps"></a>后续步骤

- [启用 Azure 存储 Blob 清单报告](blob-inventory-how-to.md)
- [计算每个容器的 Blob 计数和总大小](calculate-blob-count-size.md)
- [管理 Azure Blob 存储生命周期](./lifecycle-management-overview.md)
