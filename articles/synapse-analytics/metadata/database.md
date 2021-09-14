---
title: 共享数据库
description: Azure Synapse Analytics 提供一个共享的元数据模型，通过该模型在无服务器 Apache Spark 池中创建一个数据库后，可以从该数据库的无服务器 SQL 池和 SQL 池引擎访问该数据库。
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: 7b671246d3ee199719dbb3da6bf7820a876791cf
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123428283"
---
# <a name="azure-synapse-analytics-shared-database"></a>Azure Synapse Analytics 共享数据库

Azure Synapse Analytics 允许不同的计算工作区引擎共享数据库和表。 目前，在 Apache Spark 池上创建的数据库和表（Parquet 或 CSV 提供支持）将自动与无服务器 SQL 池引擎进行共享。

使用 Spark 作业创建的数据库会以相同的名称对工作区中所有当前的和将来的 Spark 池（包括无服务器 SQL 池引擎）变得可见。 不能使用无服务器 SQL 池直接在此同步数据库中添加自定义对象（外部表、视图、过程）。

Spark 默认数据库（名为 `default`）还会在无服务器 SQL 池上下文中显示为名为 `default` 的数据库。 不能在 Spark 中创建一个数据库，然后在无服务器 SQL 池中创建另一个同名数据库。

由于数据库以异步方式同步到无服务器 SQL 池，因此它们出现的时间会有延迟。

## <a name="manage-a-spark-created-database"></a>管理 Spark 创建的数据库

如果要管理 Spark 创建的数据库，需要使用 Apache Spark 池。 例如，通过 Spark 池作业创建或删除它。

无法从无服务器 SQL 池修改同步数据库中的对象。

>[!NOTE]
>不能在不同的池中创建多个同名数据库。 如果创建了无服务器 SQL 池数据库，将无法创建同名的 Spark 数据库。 如果数据库是在 Spark 中分别创建的，将无法创建同名的无服务器 SQL 池数据库。

## <a name="security-model"></a>安全模型

Spark 数据库和表及其在 SQL 引擎中的已同步表示形式将在基础存储级别受到保护。

创建数据库的安全主体被视为该数据库的所有者，对该数据库及其对象拥有所有权限。 默认情况下，Synapse 管理员和 Synapse SQL 管理员还将拥有对无服务器 SQL 池中同步对象的所有权限。 不允许在已同步的 SQL 数据库中创建自定义对象（包括用户）。 

要授予安全主体（例如用户、Azure AD 应用或安全组）对外部表所使用的基础数据的访问权限，需要授予他们对文件（例如表的基础数据文件）的 `read (R)` 权限，以及对文件夹（存储文件的文件夹以及根目录上的每个父文件夹）的 `execute (X)` 权限。 可以在[访问控制列表 (ACL)](/azure/storage/blobs/data-lake-storage-access-control) 页上详细了解这些权限。 

例如，在 `https://<storage-name>.dfs.core.windows.net/<fs>/synapse/workspaces/<synapse_ws>/warehouse/mytestdb.db/myparquettable/` 中，安全主体需要对从 `<fs>` 到 `myparquettable` 上的所有文件夹具有 `X` 权限，以及对相应文件夹内的 `myparquettable` 和文件具有 `R` 权限，才能读取数据库（同步数据库或原始数据库）中的表。

如果安全主体需要能够在数据库中创建对象或删除对象，则需要对 `warehouse` 文件夹中的文件夹和文件的附加 `W` 权限。 无法从无服务器 SQL 池修改数据库中的对象，只能从 Spark 进行修改。

## <a name="examples"></a>示例

### <a name="create-and-connect-to-spark-database-with-serverless-sql-pool"></a>使用无服务器 SQL 池创建并连接到 Spark 数据库

首先使用已在工作区中创建的 Spark 群集创建名为 `mytestdb` 的新 Spark 数据库。 例如，可以将 Spark C# 笔记本和以下 .NET for Spark 语句配合使用以实现此目的：

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

在短暂的延迟后，可从无服务器 SQL 池中看到该数据库。 例如，在无服务器 SQL 池中运行以下语句。

```sql
SELECT * FROM sys.databases;
```

请验证结果中是否包含 `mytestdb`。

## <a name="next-steps"></a>后续步骤

- [详细了解 Azure Synapse Analytics 的共享元数据](overview.md)
- [详细了解 Azure Synapse Analytics 的共享元数据表](table.md)
