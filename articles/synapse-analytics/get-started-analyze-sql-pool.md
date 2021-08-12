---
title: 教程：开始使用专用 SQL 池分析数据
description: 在本教程中，你将使用纽约市出租车示例数据来探索 SQL 池的分析功能。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 03/24/2021
ms.openlocfilehash: 56115e977603e1f2148f84569373dcf4d351e0c4
ms.sourcegitcommit: 40dfa64d5e220882450d16dcc2ebef186df1699f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113038203"
---
# <a name="analyze-data-with-dedicated-sql-pools"></a>使用专用 SQL 池分析数据

在本教程中，你将使用纽约市出租车数据来探索专用 SQL 池的功能。

## <a name="create-a-dedicated-sql-pool"></a>创建专用 SQL 池

1. 在 Synapse Studio 的左窗格中，选择“管理” > “SQL 池” 。
1. 选择“新建”
1. 对于“SQL 池名称”，请选择“SQLPOOL1” 
1. 对于“性能级别”，请选择“DW100C” 
1. 选择“查看 + 创建” > “创建”。 你的专用 SQL 池将在几分钟内准备就绪。 

您的专用 SQL 池与同样名为 **SQLPOOL1** 的 SQL 数据库相关联。
1. 导航到 **数据** > **工作区**。
1. 应看到名为 SQLPOOL1 的数据库。 如果没有看到，请单击“刷新”。

只要专用 SQL 池处于活动状态，就会使用计费资源。 你可以在稍后暂停池以降低成本。

> [!NOTE] 
> 在工作区中新建专用 SQL 池（之前称为 SQL DW）时，将打开专用 SQL 池预配页面。 预配将在逻辑 SQL 服务器上进行。
## <a name="load-the-nyc-taxi-data-into-sqlpool1"></a>将纽约市出租车数据加载到 SQLPOOL1

1. 在 Synapse Studio 中，导航到“开发”中心，单击“+”按钮以添加新资源，然后新建 SQL 脚本 。
1. 在脚本上方的“连接到”下拉列表中选择池“SQLPOOL1”（在本教程的[步骤 1](./get-started-create-workspace.md) 中创建的池）。
1. 输入以下代码：
    ```
    IF NOT EXISTS (SELECT * FROM sys.objects O JOIN sys.schemas S ON O.schema_id = S.schema_id WHERE O.NAME = 'NYCTaxiTripSmall' AND O.TYPE = 'U' AND S.NAME = 'dbo')
    CREATE TABLE dbo.NYCTaxiTripSmall
        (
         [DateID] int,
         [MedallionID] int,
         [HackneyLicenseID] int,
         [PickupTimeID] int,
         [DropoffTimeID] int,
         [PickupGeographyID] int,
         [DropoffGeographyID] int,
         [PickupLatitude] float,
         [PickupLongitude] float,
         [PickupLatLong] nvarchar(4000),
         [DropoffLatitude] float,
         [DropoffLongitude] float,
         [DropoffLatLong] nvarchar(4000),
         [PassengerCount] int,
         [TripDurationSeconds] int,
         [TripDistanceMiles] float,
         [PaymentType] nvarchar(4000),
         [FareAmount] numeric(19,4),
         [SurchargeAmount] numeric(19,4),
         [TaxAmount] numeric(19,4),
         [TipAmount] numeric(19,4),
         [TollsAmount] numeric(19,4),
         [TotalAmount] numeric(19,4)
        )
    WITH
        (
        DISTRIBUTION = ROUND_ROBIN,
         CLUSTERED COLUMNSTORE INDEX
         -- HEAP
        )
    GO

    COPY INTO dbo.NYCTaxiTripSmall
    (DateID 1, MedallionID 2, HackneyLicenseID 3, PickupTimeID 4, DropoffTimeID 5,
    PickupGeographyID 6, DropoffGeographyID 7, PickupLatitude 8, PickupLongitude 9, 
    PickupLatLong 10, DropoffLatitude 11, DropoffLongitude 12, DropoffLatLong 13, 
    PassengerCount 14, TripDurationSeconds 15, TripDistanceMiles 16, PaymentType 17, 
    FareAmount 18, SurchargeAmount 19, TaxAmount 20, TipAmount 21, TollsAmount 22, 
    TotalAmount 23)
    FROM 'https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet'
    WITH
    (
        FILE_TYPE = 'PARQUET'
        ,MAXERRORS = 0
        ,IDENTITY_INSERT = 'OFF'
    )
    ```
1. 单击“运行”按钮以执行脚本。
1. 此脚本将在 60 秒内完成。 它将 2 百万行纽约市出租车数据加载到一个名为 dbo.NYCTaxiTripSmall 的表中。

## <a name="explore-the-nyc-taxi-data-in-the-dedicated-sql-pool"></a>浏览专用 SQL 池中的纽约市出租车数据

1. 在 Synapse Studio 中，转到“数据”中心。
1. 转到“SQLPOOL1” > “表” 。 
3. 右键单击 dbo.NYCTaxiTripSmall 表，然后选择“新建 SQL 脚本” > “选择前 100 行”  。
4. 等待新的 SQL 脚本创建并运行。
5. 请注意，在 SQL 脚本的顶部，“连接到”自动设置为名为“SQLPOOL1”的 SQL 池 。
6. 将 SQL 脚本的文本替换为此代码并运行。

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.NYCTaxiTripSmall
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount;
    ```

    此查询显示总行程距离和平均行程距离与乘客数之间的关系。
1. 在“SQL 脚本结果”窗口中，将“视图”更改为“图表”，从而以折线图形式查看结果的可视化效果 。
    
## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [分析 Azure 存储帐户中的数据](get-started-analyze-storage.md)
