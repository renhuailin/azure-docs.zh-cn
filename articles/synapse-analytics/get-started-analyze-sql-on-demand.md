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
ms.openlocfilehash: 5f0a7477df2e281748c053ea8c7e7d3e79626296
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "104588012"
---
# <a name="analyze-data-with-a-serverless-sql-pool"></a>使用无服务器 SQL 池分析数据

在本教程中，你将了解如何通过无服务器 SQL 池使用 Spark 数据库中的数据对数据进行分析。 

## <a name="the-built-in-serverless-sql-pool"></a>内置无服务器 SQL 池

通过无服务器 SQL 池，你可以使用 SQL，而无需预留容量。 无服务器 SQL 池的计费是基于运行查询所处理的数据量，而不是运行查询所使用的节点数。

每个工作区都具有名为“内置”的预配置无服务器 SQL 池。 

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-serverless-sql-pool"></a>使用无服务器 SQL 池分析 Blob 存储中的纽约市出租车数据

在本部分中，将使用无服务器 SQL 池来分析 Azure Blob 存储帐户中的纽约出租车数据。

1. 在 Synapse Studio 中，转到“开发”中心
1. 创建一个新的 SQL 脚本。
1. 将以下代码粘贴到脚本中。

    ```
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
            BULK     'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT = 'parquet'
        ) AS [result];
    ```
1. 单击“**运行**”

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用无服务器 Spark 池分析数据](get-started-analyze-spark.md)
