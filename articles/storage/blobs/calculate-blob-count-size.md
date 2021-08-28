---
title: 使用 Azure 存储空间库存计算 blob 计数和大小
description: 了解如何计算每个容器的 blob 计数和总大小。
services: storage
author: normesta
ms.author: normesta
ms.date: 08/16/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.custom: subject-rbac-steps
ms.openlocfilehash: ffdb6dd0d998cfe12b50dab85f49f06e30903d6f
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122270951"
---
# <a name="calculate-blob-count-and-total-size-per-container-using-azure-storage-inventory"></a>使用 Azure 存储空间库存计算每个容器的 blob 计数和总大小

本文使用 Azure Blob 存储库存功能和 Azure Synapse 来计算每个容器的 blob 的 blob 计数和总大小。 在优化每个容器的 blob 使用时，这些值非常有用。

Blob 元数据未包含在此方法中。 Azure Blob 存储库存功能将[列表 Blob](/rest/api/storageservices/list-blobs) REST API 与默认参数一起使用。 因此，该示例不支持快照和“$”容器等。

## <a name="enable-inventory-reports"></a>启用库存报表

此方法中的第一步是在存储帐户上[启用库存报表](blob-inventory.md#enabling-inventory-reports)。 为生成的第一个报表启用库存报表后，可能需要等待 24 个小时。

如果有要分析的库存报表，请通过为自己分配“存储 Blob 数据读取者”角色，授予自己对报表 CSV 文件所在容器的读取访问权限。 请务必使用你用于运行报表的帐户的电子邮件地址。 若要了解如何使用 Azure 基于角色的访问控制 (Azure RBAC) 将 Azure 角色分配给用户，请按照[使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)中提供的说明进行操作。

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