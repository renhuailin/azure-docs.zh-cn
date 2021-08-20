---
title: 有关评估要迁移到 Azure SQL 托管实例和 Azure SQL 数据库的 SQL 实例的教程
description: 了解如何在 Azure Migrate 中创建 Azure SQL 评估
author: rashi-ms
ms.author: rajosh
ms.topic: tutorial
ms.date: 02/07/2021
ms.openlocfilehash: 33c4b7f00bcba89c973838c7382397c447d3464c
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114204552"
---
# <a name="tutorial-assess-sql-instances-for-migration-to-azure-sql"></a>教程：评估要迁移到 Azure SQL 的 SQL 实例

在迁移到 Azure 的过程中，将评估本地工作负载，以衡量云的就绪性、确定风险以及估算成本和复杂性。
本文介绍如何使用“Azure Migrate: 发现和评估”工具来评估准备迁移到 Azure SQL 的已发现 SQL Server 实例数据库。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 运行基于服务器配置和性能数据的评估。
> * 查看 Azure SQL 评估 

> [!NOTE]
> 教程显示尝试方案的最快路径，并尽可能使用默认选项。 

> [!NOTE]
> 如果 SQL 服务器在非 VMware 平台上运行。 [请使用数据迁移助手评估 Data SQL Server 数据资产的准备情况，以确定是否可将其迁移至 Azure SQL 数据库](/sql/dma/dma-assess-sql-data-estate-to-sqldb)。

## <a name="prerequisites"></a>先决条件

- 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

- 在遵循本教程评估要迁移到 Azure SQL 的 SQL Server 实例之前，请确保已[遵循此教程](tutorial-discover-vmware.md)使用 Azure Migrate 设备发现了要评估的 SQL 实例
- 如果要在现有项目中试用此功能，请确保已完成本文中的[先决条件](how-to-discover-sql-existing-project.md)。


## <a name="run-an-assessment"></a>运行评估
按如下述运行评估：
1. 在“概述”页 >“Windows、Linux 和 SQL Server”中，单击“评估并迁移服务器”。  
    :::image type="content" source="./media/tutorial-assess-sql/assess-migrate.png" alt-text="Azure Migrate 的“概述”页":::
2. 在“Azure Migrate: 发现和评估”中，单击“评估”并选择“Azure SQL”作为评估类型。  
    :::image type="content" source="./media/tutorial-assess-sql/assess.png" alt-text="用于选择“Azure SQL”作为评估类型的下拉列表":::
3. 在“评估服务器”中，可以看到预先选择的评估类型为“Azure SQL”，而发现源默认为“从 Azure Migrate 设备发现的服务器”。  

4. 单击“编辑”查看评估属性。
     :::image type="content" source="./media/tutorial-assess-sql/assess-servers-sql.png" alt-text="可用于自定义评估属性的“编辑”按钮":::
5. 在“评估属性”>“目标属性”中：
    - 在“目标位置”中，选择要迁移到的 Azure 区域。 
        - Azure SQL 配置和成本建议基于你指定的位置。 
    - 在“目标部署类型”中：
        - 如果你希望 Azure Migrate 评估要迁移到 Azure SQL MI 和 Azure SQL DB 的 SQL 实例的就绪状态，并推荐最合适的目标部署选项、目标层级、Azure SQL 配置和每月估算值，请选择“推荐”。 [了解详细信息](concepts-azure-sql-assessment-calculation.md)
        - 如果你只希望评估要迁移到 Azure SQL 数据库的 SQL 实例的就绪状态，并要查看目标层级、Azure SQL 配置和每月估算值，请选择“Azure SQL DB”。
        - 如果你只希望评估要迁移到 Azure SQL 托管实例的 SQL 实例的就绪状态，并要查看目标层级、Azure SQL 配置和每月估算值，请选择“Azure SQL MI”。
    - 在“预留容量”中，指定在迁移后是否要对 SQL 服务器使用预留容量。
        - 如果选择了预留容量选项，则不能指定“折扣(%)”。

