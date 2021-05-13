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
ms.openlocfilehash: acae55ca82b82de8459068bb1ac4363d6a9faafe
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108206630"
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

    ```sql
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
            BULK 'https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet',
            FORMAT='PARQUET'
        ) AS [result]
    ```
1. 单击 **“运行”** 。 

数据浏览只是一种简化的方案，可以在其中了解数据的基本特征。 在本[教程](sql/tutorial-data-analyst.md)中详细了解数据浏览和分析。

## <a name="create-data-exploration-database"></a>创建数据浏览数据库

可以直接通过 `master` 数据库浏览文件的内容。 对于一些简单的数据浏览方案，无需创建单独的数据库。
但是，在继续进行数据浏览时，可能需要创建一些实用工具对象，例如：
- 表示存储帐户的命名引用的外部数据源。
- 用于指定如何对外部数据源进行身份验证的数据库范围凭据。
- 具有访问某些数据源或数据库对象权限的数据库用户。
- 可在查询中使用的实用工具视图、过程和函数。

1. 创建一个单独的数据库来使用这些对象。 自定义数据库对象无法在 `master` 数据库中进行创建。

    ```sql
    CREATE DATABASE DataExplorationDB 
                    COLLATE Latin1_General_100_BIN2_UTF8
    ```

   > [!IMPORTANT]
   > 使用带有 `_UTF8` 后缀的排序规则确保 UTF-8 文本正确转换为 `VARCHAR` 列。 `Latin1_General_100_BIN2_UTF8` 在从 Parquet 文件和 Cosmos DB 容器读取数据的查询中提供最佳性能。

2. 切换到 `DataExplorationDB`（可在其中创建实用工具对象，例如凭据和数据源）。

    ```sql
    CREATE EXTERNAL DATA SOURCE ContosoLake
    WITH ( LOCATION = 'https://contosolake.dfs.core.windows.net')
    ```

   > [!NOTE]
   > 可创建没有凭据的外部数据源。 在这种情况下，调用方的标识将用于访问外部数据源。

3. （可选）在 `DataExplorationDB` 中为将访问外部数据的用户创建登录名：

    ```sql
    CREATE LOGIN data_explorer WITH PASSWORD = 'My Very Strong Password 1234!';
    ```

    在 `DataExplorationDB` 中为数据库用户创建登录名，并授予 `ADMINISTER DATABASE BULK OPERATIONS` 权限。
    ```sql
    CREATE USER data_explorer FOR LOGIN data_explorer;
    GO
    GRANT ADMINISTER DATABASE BULK OPERATIONS TO data_explorer;
    GO
    ```

4. 使用相对路径和数据源浏览文件的内容：

    ```sql
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
                BULK '/users/NYCTripSmall.parquet',
                DATA_SOURCE = 'ContosoLake'
                FORMAT='PARQUET'
        ) AS [result]
    ```

数据浏览数据库只是一个简单的占位符，在其中可存储实用工具对象。 通过 Synapse SQL 池，可执行更多操作，还可创建一个逻辑数据仓库（基于 Azure 数据源的关系层）。 在本[教程](sql/tutorial-data-analyst.md)中详细了解构建逻辑数据仓库。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用无服务器 Spark 池分析数据](get-started-analyze-spark.md)
