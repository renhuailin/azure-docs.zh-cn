---
title: 计划和管理成本
description: 了解如何使用 Azure 门户中的成本分析来计划和管理 Azure SQL 数据库的成本。
author: MashaMSFT
ms.author: mathoma
ms.custom: subject-cost-optimization
ms.service: sql-database
ms.subservice: service-overview
ms.topic: how-to
ms.date: 06/30/2021
ms.openlocfilehash: 29d0bdba30bb75a085e147c72438f22121744870
ms.sourcegitcommit: 8942cdce0108372d6fc5819c71f7f3cf2f02dc60
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2021
ms.locfileid: "113134575"
---
# <a name="plan-and-manage-costs-for-azure-sql-database"></a>计划和管理 Azure SQL 数据库的成本

本文介绍如何计划和管理 Azure SQL 数据库的成本。 

首先，使用 Azure 定价计算器添加 Azure 资源，然后查看估计成本。 在开始使用 Azure SQL 数据库资源之后，请使用“成本管理”功能来设置预算和监视成本。 你还可以查看预测的成本并确定支出趋势，以确定可能需要实施措施的领域。Azure SQL 数据库的成本只是 Azure 账单中每月成本的一部分。 尽管本文介绍了如何为 Azure SQL 数据库规划和管理成本，但你需要为 Azure 订阅中使用的所有 Azure 服务和资源（包括任何第三方服务）付费。


## <a name="prerequisites"></a>先决条件

