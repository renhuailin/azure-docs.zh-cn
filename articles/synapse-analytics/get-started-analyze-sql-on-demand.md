---
title: 教程：开始使用无服务器 SQL 池分析数据
description: 在本教程中，你将了解如何通过无服务器 SQL 池使用 Spark 数据库中的数据对数据进行分析。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: c9f8760bd1a7b5d3700f3fdf03331fe7013e116f
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209400"
---
# <a name="analyze-data-with-serverless-sql-pool-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中使用无服务器 SQL 池分析数据

在本教程中，你将了解如何通过无服务器 SQL 池使用 Spark 数据库中的数据对数据进行分析。 

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-serverless-sql-pool"></a>使用无服务器 SQL 池分析 Blob 存储中的纽约市出租车数据

1. 在“链接”下的“数据”中心中，右键单击“Azure Blob 存储”>“示例数据集”>“nyc_tlc_yellow”和“新建 SQL 脚本”，然后选择“选择前 100 行”    
1. 这将会使用以下代码创建新 SQL 脚本：

    ```
    -- This is auto-generated code
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
            BULK     'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT = 'parquet'
        ) AS [result];
    ```
1. 单击“**运行**”

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-serverless-sql-pool"></a>使用无服务器 SQL 池分析 Spark 数据库中的纽约市出租车数据

Spark 数据库中的表会自动显示，且无服务器 SQL 池可以查询这些表。

1. 在 Synapse Studio 中，转到“开发”中心，然后新建 SQL 脚本。
1. 将“连接到”设置为“内置”无服务器 SQL 池 。
1. 将以下文本粘贴到脚本中，然后运行该脚本。

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > 第一次运行使用无服务器 SQL 池的查询时，无服务器 SQL 池需要大约 10 秒的时间来收集运行查询所需的 SQL 资源。 后续查询的速度要快得多。
  


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [分析存储中的数据](get-started-analyze-storage.md)