6. 在“评估属性”>“评估准则”中：
    - “大小”准则默认为“基于性能”，这意味着，Azure Migrate 将收集与 SQL 实例及其管理的数据库相关的性能指标，以推荐最佳大小的 Azure SQL 数据库和/或 Azure SQL 托管实例配置。 可以指定：
        - **性能历史记录**，指示要作为评估依据的数据持续时间。 （默认持续时间为为一天）
        - **百分位使用率**，指示要用于性能示例的百分位值。 （默认值为第 95 百分位）
    - 在“舒适因子”中，指明要在评估过程中使用的缓冲区。 此帐户用于解决季节性使用情况、短期性能历史记录，以及未来使用量可能会增加等问题。 例如，如果使用舒适因子 2： 
        
        **组件** | **有效利用率** | **添加舒适因子 (2.0)**
        --- | --- | ---
        核心数 | 2  | 4
        内存 | 8 GB | 16 GB
   
7. 在“定价”中：
    - 在“产品/服务/许可计划”中，指定你已注册的 Azure 产品/服务。 目前只能选择“即用即付”或“即用即付开发/测试”。 
        - 可以通过在“即用即付”产品/服务的基础上应用预留容量和 Azure 混合权益来获得额外的折扣。 
        - 可以在“即用即付开发/测试”的基础上应用 Azure 混合权益。 评估目前不支持在“即用即付开发/测试”产品/服务的基础上应用预留容量。
    - 在“服务层级”中选择最合适的服务层级选项，以适应迁移到 Azure SQL 数据库和/或 Azure SQL 托管实例的业务需求： 
        - 如果你希望 Azure Migrate 推荐最适合你的服务器的服务层级，请选择“推荐”。 服务层级可以是“常规用途”或“业务关键”。 了解详细信息
        - 如果你希望 Azure SQL 配置专用于预算导向的工作负载，请选择“常规用途”。
        - 如果你希望 Azure SQL 配置专用于能够极其灵活地应对故障且能实现快速故障转移的低延迟工作负载，请选择“业务关键”。
    - 在“折扣 (%)”中，添加基于 Azure 产品/服务获得的任何特定于订阅的折扣。 默认设置是 0%。
    - 在“货币”中，为帐户选择计费货币。
    - 在“Azure 混合权益”中，指定是否已有 SQL Server 许可证。 如果已有许可证，并且许可证具有 SQL Server 订阅的有效软件保障，则在将许可证引入 Azure 时可以申请 Azure 混合权益。
    - 如有更改，请单击“保存”。
     :::image type="content" source="./media/tutorial-assess-sql/view-all.png" alt-text="“评估属性”中的“保存”按钮":::
8. 在“评估服务器”中单击“下一步”。
9.  在“选择要评估的服务器” > “评估名称”中，指定评估的名称 。
10. 在“选择或创建组”中，选择“新建”并指定组名称 。
     :::image type="content" source="./media/tutorial-assess-sql/assessment-add-servers.png" alt-text="“新建组”按钮的位置":::
11. 选择设备，然后选择要添加到该组的服务器。 然后，单击“下一步”。
12. 在“审阅 + 创建评估”中，查看评估详细信息，然后单击“创建评估”以创建组并运行评估 。
     :::image type="content" source="./media/tutorial-assess-sql/assessment-create.png" alt-text="“查看并创建评估”按钮的位置。":::
13. 创建评估后，转到“Windows、Linux 和 SQL Server” > “Azure Migrate: 发现和评估”磁贴，然后单击 Azure SQL 评估旁边的数字。 
     :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-navigation.png" alt-text="导航到已创建的评估":::
15. 单击要查看的评估名称。

