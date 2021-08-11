---
title: 通过 Power BI Professional 连接到 Synapse SQL
description: 在本教程中，我们将完成将 Power BI Desktop 连接到无服务器 SQL 池的步骤。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: dffe6cd263b52f7d8790aa7071f05a5b50a41668
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112540377"
---
# <a name="connect-to-serverless-sql-pool-with-power-bi-professional"></a>通过 Power BI Professional 连接到无服务器 SQL 池

> [!div class="op_single_selector"]
>
> - [Azure Data Studio](get-started-azure-data-studio.md)
> - [Power BI](get-started-power-bi-professional.md)
> - [Visual Studio](../sql/get-started-visual-studio.md)
> - [sqlcmd](../sql/get-started-connect-sqlcmd.md)
> - [SSMS](get-started-ssms.md)

在本教程中，我们将完成将 Power BI Desktop 连接到无服务器 SQL 池的步骤。

## <a name="prerequisites"></a>先决条件

需要使用以下工具发出查询：

- 你选择的 SQL 客户端：

  - Azure Data Studio
  - SQL Server Management Studio

- 已安装 Power BI Desktop

参数：

| 参数                                 | 说明                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| 无服务器 SQL 池服务终结点地址    | 将用作服务器名称                                   |
| 无服务器 SQL 池服务终结点区域     | 将用来确定我们在示例中将使用什么存储 |
| 用来访问终结点的用户名和密码 | 将用来访问终结点                               |
| 用于创建视图的数据库       | 在示例中，我们将从此数据库着手       |

## <a name="first-time-setup"></a>首次设置

在使用示例之前，需要执行两个步骤：

1. 为视图创建数据库
2. 创建供无服务器 SQL 池用来访问存储中的文件的凭据

### <a name="create-database"></a>创建数据库

在此入门文章中，你应创建自己的用作演示的数据库。 创建视图时需要数据库。 你将在本文档的一些示例查询中使用此数据库。

> [!NOTE]
> 数据库仅用于查看元数据，而不用于实际数据。
>
> 请记下使用的数据库名称，稍后需要用到它。

```sql
DROP DATABASE IF EXISTS demo;
```

### <a name="create-credentials"></a>创建凭据

需要先创建凭据，然后才能运行查询。 无服务器 SQL 池服务将使用此凭据访问存储中的文件。

> [!NOTE]
> 你需要创建用于访问存储帐户的凭据。 尽管无服务器 SQL 池可以访问不同区域中的存储，但将存储和 Azure Synapse 工作区置于同一区域中可提供性能更好的体验。

**有关如何为人口普查数据容器创建凭据的代码片段**，请运行：

```sql
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
GO

-- Create credentials for Census Data container which resides in a azure open data storage account
-- There is no secret. We are using public storage account which doesn't need secret
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = '';
GO
```

## <a name="create-a-power-bi-desktop-report"></a>创建 Power BI Desktop 报表

打开 Power BI Desktop 应用程序并选择“获取数据”选项。 

![打开 Power BI Desktop 应用程序并选择“获取数据”。](./media/get-started-power-bi-professional/step-0-open-powerbi.png)

### <a name="step-1---select-data-source"></a>步骤 1 - 选择数据源

在菜单中选择“Azure”  ，然后选择“Azure SQL 数据库”  。
![选择数据源。](./media/get-started-power-bi-professional/step-1-select-data-source.png)

### <a name="step-2---select-database"></a>步骤 2 - 选择数据库

填写视图所在数据库的 URL 以及该数据库的名称。
![选择终结点上的数据库。](./media/get-started-power-bi-professional/step-2-db.png)

## <a name="next-steps"></a>后续步骤

转到[查询存储文件](get-started-azure-data-studio.md)，了解如何使用 Azure Data Studio 连接到无服务器 SQL 池。