---
title: 通过 VSTS 连接到专用 SQL 池（以前称为 SQL DW）
description: 使用 Visual Studio 在 Azure Synapse Analytics 中查询专用 SQL 池（以前称为 SQL DW）。
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/15/2019
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 0baf2396b7c5af103f0b3aa223d0bccf725babbe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104584136"
---
# <a name="connect-to-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-with-visual-studio-and-ssdt"></a>使用 Visual Studio 和 SSDT 连接到 Azure Synapse Analytics 中的专用 SQL 池（以前称为 SQL DW）

> [!div class="op_single_selector"]
> * [Azure Data Studio](../sql/get-started-azure-data-studio.md)
> * [Power BI](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

使用 Visual Studio 只需几分钟即可查询 Azure Synapse 中的专用 SQL 池（以前称为 SQL DW）。 此方法使用 Visual Studio 2019 中的 SQL Server Data Tools (SSDT) 扩展。 

## <a name="prerequisites"></a>先决条件
要使用本教程，需要：

* 现有专用 SQL 池（以前称为 SQL DW）。 若要创建一个，请参阅[创建专用 SQL 池（以前称为 SQL DW）](create-data-warehouse-portal.md)。
* 适用于 Visual Studio 的 SSDT。 如果安装了 Visual Studio，则可能已有 SSDT for Visual Studio。 有关安装指说明和选项，请参阅 [安装 Visual Studio 和 SSDT](sql-data-warehouse-install-visual-studio.md)。
* 完全限定的 SQL Server 名称。 若要查找此信息，请参阅[连接到专用 SQL 池（以前称为 SQL DW）](sql-data-warehouse-connect-overview.md)。

## <a name="1-connect-to-your-dedicated-sql-pool-formerly-sql-dw"></a>1.连接到专用 SQL 池（以前称为 SQL DW）
1. 打开 Visual Studio 2019。
2. 通过选择“视图”   > “SQL Server 对象资源管理器”  打开 SQL Server 对象资源管理器。
   
    ![SQL Server 对象资源管理器](./media/sql-data-warehouse-query-visual-studio/open-ssdt.png)
3. 单击“添加 SQL Server”  图标。
   
    ![添加 SQL 服务器](./media/sql-data-warehouse-query-visual-studio/add-server.png)
4. 填写“连接到服务器”窗口中的字段。
   
    ![连接到服务器](./media/sql-data-warehouse-query-visual-studio/connection-dialog.png)
   
   * **服务器名称**。 输入前面标识的 **服务器名称** 。
   * **身份验证**。 选择“SQL Server 身份验证”  或“Active Directory 集成身份验证”  。
   * “用户名”  和“密码”  。 如果上面选择了 SQL Server 身份验证，请输入用户名和密码。
   * 单击“连接”  。
5. 要浏览，请展开 Azure SQL 服务器。 可以查看与服务器关联的数据库。 展开 AdventureWorksDW 以查看示例数据库中的表。
   
    ![浏览 AdventureWorksDW](./media/sql-data-warehouse-query-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2.运行示例查询
现在，已建立了与数据库的连接，接下来让我们编写查询。

1. 在 SQL Server 对象资源管理器中右键单击数据库。
2. 选择“新建查询”  。 此时将打开一个新的查询窗口。
   
    ![新建查询](./media/sql-data-warehouse-query-visual-studio/new-query2.png)
3. 将以下 T-SQL 查询复制到查询窗口中：
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. 通过单击绿色箭头或使用以下快捷方式运行查询：`CTRL`+`SHIFT`+`E`。
   
    ![运行查询](./media/sql-data-warehouse-query-visual-studio/run-query.png)
5. 查看查询结果。 在此示例中，FactInternetSales 表包含 60398 行。
   
    ![查询结果](./media/sql-data-warehouse-query-visual-studio/query-results.png)

## <a name="next-steps"></a>后续步骤
可以进行连接和查询后，接下来请尝试[使用 Power BI 可视化数据](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)。

若要为你的环境配置 Azure Active Directory 身份验证，请参阅[向专用 SQL 池（以前称为 SQL DW）进行身份验证](sql-data-warehouse-authentication.md)。