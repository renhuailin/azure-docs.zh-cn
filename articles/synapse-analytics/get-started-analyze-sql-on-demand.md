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
ms.date: 04/15/2021
ms.openlocfilehash: c6f2dfe0d4846227400ac9b3c7ac3e6ead8f0b57
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567547"
---
# <a name="analyze-data-with-a-serverless-sql-pool"></a>使用无服务器 SQL 池分析数据

在本教程中，你将了解如何通过无服务器 SQL 池分析对数据。 

## <a name="the-built-in-serverless-sql-pool"></a>内置无服务器 SQL 池

通过无服务器 SQL 池，你可以使用 SQL，而无需预留容量。 无服务器 SQL 池的计费是基于运行查询所处理的数据量，而不是运行查询所使用的节点数。

每个工作区都具有名为“内置”的预配置无服务器 SQL 池。 

## <a name="analyze-nyc-taxi-data-with-a-serverless-sql-pool"></a>使用无服务器 SQL 池分析纽约市出租车数据

1. 在 Synapse Studio 中，转到“开发”中心
1. 创建一个新的 SQL 脚本。
1. 将以下代码粘贴到脚本中。

    ```
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
            BULK 'https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet',
            FORMAT='PARQUET'
        ) AS [result]
    ```
1. 单击 **“运行”** 。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用无服务器 Spark 池分析数据](get-started-analyze-spark.md)
