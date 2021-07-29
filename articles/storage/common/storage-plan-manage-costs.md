---
title: 计划和管理 Azure Blob 存储的成本
description: 了解如何使用 Azure 门户中的成本分析来计划和管理 Azure Blob 存储的成本。
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: 0bad4637f13bbcf02047416499e4f82fdc53eb4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98601310"
---
# <a name="plan-and-manage-costs-for-azure-blob-storage"></a>计划和管理 Azure Blob 存储的成本

本文可帮助你计划和管理 Azure Blob 存储的成本。 首先，使用 Azure 定价计算器估算成本。 创建存储帐户后对其进行优化，以便只为所需的内容付费。 使用成本管理功能来设置预算和监视成本。 还可以查看预测的成本并监视支出趋势以确定可能需要实施措施的领域。

请记住，Blob 存储的成本只是 Azure 账单中每月成本的一部分。 尽管本文介绍了如何估算和管理 Blob 存储的成本，但你需要为 Azure 订阅中使用的所有 Azure 服务和资源（包括第三方服务）付费。 在熟悉了如何管理 Blob 存储的成本之后，可以应用类似的方法来管理订阅中使用的所有 Azure 服务的成本。

## <a name="estimate-costs"></a>估算成本

在创建 Azure 存储帐户并开始向该帐户传输数据之前，先使用 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)估算成本。

1. 在 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)页面上，选择“存储帐户”磁贴。

2. 向下滚动页面，找到“你的估算”的“存储帐户”部分。

3. 从下拉列表选择选项。 

   修改这些下拉列表的值时，成本估算随即发生变化。 该估算值显示估算页面的上角和底部。

   ![显示估算的屏幕截图](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   更改“类型”下拉列表的值时，此工作表上显示的其他选项也会随之更改。 使用“详细信息”部分中的链接详细了解每个选项的含义，以及这些选项如何影响与存储相关的操作的价格。 

4. 修改剩余的选项，查看其对估算的影响。

   > [!NOTE]
   > 可以使用 Azure 预付款（之前称为货币承诺）额度支付 Azure Blob 存储费用。 但是，不能使用 Azure 预付款额度来支付第三方产品和服务（包括 Azure 市场中的）的费用。

## <a name="optimize-costs"></a>优化成本

请考虑使用以下方案来降低成本。 

- 保留存储容量

- 将数据组织到访问层

- 在访问层之间自动移动数据

本部分详细介绍了每个方案。 

#### <a name="reserve-storage-capacity"></a>保留存储容量

可以借助 Azure 存储预留容量来节约 blob 数据的存储成本。 如果你承诺预留一年或三年，Azure 存储预留容量可为标准存储帐户中的块 blob 和 Azure Data Lake Storage Gen2 数据提供容量折扣。 预留在预留期内提供固定数量的存储容量。 Azure 存储预留容量可大幅降低用于块 blob 和 Azure Data Lake Storage Gen2 数据的容量成本。 

有关详细信息，请参阅[借助预留容量优化 Blob 存储的成本](../blobs/storage-blob-reserved-capacity.md)。

#### <a name="organize-data-into-access-tiers"></a>将数据组织到访问层

可通过将 blob 数据放入最经济高效的访问层来降低成本。 在三个专为优化数据使用成本设计的层中选择。 例如，热层的存储成本较高，但访问成本较低。 因此，如果计划频繁访问数据，则热层可能是最经济高效的选择。 如果计划不经常访问数据，冷层或存档层可能最适用，因为它们会提高访问数据的成本，同时降低存储数据的成本 。    

有关详细信息，请参阅 [Azure Blob 存储：热、冷和存档层](../blobs/storage-blob-storage-tiers.md?tabs=azure-portal)。

#### <a name="automatically-move-data-between-access-tiers"></a>在访问层之间自动移动数据

使用生命周期管理策略定期在层之间移动数据，以最大化地节省成本。 这些策略可以使用指定的规则移动数据。 例如，可以创建一个规则，如果 blob 在 90 天内未修改，就将其移动到存档层。 通过创建调整数据访问层的策略，可根据需求设计出成本最低的存储方案。

有关详细信息，请参阅[管理 Azure Blob 存储生命周期](../blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal)

## <a name="create-budgets"></a>创建预算

可以创建[预算](../../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)来管理成本，并创建警报以自动通知利益干系人支出异常和超支风险。 警报基于与预算和成本阈值相比的支出。 预算和警报是针对 Azure 订阅和资源组创建的，作为总体成本监视策略的一部分，它们非常有用。 但是，因为它们设计为在较高级别跟踪成本，因此在管理单个 Azure 服务成本（如 Azure 存储的成本）方面的功能可能有限。

## <a name="monitor-costs"></a>监视成本

将 Azure 资源用于 Azure 存储时，会产生成本。 资源使用情况单位成本因时间间隔（秒、分钟、小时和天）或单位使用情况（字节、MB 等）而异。Azure 存储一经使用，即会产生成本。 可以在 Azure 门户的[成本分析](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)窗格中看到成本。

使用成本分析时，可以在图表中查看不同时间间隔的 Azure 存储成本。 有些示例是按天、当前、上个月和年划分的。 还可以对比预算和预测的成本来查看成本。 切换到时间更长的视图有助于确定支出趋势，以及了解可能超支的领域。 如果已创建预算，还可以轻松查看超支的地方。

>[!NOTE]
> 成本分析支持各种 Azure 帐户类型。 若要查看支持的帐户类型的完整列表，请参阅[了解成本管理数据](../../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。 若要查看成本数据，你至少需要对 Azure 帐户具有读取访问权限。 若要了解如何分配对 Azure 成本管理数据的访问权限，请参阅[分配对数据的访问权限](../../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

在成本分析中查看 Azure 存储成本：

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 打开“成本管理 + 计费”窗口，从菜单中选择“成本管理”，然后选择“成本分析”  。 然后，可以从“范围”下拉列表中更改特定订阅的范围。

   ![显示范围的屏幕截图](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. 若仅查看 Azure 存储的成本，请选择“添加筛选器”，然后选择“服务名称” 。 然后，从列表中选择“存储”。 

   下面的示例只显示了 Azure 存储的成本：

   ![显示按存储筛选的屏幕截图](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

在前面的示例中，你将看到该服务的当前成本。 还显示了按 Azure 区域（位置）和按资源组筛选的成本。 也可以添加其他筛选器（例如：用于查看特定存储帐户的成本的筛选器）。

## <a name="export-cost-data"></a>导出成本数据

还可以将[成本数据导出](../../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)到存储帐户。 当你或其他人需要进行有关成本的额外数据分析时，这非常有用。 例如，财务团队可以使用 Excel 或 Power BI 来分析数据。 可以按每天、每周或每月计划导出成本，并设置自定义的日期范围。 建议导出成本数据来检索成本数据集。

## <a name="next-steps"></a>后续步骤

- 详细了解 Azure 存储的定价方式。 请参阅 [Azure 存储概述定价](https://azure.microsoft.com/pricing/details/storage/)。
- [借助预留容量优化 Blob 存储的成本](../blobs/storage-blob-reserved-capacity.md)。
- 了解[如何通过 Azure 成本管理优化云投资](../../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 详细了解如何通过[成本分析](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)来管理成本。
- 了解如何[防止意外成本](../../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 参与[成本管理](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)引导式学习课程。