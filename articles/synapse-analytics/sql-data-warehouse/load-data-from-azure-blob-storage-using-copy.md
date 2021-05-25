---
title: 教程：加载纽约出租车数据
description: 教程使用 Azure 门户和 SQL Server Management Studio 从 Synapse SQL 的 Azure Blob 加载纽约出租车数据。
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/23/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 7ede40aba8e2d36e4262b4bc89a35f5d67079e0e
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567496"
---
# <a name="tutorial-load-the-new-york-taxicab-dataset"></a>教程：加载纽约出租车数据集

本教程使用 [COPY 语句](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true)从 Azure Blob 存储帐户加载纽约出租车数据集。 本教程使用 [Azure 门户](https://portal.azure.com)和 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SSMS) 执行以下操作：

> [!div class="checklist"]
>
> * 创建专用于加载数据的用户
> * 为示例数据集创建表 
> * 使用 COPY T-SQL 语句将数据加载到数据仓库
> * 查看正在加载的数据的进度

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="before-you-begin"></a>开始之前

开始本教程之前，请下载并安装最新版 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (SSMS)。  

本教程假设你已根据以下[教程](./create-data-warehouse-portal.md#connect-to-the-server-as-server-admin)创建 SQL 专用池。

## <a name="create-a-user-for-loading-data"></a>创建用于加载数据的用户

服务器管理员帐户用于执行管理操作，不适合对用户数据运行查询。 加载数据是一种内存密集型操作。 内存最大值根据配置的[数据仓库单位](what-is-a-data-warehouse-unit-dwu-cdwu.md)和[资源类](resource-classes-for-workload-management.md)定义。

最好创建专用于加载数据的登录名和用户。 然后，将加载用户添加到启用相应最大内存分配的[资源类](resource-classes-for-workload-management.md)。

以服务器管理员身份连接，以便创建登录名和用户。 使用以下步骤创建名为 LoaderRC20 的登录名和用户。 然后将该用户分配到 staticrc20 资源类。

1. 在 SSMS 中右键单击“master”，然后在显示的下拉菜单中选择“新建查询”。 此时将打开一个新的查询窗口。

    ![在 Master 中新建查询](./media/load-data-from-azure-blob-storage-using-polybase/create-loader-login.png)

2. 在查询窗口中，输入以下 T-SQL 命令，创建一个名为 LoaderRC20 的登录名和用户，并将“a123STRONGpassword!”替换为自己的密码。

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    ```

3. 选择“执行”。

4. 右键单击“mySampleDataWarehouse”，然后选择“新建查询”。 此时会打开一个新的查询窗口。  

    ![针对示例数据仓库的新查询](./media/load-data-from-azure-blob-storage-using-polybase/create-loading-user.png)

5. 输入以下 T-SQL 命令，为 LoaderRC20 登录名创建名为 LoaderRC20 的数据库用户。 第二行为新用户授予对新数据仓库的 CONTROL 权限。  这些权限类似于使用户成为数据库所有者。 第三行将新用户添加为 staticrc20 [资源类](resource-classes-for-workload-management.md)的成员。

    ```sql
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
    EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
    ```

6. 选择“执行”。

## <a name="connect-to-the-server-as-the-loading-user"></a>以加载用户的身份连接到服务器

加载数据的第一步是以 LoaderRC20 的身份登录。  

1. 在对象资源管理器中选择“连接”下拉菜单，然后选择“数据库引擎”。 此时会显示“连接到服务器”对话框。

    ![使用新登录名连接](./media/load-data-from-azure-blob-storage-using-polybase/connect-as-loading-user.png)

2. 输入完全限定的服务器名称，并输入“LoaderRC20”作为登录名。  输入 LoaderRC20 的密码。

3. 选择“连接”  。

4. 当连接准备就绪时，对象资源管理器中出现两个服务器连接。 一个是作为 ServerAdmin 连接，另一个是作为 MedRCLogin 连接。

    ![连接成功](./media/load-data-from-azure-blob-storage-using-polybase/connected-as-new-login.png)

## <a name="create-tables-for-the-sample-data"></a>为示例数据创建表

已准备好开始将数据加载到新的数据仓库。 本教程的此部分说明如何使用 COPY 语句从 Azure 存储 Blob 加载纽约市出租车数据。 若要获得进一步的参考以了解如何将数据置于 Azure Blob 存储或直接从源加载数据，请参阅[加载概述](design-elt-data-loading.md)。

运行以下 SQL 脚本，并指定要加载的数据的相关信息。 此信息包括数据所在的位置、数据内容的格式以及数据的表定义。

1. 在前一节中，已经以 LoaderRC20 的身份登录数据仓库。 在 SSMS 中，右键单击 LoaderRC20 连接，然后选择“新建查询”。  此时会显示一个新的查询窗口。

    ![新的加载查询窗口](./media/load-data-from-azure-blob-storage-using-polybase/new-loading-query.png)

2. 比较查询窗口和之间的图像。  验证新的查询窗口以 LoaderRC20 的身份运行，并对 MySampleDataWarehouse 数据库执行查询。 使用此查询窗口执行所有加载步骤。

7. 运行以下 T-SQL 语句以创建表：

    ```sql
    CREATE TABLE [dbo].[Date]
    (
        [DateID] int NOT NULL,
        [Date] datetime NULL,
        [DateBKey] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DaySuffix] varchar(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeek] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfQuarter] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfYear] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfMonth] varchar(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Month] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Quarter] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [QuarterName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Year] char(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [YearName] char(7) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthYear] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MMYYYY] char(6) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FirstDayOfMonth] date NULL,
        [LastDayOfMonth] date NULL,
        [FirstDayOfQuarter] date NULL,
        [LastDayOfQuarter] date NULL,
        [FirstDayOfYear] date NULL,
        [LastDayOfYear] date NULL,
        [IsHolidayUSA] bit NULL,
        [IsWeekday] bit NULL,
        [HolidayUSA] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Geography]
    (
        [GeographyID] int NOT NULL,
        [ZipCodeBKey] varchar(10) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [County] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [City] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [State] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Country] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [ZipCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[HackneyLicense]
    (
        [HackneyLicenseID] int NOT NULL,
        [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Medallion]
    (
        [MedallionID] int NOT NULL,
        [MedallionBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [MedallionCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Time]
    (
        [TimeID] int NOT NULL,
        [TimeBKey] varchar(8) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HourNumber] tinyint NOT NULL,
        [MinuteNumber] tinyint NOT NULL,
        [SecondNumber] tinyint NOT NULL,
        [TimeInSecond] int NOT NULL,
        [HourlyBucket] varchar(15) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [DayTimeBucketGroupKey] int NOT NULL,
        [DayTimeBucket] varchar(100) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Trip]
    (
        [DateID] int NOT NULL,
        [MedallionID] int NOT NULL,
        [HackneyLicenseID] int NOT NULL,
        [PickupTimeID] int NOT NULL,
        [DropoffTimeID] int NOT NULL,
        [PickupGeographyID] int NULL,
        [DropoffGeographyID] int NULL,
        [PickupLatitude] float NULL,
        [PickupLongitude] float NULL,
        [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DropoffLatitude] float NULL,
        [DropoffLongitude] float NULL,
        [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [PassengerCount] int NULL,
        [TripDurationSeconds] int NULL,
        [TripDistanceMiles] float NULL,
        [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FareAmount] money NULL,
        [SurchargeAmount] money NULL,
        [TaxAmount] money NULL,
        [TipAmount] money NULL,
        [TollsAmount] money NULL,
        [TotalAmount] money NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Weather]
    (
        [DateID] int NOT NULL,
        [GeographyID] int NOT NULL,
        [PrecipitationInches] float NOT NULL,
        [AvgTemperatureFahrenheit] float NOT NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    ```
    

## <a name="load-the-data-into-your-data-warehouse"></a>将数据加载到数据仓库

本部分使用 [COPY 语句](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true)从 Azure 存储 Blob 加载示例数据。  

> [!NOTE]
> 本教程直接将数据加载到最终表。 对于生产工作负载，通常要加载到临时表中。 数据在临时表中时，可以执行任何必要的转换。 

1. 运行以下语句以加载数据：

    ```sql
    COPY INTO [dbo].[Date]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Date'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Date] - Taxi dataset');
    
    
    COPY INTO [dbo].[Geography]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Geography'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Geography] - Taxi dataset');
    
    COPY INTO [dbo].[HackneyLicense]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/HackneyLicense'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[HackneyLicense] - Taxi dataset');
    
    COPY INTO [dbo].[Medallion]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Medallion'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Medallion] - Taxi dataset');
    
    COPY INTO [dbo].[Time]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Time'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Time] - Taxi dataset');
    
    COPY INTO [dbo].[Weather]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Weather'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Weather] - Taxi dataset');
    
    COPY INTO [dbo].[Trip]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = '|',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A',
        COMPRESSION = 'GZIP'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Trip] - Taxi dataset');
    ```

2. 在加载数据的同时查看数据。 假设要加载几个 GB 的数据，并将其压缩成高性能群集列存储索引。 运行以下使用动态管理视图 (DMV) 的查询以显示负载的状态。

    ```sql
    SELECT  r.[request_id]                           
    ,       r.[status]                               
    ,       r.resource_class                         
    ,       r.command
    ,       sum(bytes_processed) AS bytes_processed
    ,       sum(rows_processed) AS rows_processed
    FROM    sys.dm_pdw_exec_requests r
                  JOIN sys.dm_pdw_dms_workers w
                         ON r.[request_id] = w.request_id
    WHERE [label] = 'COPY : Load [dbo].[Date] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Geography] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[HackneyLicense] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Medallion] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Time] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Weather] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Trip] - Taxi dataset' 
    and session_id <> session_id() and type = 'WRITER'
    GROUP BY r.[request_id]                           
    ,       r.[status]                               
    ,       r.resource_class                         
    ,       r.command;
    ```
    
3. 查看所有系统查询。

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

4. 结果让人欣慰，数据已顺利载入数据仓库。

    ![查看已加载的表](./media/load-data-from-azure-blob-storage-using-polybase/view-loaded-tables.png)

## <a name="clean-up-resources"></a>清理资源

需要为加载到数据仓库中的计算资源和数据付费。 这些需要单独计费。

* 如果想要将数据保留在存储中，可以在不使用数据仓库时暂停计算。 暂停计算后，仅需为数据存储付费，并且随时都可在准备处理数据时恢复计算。
* 如果不想支付将来的费用，则可以删除数据仓库。

请按照下列步骤按需清理资源。

1. 登录到 [Azure 门户](https://portal.azure.com)，选择你的数据仓库。

    ![清理资源](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. 若要暂停计算，请选择“暂停”按钮。 暂停数据仓库后，可看到“启动”按钮。  若要恢复计算，请选择“启动”。

3. 若要删除数据仓库，以便不再为计算或存储付费，请选择“删除”。

4. 要删除所创建的服务器，请选择上图所示的“mynewserver-20180430.database.windows.net”，然后选择“删除” 。  请审慎执行此操作，因为删除服务器会删除分配给该服务器的所有数据库。

5. 若要删除资源组，请选择“myResourceGroup”，然后选择“删除资源组”。

## <a name="next-steps"></a>后续步骤

在本教程中，已学习了如何创建数据仓库以及用于加载数据的用户。 使用简单的 [COPY 语句](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#examples)将数据加载到数据仓库。

完成了以下操作：
> [!div class="checklist"]
>
> * 在 Azure 门户中创建数据仓库
> * 在 Azure 门户中设置服务器级防火墙规则
> * 使用 SSMS 连接到数据仓库
> * 创建了专用于加载数据的用户
> * 为示例数据创建表
> * 使用 COPY T-SQL 语句将数据加载到数据仓库
> * 查看了正在加载的数据的进度

转到开发概述，了解如何将现有数据库迁移到 Azure Synapse Analytics：

> [!div class="nextstepaction"]
> [将现有数据库迁移到 Azure Synapse Analytics 的设计决策](sql-data-warehouse-overview-develop.md)

有关加载示例和参考的详细信息，请查看以下文档：

- [COPY 语句参考文档](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax)
- [每个身份验证方法的 COPY 示例](./quickstart-bulk-load-copy-tsql-examples.md)
- [单个表的 COPY 快速入门](./quickstart-bulk-load-copy-tsql.md)
