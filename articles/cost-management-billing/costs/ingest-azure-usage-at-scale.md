---
title: 通过 Azure 成本管理的导出项来定期检索高成本的数据集
description: 本文帮助你使用成本管理中的导出功能定期导出大量数据。
author: bandersmsft
ms.author: banders
ms.date: 10/07/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: a27e64e7b5c94d033529c08ea02e757ba8857549
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129705848"
---
# <a name="retrieve-large-cost-datasets-recurringly-with-exports"></a>使用导出功能定期检索大型成本数据集

本文帮助你使用成本管理中的导出功能定期导出大量数据。 建议通过导出的方式来检索未聚合的成本数据。 特别是当使用情况文件太大而无法使用“使用情况详细信息 API”可靠地调用和下载时。 导出的数据置于你所选择的 Azure 存储帐户中。 因此，可以将其加载到你自己的系统中，并按需对其进行分析。 若要在 Azure 门户中配置导出，请参阅[导出数据](tutorial-export-acm-data.md)。

如果要在不同范围自动执行导出，可以首先了解下一部分的示例 API 请求。 可以使用导出 API 创建自动导出，作为常规环境配置的一部分。 自动导出有助于确保你拥有所需的数据。 扩展 Azure 用途时，可以在自己组织的系统中使用。

## <a name="common-export-configurations"></a>常用导出配置

在创建第一个导出之前，请考虑你的方案和启用该方案所需的配置选项。 请考虑以下导出选项：

- **定期** - 确定导出作业的运行频率以及何时将文件放入 Azure 存储帐户。 你可以选择“每天”、“每周”和“每月”。 尝试配置定期，使其与组织内部系统使用的数据导入作业匹配。
- **定期间隔** - 确定导出有效期。 文件仅在定期间隔内导出。
- **时间范围** - 确定在给定运行中由导出生成的数据量。 常用选项是 MonthToDate 和 WeekToDate。
- **StartDate** - 配置何时开始导出计划。 将在 StartDate 上创建一个导出，然后根据周期创建导出。
- **类型** - 导出类型分为以下三种：
  - ActualCost - 显示指定时间段内的总使用量和费用，这些总使用量和费用已累积显示在帐单上。
  - AmortizedCost - 显示指定时间段内的总使用量和成本，并均摊适用的预留购买成本。
  - 用法 - 2020 年 7 月 20 日之前创建的所有导出均为“用法”类型。 将所有计划的导出更新为 ActualCost 或 AmortizedCost。
- **列** - 定义要包含在导出文件中的数据字段。 它们与“用法详细信息 API”中可用的字段相对应。 有关详细信息，请参阅[使用情况详细信息 API](/rest/api/consumption/usagedetails/list)。

## <a name="create-a-daily-month-to-date-export-for-a-subscription"></a>每天为订阅创建本月至今导出

请求 URL：`PUT https://management.azure.com/{scope}/providers/Microsoft.CostManagement/exports/{exportName}?api-version=2020-06-01`

```json
{
  "properties": {
    "schedule": {
      "status": "Active",
      "recurrence": "Daily",
      "recurrencePeriod": {
        "from": "2020-06-01T00:00:00Z",
        "to": "2020-10-31T00:00:00Z"
      }
    },
    "format": "Csv",
    "deliveryInfo": {
      "destination": {
        "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MYDEVTESTRG/providers/Microsoft.Storage/storageAccounts/{yourStorageAccount} ",
        "container": "{yourContainer}",
        "rootFolderPath": "{yourDirectory}"
      }
    },
    "definition": {
      "type": "ActualCost",
      "timeframe": "MonthToDate",
      "dataSet": {
        "granularity": "Daily",
        "configuration": {
          "columns": [
            "Date",
            "MeterId",
            "ResourceId",
            "ResourceLocation",
            "Quantity"
          ]
        }
      }
    }
}
```

## <a name="copy-large-azure-storage-blobs"></a>复制大型 Azure 存储 Blob

可以使用成本管理在 Azure 存储帐户中计划以 Blob 形式导出 Azure 使用情况详细信息。 生成的 Blob 大小可能超过数 GB。 成本管理团队已与 Azure 存储团队协作测试了大型 Azure 存储 Blob 的复制。 以下部分说明了这些结果。 将存储 Blob 从一个 Azure 区域复制到另一个 Azure 区域预期会产生类似的结果。

为了测试复制性能，该团队已将位于“美国西部”区域的存储帐户中的 Blob 传输到同一区域和其他区域。 该团队测得的速度为：在同一区域中复制速度为每秒 2 GB，复制到“东南亚”区域中存储帐户的速度为每秒 150 MB。

### <a name="test-configuration"></a>测试配置

为了测量 Blob 传输速度，该团队创建了一个简单的 .NET 控制台应用程序，该应用程序通过 NuGet 引用最新版本 (v2.0.1) 的 Azure 数据移动库 (DLM)。 DLM 是 Azure 存储团队提供的 SDK，能够简化对其传输服务的编程式访问。 然后，他们在多个区域中创建了标准 V2 存储帐户，并使用“美国西部”作为源区域。 他们在该区域的存储帐户中填充了容器，其中每个容器保存 10 个 2 GB 的块 Blob。 他们结合 _CopyMethod. ServiceSideSyncCopy_ 选项使用 DLM 的 _TransferManager.CopyDirectoryAsync()_  方法将容器复制到了其他存储帐户。 测试是在一台运行 Windows 10 的计算机上进行的，该计算机配备 12 个核心和 1-GbE 网络。