成本分析支持大多数 Azure 帐户类型，但并非支持所有帐户类型。 若要查看支持的帐户类型的完整列表，请参阅[了解成本管理数据](../../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。 若要查看成本数据，你至少需要对 Azure 帐户具有读取访问权限。 

若要了解如何分配对 Azure 成本管理数据的访问权限，请参阅[分配对数据的访问权限](../../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。


## <a name="sql-database-initial-cost-considerations"></a>SQL 数据库初始成本注意事项

使用 Azure SQL 数据库时，需要注意几个节省成本功能：

### <a name="vcore-or-dtu-purchasing-models"></a>vCore 或 DTU 购买模型 

Azure SQL 数据库支持两种购买模型：vCore 和 DTU。 两种购买模型的付费方式各不相同，因此，在规划和考虑成本时，必须了解最适合工作负荷的模型。 有关 vCore 和 DTU 购买模型的信息，请参阅[在 vCore 和 DTU 购买模型之间进行选择](purchasing-models.md)。

### <a name="provisioned-or-serverless"></a>预配或无服务器

在 vCore 购买模型中，Azure SQL 数据库还支持两种类型的计算层：预配吞吐量和无服务器。 每个计算层的付费方式各不相同，因此，在规划和考虑成本时，必须了解最适合工作负荷的模型。 有关详细信息，请参阅 [vCore 模型概述 - 计算层](service-tiers-sql-database-vcore.md#compute-tiers)。

在基于 vCore 的购买模型的预配计算层中，可以交换现有许可证以获得折扣价格。 有关详细信息，请参阅 [Azure 混合权益 (AHB)](../azure-hybrid-benefit.md)。

### <a name="elastic-pools"></a>弹性池

对于包含具有不断变化且不可预测的使用需求的多个数据库的环境，与预配单一数据库的相同数量相比，弹性池可以节省成本。 有关详细信息，请参阅[弹性池](elastic-pool-overview.md)。

## <a name="estimate-azure-sql-database-costs"></a>估计 Azure SQL 数据库成本

使用 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)为不同的 Azure SQL 数据库配置估计成本。 有关详细信息，请参阅 [Azure SQL 数据库定价](https://azure.microsoft.com/pricing/details/azure-sql-database/)。 

下图中的信息和定价仅用作示例： 

:::image type="content" source="media/cost-management/pricing-calc.png" alt-text="Azure SQL 数据库定价计算器示例":::

还可以估计不同的保留策略选项对成本的影响。 下图中的信息和定价仅用作示例：

:::image type="content" source="media/cost-management/backup-storage.png" alt-text="用于存储的 Azure SQL 数据库定价计算器示例":::


## <a name="understand-the-full-billing-model-for-azure-sql-database"></a>了解 Azure SQL 数据库的完整计费模式

Azure SQL 数据库在 Azure 基础结构上运行，部署新资源时，会随 Azure SQL 数据库产生成本。 需要了解的是，其他基础结构可能会产生成本。  

Azure SQL 数据库（无服务器除外）按可预测的小时费率计费。 如果 SQL 数据库运行时间少于 1 小时，则根据该小时内所用的最高服务层级、预配存储和 IO 来计费，而不考虑使用情况或数据库运行时间是否少于 1 小时。

计费取决于产品 SKU、SKU 的硬件生成以及计量类别。 Azure SQL 数据库可能有以下 SKU：

- 基本 (B)
- 标准 (S)
- 高级 (P)
- 常规用途 (GP)
- 业务关键 (BC)
- 对于存储：异地冗余存储 (GRS)、本地冗余存储 (LRS) 和区域冗余存储 (ZRS)
- 还可以从弃用的资源产品/服务中获得弃用的 SKU

若要了解详细信息，请查看[服务层级](service-tiers-general-purpose-business-critical.md)。 

下表显示了单个数据库的最常见计费计量和可能的 SKU： 

| 度量| 可能的 SKU | 说明 | 
| :----|:----|:----|
| 备份\* | GP/BC/HS | 度量备份使用的存储使用量，按每月存储使用量 (GB) 计费。 | 
| 备份 (LTR) | GRS/LRS/ZRS/GF | 度量通过长期保留配置的长期备份所使用的存储使用量，按存储使用量进行计费。 | 
| 计算  | B/S/P/GP/BC  | 度量每小时计算资源的使用量。 | 
| 计算（主副本/已命名副本） | HS | 度量 HS 主副本的每小时计算资源的使用量。 
| 计算（HA 副本）             | HS | 度量 HS 次要副本的每小时计算资源的使用量。 | 
| 计算（ZR 加载项）              | GP | 度量区域冗余加载项副本的每分钟计算资源的使用量。 | 
| 计算（无服务器）             | GP | 度量每分钟无服务器计算资源的使用量。  | 
| 许可证 | GP/BC/HS | 按月应计 SQL Server 许可证账单。 | 
| 存储 | B/S\*/P\*/G/BC/HS | 根据每小时存储的数据量按月计费。 |

\* 在 DTU 购买模型中，提供了一组用于数据和备份的初始存储，无需额外付费。 存储大小取决于所选服务层级。 额外的数据存储可在标准层和高级层中购买。 有关详细信息，请参阅 [Azure SQL 数据库定价](https://azure.microsoft.com/pricing/details/azure-sql-database/)。 

下表显示了弹性池的最常见计费计量和可能的 SKU： 

| 度量| 可能的 SKU | 说明 | 
|:----|:----|:----|
| 备份\* | GP/BC  | 度量备份使用的存储使用量，每月按 GB/小时计费。 | 
| 计算  | B/S/P/GP/BC | 度量每小时计算资源的使用量，如 vCore 和内存或 DTU。 | 
| 许可证 | GP/BC | 按月应计 SQL Server 许可证账单。 | 
| 存储 | B/S\*/P\*/GP/HS | 根据每小时使用存储空间存储在驱动器上的数据量，以及每秒兆字节的吞吐量 (MBPS) 按月计费。 |

\* 在 DTU 购买模型中，提供了一组用于数据和备份的初始存储，无需额外付费。 存储大小取决于所选服务层级。 额外的数据存储可在标准层和高级层中购买。 有关详细信息，请参阅 [Azure SQL 数据库定价](https://azure.microsoft.com/pricing/details/azure-sql-database/)。 

### <a name="using-monetary-credit-with-azure-sql-database"></a>将货币额度用于 Azure SQL 数据库

可以使用 Azure 预付款（之前称为货币承诺）额度支付 Azure SQL 数据库费用。 但是，不能使用 Azure 预付款额度来支付第三方产品和服务的费用（包括 Azure 市场中的第三方产品和服务）。

## <a name="review-estimated-costs-in-the-azure-portal"></a>在 Azure 门户中查看估计成本

完成创建 Azure SQL 数据库的过程后，可以在计算层的配置过程中查看估计成本。 

若要访问此屏幕，请选择“创建 SQL 数据库”页的“基本信息”选项卡上的“配置数据库”。 下图中的信息和定价仅用作示例：

  :::image type="content" source="media/cost-management/cost-estimate.png" alt-text="显示 Azure 门户中的成本估计的示例":::

如果你的 Azure 订阅有支出限制，Azure 将阻止超过额度金额的支出。 创建和使用 Azure 资源时，将使用额度。 达到额度限制时，将在该计费周期的剩余时间内禁用部署的资源。 你无法更改额度限制，但可以删除该限制。 有关支出限制的详细信息，请参阅 [Azure 支出限制](../../cost-management-billing/manage/spending-limit.md)。

## <a name="monitor-costs"></a>监视成本

开始使用 Azure SQL 数据库后，可以在门户中查看估计成本。 请按照以下步骤查看成本估计：

1. 登录到 Azure 门户并导航到 Azure SQL 数据库的资源组。 可以通过导航到数据库并在“概述”部分中选择“资源组”来查找资源组。
1. 在菜单中，选择“成本分析”。
1. 查看“累计成本”，并将底部的图表设置为“服务名称”。 此图显示了当前 SQL 数据库成本的估计值。 若要缩小整个页面以查看 Azure SQL 数据库的成本，请选择“添加筛选器”，然后选择“Azure SQL 数据库”。 下图中的信息和定价仅用作示例：

   :::image type="content" source="media/cost-management/cost-analysis.png" alt-text="在 Azure 门户中显示累计成本的示例":::

从这里，你可以自行探索成本。 有关不同成本分析设置的详细信息，请参阅[开始分析成本](../../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="create-budgets"></a>创建预算

可以创建[预算](../../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)来管理成本，并创建[警报](../../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)以自动通知利益干系人支出异常和超支风险。 警报基于与预算和成本阈值相比的支出。 预算和警报是针对 Azure 订阅和资源组创建的，作为总体成本监视策略的一部分，它们非常有用。 

如果希望增加监视中的精度，可以在 Azure 中使用筛选器为特定资源或服务创建预算。 筛选器可帮助确保不会意外创建新资源。 有关创建预算时筛选器选项的详细信息，请参阅[对选项进行分组和筛选](../../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="export-cost-data"></a>导出成本数据

还可以将[成本数据导出](../../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)到存储帐户。 当你需要对成本进行进一步的数据分析时，这很有帮助。 例如，财务团队可以使用 Excel 或 Power BI 来分析数据。 可以按每天、每周或每月计划导出成本，并设置自定义的日期范围。 建议导出成本数据来检索成本数据集。

## <a name="other-ways-to-manage-and-reduce-costs-for-azure-sql-database"></a>管理和降低 Azure SQL 数据库成本的其他方式

Azure SQL 数据库还可以根据应用程序需求，增加或减少资源来控制成本。 有关详细信息，请参阅[动态缩放数据库资源](scale-resources.md)。

通过预付为期一到三年的计算资源预留费用来节省资金。 有关详细信息，请参阅[通过预留容量节省资源成本](reserved-capacity-overview.md)。


## <a name="next-steps"></a>后续步骤

- 了解[如何通过 Azure 成本管理优化云投资](../../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 详细了解如何通过[成本分析](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)来管理成本。
- 了解如何[防止意外成本](../../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 参与[成本管理](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)引导式学习课程。