> [!NOTE]
> 由于 Azure SQL 评估是基于性能的评估，我们建议在开始发现后至少等待一天，然后再创建评估。 这为收集具有较高置信度的性能数据提供了时间。 如果仍在进行发现，SQL 实例的就绪状态将标记为“未知”。 理想情况下，在开始发现后，请 **等待指定的性能持续时间（天/周/月）过去**，以便为高置信度分级创建或重新计算评估。 

## <a name="review-an-assessment"></a>查看评估

**若要查看评估**：

1. 转到“Windows、Linux 和 SQL Server” > “Azure Migrate: 发现和评估”，然后单击 Azure SQL 评估旁边的数字。 
2. 单击要查看的评估名称。 例如（估算值和成本仅为示例）：:::image type="content" source="./media/tutorial-assess-sql/assessment-sql-summary.png" alt-text="SQL 评估概述":::
3. 查看评估摘要。 还可以编辑评估属性，或重新计算评估。

#### <a name="discovered-items"></a>发现的项

表示在此评估中已评估的 SQL 服务器、实例和数据库数目。
    
#### <a name="azure-readiness"></a>Azure 迁移就绪性

表示已评估的 SQL 实例的分布： 
    
**目标部署类型（在评估属性中）** | **就绪情况**   
--- | --- |
推荐 |  “为 Azure SQL 数据库准备就绪”、“为 Azure SQL 托管实例准备就绪”、“可能为 Azure VM 准备就绪”、“就绪状态未知”（当正在进行发现，或者存在一些有待修复的发现问题时）
“Azure SQL DB”或“Azure SQL MI”  | “为 Azure SQL 数据库或 Azure SQL 托管实例准备就绪”、“未为 Azure SQL 数据库或 Azure SQL 托管实例准备就绪”、“就绪状态未知”（当正在进行发现，或者存在一些有待修复的发现问题时）
     
可以向下钻取以了解有关在迁移到 Azure SQL 之前可予以修正的迁移问题/警告的详细信息。 [了解详细信息](concepts-azure-sql-assessment-calculation.md) 还可以查看推荐用于迁移到 Azure SQL 数据库和/或托管实例的 Azure SQL 配置。
    
#### <a name="azure-sql-database-and-managed-instance-cost-details"></a>Azure SQL 数据库和托管实例成本详细信息