使用的应用程序设置：

- _TransferManager.Configurations.ParallelOperations_ = _Environment.ProcessorCount \* 32_. 该团队找到了对提高总体吞吐量最为有效的设置。 将核心数乘以 32 得到的值能够为测试客户端提供最佳吞吐量。
- _ServicePointManager.DefaultConnectionLimit = int.MaxValue_。 将其设置为最大值能够有效地向上述 _ParallelOperations_ 设置传递传输并行度的全面控制。
- _TransferManager.Configurations.BlockSize = 4,194,304_。 此设置对 4 MB 传输速率有一定的效果，已证实最适合用于测试。

有关详细信息和示例代码，请参阅[后续步骤](#next-steps)部分中的链接。

### <a name="test-results"></a>测试结果

| **测试编号** | **目标区域** | **Blob** | **时间（秒）** | **MB/s** | **注释** |
| --- | --- | --- | --- | --- | --- |
| 1 | 美国西部 | 2 GB x 10 | 10 | 2,000 |   |
| 2 | 美国西部 2 | 2 GB x 10 | 33 | 600 |   |
| 3 | EastUS | 2 GB x 10 | 67 | 300 |   |
| 4 | EastUS | 2 GB x 10 x 4 | 99 | 200 | 使用 8 个存储帐户的 4 路并行传输：每路传输平均从 4 个西部存储帐户复制到 4 个东部存储帐户 |
| 6 | EastUS | 2 GB x 10 x 4 | 92 | 870 | 从 1 个存储帐户复制到另一个存储帐户的 4 路并行传输 |
| 5 | EastUS | 2G x 10 x 8 | 148 | 135 | 使用 8 个存储帐户的 8 路并行传输：每路传输平均从 4 个西部存储帐户复制到 4x2 个东部存储帐户 |
| 7 | 东南亚 | 2 GB x 10 | 133 | 150 |   |
| 8 | 东南亚 | 2 GB x 10 x 4 | 444 | 180 | 从 1 个存储帐户复制到另一个存储帐户的 4 路并行传输 |

### <a name="sync-transfer-characteristics"></a>同步传输特征

下面是与 DML 配合使用的服务端同步传输的一些特征，这些特征与该传输形式的用法有关：

- DML 可以传输单个 Blob 或目录。 传输目录时，可以使用搜索模式来匹配 Blob 前缀。
- 块 Blob 传输并行发生。 除非传输过程结束，否则所有传输不会停止。 各个 Blob 块以并行方式传输。
- 传输在客户端上以异步方式执行。 可以通过方法回调（可在 _TransferContext_ 对象中定义）定期获取传输状态。
- 传输功能在传输过程中将创建检查点，并公开 _TransferCheckpoint_ 对象。 该对象通过 _TransferContext_ 对象表示最新的检查点。 如果在取消/中止传输之前保存了 _TransferCheckpoint_，则传输可以从最多七天内的检查点恢复。 传输可以从任何检查点（而不仅仅是最新检查点）恢复。
- 如果在未实施检查点功能的情况下终止再重启传输客户端进程：
  - 在已完成任何 Blob 传输之前，传输将重启。
  - 在已完成某些 Blob 之后，只对未完成的 Blob 重启传输。
- 暂停客户端执行会暂停传输。
- 发生暂时性故障时，Blob 传输功能可使客户端独善其身。 例如，发生存储帐户限制一般不会导致传输失败，而只会减慢传输速度。
- 服务端传输使用的 CPU 和内存客户端资源较少，且只会消耗一部分网络带宽和连接。

### <a name="async-transfer-characteristics"></a>异步传输特征

可以结合 _CopyMethod.ServiceSideAsyncCopy_ 选项调用 _TransferManager.CopyDirectoryAsync()_ 方法。 从客户端角度看，异步传输的工作原理与同步传输机制类似，不过操作时存在以下差异：

- 传输速率要比类似的同步传输慢得多（通常为 10 MB/s 或更慢）。
- 即使客户端进程终止，传输也仍会继续。
- 尽管支持检查点，但使用 _TransferCheckpoint_ 恢复传输并不会在检查点时间进行恢复，而是从当前传输状态恢复。

### <a name="test-summary"></a>测试摘要

Azure Blob 存储凭借其服务端同步传输功能支持较高的全局传输速率。 在 .NET 应用程序中使用该功能的方法就是直截了当地使用数据移动库。 成本管理导出能够在一小时内，将数百 GB 的数据可靠地复制到位于任何位置的存储帐户。

## <a name="next-steps"></a>后续步骤

- 参阅 [Microsoft Azure 存储数据移动库](https://github.com/Azure/azure-storage-net-data-movement)资源。
- [使用数据移动库传输数据](../../storage/common/storage-use-data-movement-library.md)。
- 参阅 [AzureDmlBackup 示例应用程序](https://github.com/markjbrown/AzureDmlBackup)源代码示例。
- 参阅[使用 Azure Blob 存储实现高吞吐量](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage)。