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
ms.openlocfilehash: 7c228bfe5897b45e6345234f2ed8e0f5cfbec73a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312784"
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
1. 单击“**运行**”

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用无服务器 Spark 池分析数据](get-started-analyze-spark.md)
