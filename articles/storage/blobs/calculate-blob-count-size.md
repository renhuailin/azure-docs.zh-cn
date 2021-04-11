---
title: 使用 Azure 存储空间库存计算 blob 计数和大小
description: 了解如何计算每个容器的 blob 计数和总大小。
services: storage
author: twooley
ms.author: twooley
ms.date: 03/10/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.openlocfilehash: e752d40ce2f237c2ab08bac2e71133cd06ec40e4
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277175"
---
# <a name="calculate-blob-count-and-total-size-per-container-using-azure-storage-inventory"></a>使用 Azure 存储空间库存计算每个容器的 blob 计数和总大小

本文使用 Azure Blob 存储库存功能和 Azure Synapse 来计算每个容器的 blob 的 blob 计数和总大小。 在优化每个容器的 blob 使用时，这些值非常有用。

Blob 元数据未包含在此方法中。 Azure Blob 存储库存功能将[列表 Blob](/rest/api/storageservices/list-blobs) REST API 与默认参数一起使用。 因此，该示例不支持快照和“$”容器等。

> [!IMPORTANT]
> Blob 库存目前处于预览阶段。 有关 beta 版本、预览版或尚未正式发布的版本的 Azure 功能所适用的法律条款，请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="enable-inventory-reports"></a>启用库存报表

此方法中的第一步是在存储帐户上[启用库存报表](blob-inventory.md#enable-inventory-reports)。 为生成的第一个报表启用库存报表后，可能需要等待 24 个小时。

如果有要分析的库存报表，请授予自己对报表 CSV 文件所在容器的 blob 读取访问权限。

1. 导航到具有库存 CSV 报表文件的容器。
1. 依次选择“访问控制(IAM)”和“添加角色分配”

    :::image type="content" source="media/calculate-blob-count-size/access.png" alt-text="选择“添加角色分配”":::

1. 从“角色”下拉列表中，选择“存储 Blob 数据读取器”。

    :::image type="content" source="media/calculate-blob-count-size/add-role-assignment.png" alt-text="添加来自下拉列表的存储 Blob 数据读取器角色":::

1. 在“选择”字段中输入将用于运行报表的帐户的电子邮件地址。

## <a name="create-an-azure-synapse-workspace"></a>创建 Azure Synapse 工作区

接下来，[创建 Azure Synapse 工作区](../../synapse-analytics/get-started-create-workspace.md)，你将在其中执行 SQL 查询来报告库存结果。

## <a name="create-the-sql-query"></a>创建 SQL 查询

创建 Azure Synapse 工作区后，请执行以下步骤。

1. 导航到 [https://web.azuresynapse.net](https://web.azuresynapse.net)。
1. 选择左边缘的“开发”选项卡。
1. 选择大的加号 (+) 来添加项。
1. 选择“SQL 脚本”。

    :::image type="content" source="media/calculate-blob-count-size/synapse-sql-script.png" alt-text="选择“SQL 脚本”以创建新查询":::

## <a name="run-the-sql-query"></a>运行 SQL 查询

1. 在 Azure Synapse 工作区中添加以下 SQL 查询以 [读取库存 CSV 文件](../../synapse-analytics/sql/query-single-csv-file.md#read-a-csv-file)。

    对于 `bulk` 参数，请使用要分析的库存报表 CSV 文件的 URL。

    ```sql
    SELECT LEFT([Name], CHARINDEX('/', [Name]) - 1) AS Container, 
            COUNT(*) As TotalBlobCount,
            SUM([Content-Length]) As TotalBlobSize
    FROM OPENROWSET(
        bulk '<URL to your inventory CSV file>',
        format='csv', parser_version='2.0', header_row=true
    ) AS Source
    GROUP BY LEFT([Name], CHARINDEX('/', [Name]) - 1)
    ```

1. 在右侧的“属性”窗格中命名 SQL 查询。

1. 通过按 CTRL + S 或选择“全部发布”按钮，发布 SQL 查询。

1. 选择“运行”按钮以执行 SQL 查询。 “结果”窗格中报告了每个容器的 blob 计数和总大小。

    :::image type="content" source="media/calculate-blob-count-size/output.png" alt-text="运行脚本的输出用于计算 blob 计数和总大小。":::

## <a name="next-steps"></a>后续步骤

- [使用 Azure 存储 blob 库存来管理 blob 数据](blob-inventory.md)
- [计算要计费的 Blob 容器总大小](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)