每月估算成本包括与推荐的 Azure SQL 数据库和/或 Azure SQL 托管实例部署类型相对应的 Azure SQL 配置的计算和存储成本。 [了解详细信息](concepts-azure-sql-assessment-calculation.md#calculate-monthly-costs)


### <a name="review-readiness"></a>查看就绪情况

1. 单击“Azure SQL 就绪状态”。
    :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-readiness.png" alt-text="Azure SQL 就绪状态详细信息":::
1. 在“Azure SQL 就绪状态”中，查看已评估的 SQL 实例的“Azure SQL DB 就绪状态”和“Azure SQL MI 就绪状态”： 
    - **准备就绪**：实例已准备好迁移到 Azure SQL DB/MI，且未出现任何迁移问题或警告。 
        - 准备就绪（带有超链接，并且有蓝色信息图标）：实例已准备好迁移到 Azure SQL DB/MI 且未出现任何迁移问题，但出现了一些需要关注的迁移警告。 可以单击超链接来查看迁移警告和建议的修正指导：:::image type="content" source="./media/tutorial-assess-sql/assess-ready.png" alt-text="包含“就绪”状态的评估结果":::       
    - **未准备就绪**：要迁移到 Azure SQL DB/MI 的实例存在一个或多个迁移问题。 可以单击超链接并查看迁移问题和建议的修正指导。
    - **未知**：Azure Migrate 无法评估就绪状态，因为正在进行发现，或者通知边栏选项卡中显示发现期间出现了需要修复的问题。 如果该问题仍然存在，请联系 Microsoft 支持部门。 
1. 查看按以下对照表确定的推荐 SQL 实例部署类型：

    - **目标部署类型**（在评估属性中选择）：**推荐**

        **Azure SQL DB 就绪状态** | **Azure SQL MI 就绪状态** | **推荐的部署类型** | **是否计算 Azure SQL 配置和估算成本？**
         --- | --- | --- | --- |
        就绪 | 就绪 | Azure SQL DB 或 Azure SQL MI [了解详细信息](concepts-azure-sql-assessment-calculation.md#recommended-deployment-type) | 是
        就绪 | “未准备就绪”或“未知” | Azure SQL DB | 是
        “未准备就绪”或“未知” | 就绪 | Azure SQL MI | 是
        未准备就绪 | 未准备就绪 | 可能为 Azure VM 准备就绪 [了解详细信息](concepts-azure-sql-assessment-calculation.md#potentially-ready-for-azure-vm) | 否
        “未准备就绪”或“未知” | “未准备就绪”或“未知” | 未知 | 否
    
    - **目标部署类型**（在评估属性中选择）：**Azure SQL DB**
    
        **Azure SQL DB 就绪状态** | **是否计算 Azure SQL 配置和估算成本？**
        --- | --- |
        就绪 | 是
        未准备就绪 | 否
        未知 | 否
    
    - **目标部署类型**（在评估属性中选择）：**Azure SQL MI**
    
        **Azure SQL MI 就绪状态** | **是否计算 Azure SQL 配置和估算成本？**
         --- | --- |
        就绪 | 是
        未准备就绪 | 否
        未知 | 否

4. 单击实例名称向下钻取，以查看用户数据库数目和实例详细信息，包括实例属性、计算（限定到实例）和源数据库存储详细信息。
5. 单击用户数据库数目以查看数据库的列表及其详细信息。 例如（估算值和成本仅为示例）：:::image type="content" source="./media/tutorial-assess-sql/assessment-db.png" alt-text="SQL 实例详细信息":::
5. 单击“迁移问题”列中的详细信息，以查看特定目标部署类型的迁移问题和警告。 
    :::image type="content" source="./media/tutorial-assess-sql/assessment-db-issues.png" alt-text="数据库迁移问题和警告":::

### <a name="review-cost-estimates"></a>查看成本估算
评估摘要会针对与推荐的 Azure SQL 数据库和/或托管实例部署类型对应的 Azure SQL 配置显示已估算的每月计算和存储成本。

1. 查看每月总成本。 被评估的组中所有 SQL 实例的成本均将聚合。
    - 成本估算基于为实例推荐的 Azure SQL 配置。 
    - 将显示估算的每月计算和存储成本。 例如（估算值和成本仅为示例）：
    
    :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-cost.png" alt-text="成本详细信息":::

1. 可以在实例级别向下钻取，以查看实例级别的 Azure SQL 配置和估算成本。  
1. 推荐了 Azure SQL 数据库配置后，你还可以向下钻取到数据库列表，以查看每个数据库的 Azure SQL 配置和估算成本。

### <a name="review-confidence-rating"></a>查看置信度分级
Azure Migrate 根据计算所有被评估 SQL 实例和数据库的评估所需的性能/利用率数据点的可用性，将置信度分级分配给所有 Azure SQL 评估。 评级从一星（最低）到五星（最高）。
置信度评级用于判断评估中大小建议的可靠性。 置信度分级如下：

**数据点可用性** | **置信度分级**
--- | ---
0%-20% | 1 星
21%-40% | 2 星
41%-60% | 3 星
61%-80% | 4 星
81%-100% | 5 星

[详细了解](concepts-azure-sql-assessment-calculation.md#confidence-ratings)置信度评级。


## <a name="next-steps"></a>后续步骤

- [详细了解](concepts-azure-sql-assessment-calculation.md)如何计算 Azure SQL 评估。
- 使用 [Azure 数据库迁移服务](../dms/dms-overview.md)开始迁移 SQL 实例和数据库。