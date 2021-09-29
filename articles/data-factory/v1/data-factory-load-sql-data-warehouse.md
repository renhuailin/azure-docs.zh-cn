---
title: 将数 TB 的数据加载到 Azure Synapse Analytics 中
description: 演示如何在 15 分钟内通过 Azure 数据工厂将 1 TB 的数据加载到 Azure Synapse Analytics
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d773c25059f1d5767df735ff89bd7701d4733c4b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601589"
---
# <a name="load-1-tb-into-azure-synapse-analytics-under-15-minutes-with-data-factory"></a>在不到 15 分钟的时间里通过数据工厂将 1 TB 的数据加载到 Azure Synapse Analytics
> [!NOTE]
> 本文适用于数据工厂版本 1。 如果使用当前版本数据工厂服务，请参阅[使用 Azure 数据工厂将数据复制到 Azure Synapse Analytics 或从 Azure Synapse Analytics 复制数据](../connector-azure-sql-data-warehouse.md)。


[Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 是一种基于云的横向扩展数据库，可以处理大量数据（关系数据和非关系数据）。  Azure Synapse Analytics 在大规模并行处理 (MPP) 体系结构的基础上构建，已针对企业数据仓库工作负荷进行优化。  它通过灵活地缩放存储以及独立计算提供云灵活性。

如今通过使用 Azure 数据工厂，Azure Synapse Analytics 入门变得前所未有的简单。  Azure 数据工厂是一个完全托管的基于云的数据集成服务，可用于使用现有系统中的数据填充 Azure Synapse Analytics，而且在评估 Azure Synapse Analytics 并生成分析解决方案时为你节省了宝贵时间。 以下是使用 Azure 数据工厂将数据加载到 Azure Synapse Analytics 的主要优点：

* **轻松设置**：无需脚本的 5 步直观的向导。
* **丰富的数据存储支持**：对一组丰富的本地和基于云的数据存储的内置支持。
* **安全且合规**：通过 HTTPS 或 ExpressRoute 传输数据，并且全局服务可确保数据不会离开地理边界
* **通过使用 PolyBase 提供无与伦比的性能** - 使用 Polybase 能够最高效地将数据移到 Azure Synapse Analytics 中。 使用临时 blob 功能，可以实现所有类型的数据存储（包括 Azure Blob 存储）的高加载速度，默认情况下，Polybase 支持此功能。

本文介绍了如何使用数据工厂复制向导，在 15 分钟内以超过 1.2 GBps 的吞吐量将 1 TB 数据从 Azure Blob 存储加载到 Azure Synapse Analytics。

本文提供了使用复制向导将数据迁移到 Azure Synapse Analytics 的分步说明。

> [!NOTE]
>  有关数据工厂将数据移入/移出 Azure Synapse Analytics 功能的一般信息，请参阅[使用 Azure 数据工厂将数据移入和移出 Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md) 一文。
>
> 还可以使用 Visual Studio、PowerShell 等生成管道。有关在 Azure 数据工厂中使用复制活动的分步说明的快速演练，请参阅[教程：将数据从 Azure Blob 复制到 Azure SQL 数据库](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。  
>
>

## <a name="prerequisites"></a>先决条件
* Azure Blob 存储：此试验使用 Azure Blob 存储 (GRS) 来存储 TPC-H 测试数据集。  如果还没有 Azure 存储帐户，请参阅[如何创建存储帐户](../../storage/common/storage-account-create.md)。
* [TPC-H](http://www.tpc.org/tpch/) 数据：我们将使用 TPC-H 作为测试数据集。  为此，需要使用 TPC-H 工具包中的 `dbgen`，它将有助于生成数据集。  可以从 [TPC 工具](http://www.tpc.org/tpc_documents_current_versions/current_specifications5.asp)下载 `dbgen` 的源代码，并自己进行编译，或从 [GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TPCHTools) 下载已编译的二进制。  使用以下命令运行 dbgen.exe，为分布在 10 个文件中的 `lineitem` 表生成 1 TB 的平面文件：

  * `Dbgen -s 1000 -S **1** -C 10 -T L -v`
  * `Dbgen -s 1000 -S **2** -C 10 -T L -v`
  * …
  * `Dbgen -s 1000 -S **10** -C 10 -T L -v`

    现在将生成的文件复制到 Azure Blob。  请参阅[使用 Azure 数据工厂将数据移入和移出本地文件系统](data-factory-onprem-file-system-connector.md)了解如何使用 ADF 复制执行此操作。    
* Azure Synapse Analytics：此试验将数据加载到通过 6,000 DWu 创建的 Azure Synapse Analytics 中

    有关如何创建 Azure Synapse Analytics 数据库的详细说明，请参阅[创建 Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md)。  若要使用 Polybase 获取到 Azure Synapse Analytics 的最佳加载性能，我们选择性能设置中允许的数据仓库单位 (DWU) 的最大数，即 6,000 DWU。

  > [!NOTE]
  > 从 Azure Blob 加载时，数据加载性能与在 Azure Synapse Analytics 中配置的 DWU 数直接成正比：
  >
  > 加载 1 TB 数据到 1,000 DWU Azure Synapse Analytics 花费 87 分钟（~200 MBps 吞吐量）加载 1 TB 数据到 2,000 DWU Azure Synaps e Analytics花费 46 分钟（~380 MBps 吞吐量）加载 1 TB 数据到 6,000 DWU Azure Synapse Analytics 花费 14 分钟（~1.2 GBps 吞吐量）
  >
  >

    若要创建 6,000 DWU 的专用 SQL 池，可将性能滑块移动到最右侧：

    :::image type="content" source="media/data-factory-load-sql-data-warehouse/performance-slider.png" alt-text="性能滑块":::

    对于未配置为 6,000 DWU 的现有数据库，可以使用 Azure 门户对其进行扩展。  导航到 Azure 门户中的数据库，在“概述”面板中有一个“缩放”按钮，如下图所示：

    :::image type="content" source="media/data-factory-load-sql-data-warehouse/scale-button.png" alt-text="“缩放”按钮":::    

    单击“缩放”按钮以打开以下面板，将滑块移动到最大值，然后单击“保存”按钮。

    :::image type="content" source="media/data-factory-load-sql-data-warehouse/scale-dialog.png" alt-text="“缩放”对话框":::

    此试验使用 `xlargerc` 资源类将数据加载到 Azure Synapse Analytics。

    若要获得可能的最佳吞吐量，需要使用属于 `xlargerc` 资源类的 Azure Synapse Analytics 用户来执行复制操作。  请参阅[更改用户资源类示例](../../synapse-analytics/sql-data-warehouse/resource-classes-for-workload-management.md)，了解如何执行该操作。  
* 通过运行以下 DDL 语句在 Azure Synapse Analytics 数据库中创建目标表架构：

    ```SQL  
    CREATE TABLE [dbo].[lineitem]
    (
        [L_ORDERKEY] [bigint] NOT NULL,
        [L_PARTKEY] [bigint] NOT NULL,
        [L_SUPPKEY] [bigint] NOT NULL,
        [L_LINENUMBER] [int] NOT NULL,
        [L_QUANTITY] [decimal](15, 2) NULL,
        [L_EXTENDEDPRICE] [decimal](15, 2) NULL,
        [L_DISCOUNT] [decimal](15, 2) NULL,
        [L_TAX] [decimal](15, 2) NULL,
        [L_RETURNFLAG] [char](1) NULL,
        [L_LINESTATUS] [char](1) NULL,
        [L_SHIPDATE] [date] NULL,
        [L_COMMITDATE] [date] NULL,
        [L_RECEIPTDATE] [date] NULL,
        [L_SHIPINSTRUCT] [char](25) NULL,
        [L_SHIPMODE] [char](10) NULL,
        [L_COMMENT] [varchar](44) NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    ```
  完成必需的步骤后，现在可以准备使用复制向导配置复制活动。

## <a name="launch-copy-wizard"></a>启动复制向导
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 单击左上角的“创建资源”，单击“智能 + 分析”，然后单击“数据工厂”。
3. 在“新建数据工厂”窗格中：

   1. 输入 **LoadIntoSQLDWDataFactory** 作为 **名称**。
       Azure 数据工厂的名称必须全局唯一。 如果收到错误：“数据工厂名称“LoadIntoSQLDWDataFactory”不可用”，请更改该数据工厂名称（例如改为“yournameLoadIntoSQLDWDataFactory”），并尝试再次创建。 有关数据工厂项目命名规则，请参阅 [Data Factory - Naming Rules](data-factory-naming-rules.md) （数据工厂 - 命名规则）主题。  
   2. 选择 **Azure 订阅**。
   3. 对于资源组，请执行以下步骤之一：
      1. 选择“使用现有资源组”并选择一个现有的资源组。
      2. 选择“新建”并输入资源组的名称。
   4. 选择数据工厂的 **位置**。
   5. 选中位于边栏选项卡底部的“固定到仪表板”复选框。  
   6. 单击“创建”。
4. 完成创建后，将看到如下图所示的“数据工厂”边栏选项卡：

   :::image type="content" source="media/data-factory-load-sql-data-warehouse/data-factory-home-page-copy-data.png" alt-text="数据工厂主页":::
5. 在“数据工厂”主页上，单击“复制数据”磁贴，启动“复制向导”。

   > [!NOTE]
   > 如果 Web 浏览器卡在“正在授权...”处，请禁用或取消选中“阻止第三方 Cookie 和站点数据”设置，或在保持启用该项的状态下为 **login.microsoftonline.com** 创建一个例外，然后尝试再次启动该向导。
   >
   >

## <a name="step-1-configure-data-loading-schedule"></a>步骤 1：配置数据加载计划
第一步是配置数据加载计划。  

在“属性”  页中：

1. 输入 **CopyFromBlobToAzureSqlDataWarehouse** 作为 **任务名称**
2. 选择“立即运行一次”选项。   
3. 单击“下一步”。  

    :::image type="content" source="media/data-factory-load-sql-data-warehouse/copy-wizard-properties-page.png" alt-text="复制向导 - 属性页":::

## <a name="step-2-configure-source"></a>步骤 2：配置源
本部分说明配置源的步骤：包含 1-TB TPC-H 行项目文件的 Azure Blob。

1. 选择“Azure Blob 存储”作为数据存储，并单击“下一步”。

    :::image type="content" source="media/data-factory-load-sql-data-warehouse/select-source-connection.png" alt-text="复制向导 - 选择源页":::

2. 填写 Azure Blob 存储帐户的连接信息，并单击“下一步”。

    :::image type="content" source="media/data-factory-load-sql-data-warehouse/source-connection-info.png" alt-text="复制向导 - 源连接信息":::

3. 选择包含 TPC-H 行项目文件的“文件夹”，并单击“下一步”。

    :::image type="content" source="media/data-factory-load-sql-data-warehouse/select-input-folder.png" alt-text="复制向导 - 选择输入文件夹":::

4. 单击“下一步”时会自动检测文件格式设置。  请检查以确保列分隔符为“|”而非默认的逗号“，”。  预览数据后，单击“下一步”。

    :::image type="content" source="media/data-factory-load-sql-data-warehouse/file-format-settings.png" alt-text="复制向导 - 文件格式设置":::

## <a name="step-3-configure-destination"></a>步骤 3：配置目标
本部分演示如何配置目标：Azure Synapse Analytics 数据库中的 `lineitem` 表。

1. 选择“Azure Synapse Analytics”作为目标存储，并单击“下一步”。

    :::image type="content" source="media/data-factory-load-sql-data-warehouse/select-destination-data-store.png" alt-text="复制向导 - 选择目标数据存储":::

2. 填写 Azure Synapse Analytics 的连接信息。  请确保指定作为 `xlargerc` 角色的成员的用户（有关详细说明，请参阅 **先决条件** 部分），并单击“下一步”。

    :::image type="content" source="media/data-factory-load-sql-data-warehouse/destination-connection-info.png" alt-text="复制向导 - 目标连接信息":::

3. 选择目标表，并单击“下一步”。

    :::image type="content" source="media/data-factory-load-sql-data-warehouse/table-mapping-page.png" alt-text="复制向导 - 表映射页":::

4. 在架构映射页中，保留“应用列映射”选项的未勾选状态，并单击“下一步”。

## <a name="step-4-performance-settings"></a>步骤 4：性能设置

默认选中“允许 polybase”。  单击“下一步”。

:::image type="content" source="media/data-factory-load-sql-data-warehouse/performance-settings-page.png" alt-text="复制向导 - 架构映射页":::

## <a name="step-5-deploy-and-monitor-load-results"></a>步骤 5：部署和监视加载结果
1. 单击“完成”按钮以便部署。

    :::image type="content" source="media/data-factory-load-sql-data-warehouse/summary-page.png" alt-text="复制向导 - 摘要页 1":::

2. 部署完成后，单击 `Click here to monitor copy pipeline` 以监视副本运行进度。 选择在“活动窗口”列表中创建的副本管道。

    :::image type="content" source="media/data-factory-load-sql-data-warehouse/select-pipeline-monitor-manage-app.png" alt-text="复制向导 - 摘要页 2":::

    可以在右侧面板中的“活动窗口资源管理器”中查看副本运行的详细信息，包括从源中读取和写入到目标中的数据量、持续时间以及运行的平均吞吐量。

    如以下屏幕截图所示，将 1 TB 数据从 Azure Blob 存储复制到 Azure Synapse Analytics 花费 14 分钟，从而有效地实现了 1.22 GBps 的吞吐量！

    :::image type="content" source="media/data-factory-load-sql-data-warehouse/succeeded-info.png" alt-text="复制向导 - “成功”对话框":::

## <a name="best-practices"></a>最佳做法
以下是运行 Azure Synapse Analytics 数据库的一些最佳做法：

* 加载到聚集列存储索引时，请使用较大的资源类。
* 若要提高联接效率，请考虑使用基于所选列的哈希分布而非默认的轮循机制分布。
* 若要提高加载速度，请考虑对临时数据使用堆。
* 完成加载到 Azure Synapse Analytics 后，创建统计信息。

请参阅[Azure Synapse Analytics 最佳实践](../../synapse-analytics/sql/best-practices-dedicated-sql-pool.md)，了解详细信息。

## <a name="next-steps"></a>后续步骤
* [数据工厂复制向导](data-factory-copy-wizard.md) - 此文章提供有关复制向导的详细信息。
* [复制活动性能和优化指南](data-factory-copy-activity-performance.md) - 此文章包含参考性能度量和优化指南。