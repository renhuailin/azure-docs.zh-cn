---
title: 计划和管理 Azure Blob 存储的成本
description: 了解如何在 Azure 门户中使用成本分析来计划和管理 Azure Blob 存储的成本。
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: 5c50cffb561076dd7d9c4af8e5b4654784cc3a01
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128555353"
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

## <a name="understand-the-full-billing-model-for-azure-blob-storage"></a>了解 Azure Blob 存储的完整计费模型

Azure Blob 存储在 Azure 基础结构上运行。当你部署新资源时，该基础结构会产生成本。 用户务必了解，是否可能产生其他基础结构成本。

### <a name="how-youre-charged-for-azure-blob-storage"></a>Azure Blob 存储的计费方式

创建或使用 Blob 存储资源时，将按以下计量计费：

| 计量 | 单位 |
|---|---|
| 数据存储 | 每 GB/每月|
| Operations | 每事务 |
| 数据传输 | 每 GB |
| 元数据 | 每 GB/每月<sup>1 |
| Blob 索引标记 | 每标记<sup>2  |
| 更改源 | 每个记录的更改<sup>2 |
| 加密范围 | 每月<sup>2 |
| 查询加速 | 扫描的每 GB 和返回的每 GB |

<sup>1</sup> 仅适用于具有分层命名空间的帐户。<br />
<sup>2</sup> 仅当启用了该功能时才适用。<br />

数据流量还可能会产生网络成本。 请参阅[带宽定价](https://azure.microsoft.com/pricing/details/data-transfers/)。

在计费周期结束时，将对每个计量标准的费用求和。 账单或发票会将所有 Azure Blob 存储成本显示在一个部分中。 每个计量标准都有单独的一行项目。

数据存储和元数据按每月每 GB 计费。 对于存储时间不到一个月的数据和元数据，你可以通过计算每 GB 每天的成本来估算对每月账单的影响。 你可以使用类似的方法来估算使用时间不到一个月的加密范围的成本。 任意给定月份中的天数有所不同。 因此，若要获得给定月份的成本的最佳近似值，请确保将每月成本除以该月的天数。

### <a name="finding-the-unit-price-for-each-meter"></a>查找每个计量的单价

若要查找单价，请打开正确的定价页。 如果已在帐户上启用了分层命名空间功能，请参阅 [Azure Data Lake Storage Gen2 定价](https://azure.microsoft.com/pricing/details/storage/data-lake/)页。 如果尚未启用此功能，请参阅[块 blob 定价](https://azure.microsoft.com/pricing/details/storage/blobs/)页。

在定价页中，请应用合适的冗余、区域和货币筛选器。 每个计量的价格会显示在一个表中。 价格因帐户中的其他设置（例如数据冗余选项、访问层和性能层）而异。

### <a name="flat-namespace-accounts-and-transaction-pricing"></a>平面命名空间帐户和事务定价

客户端可以使用你的帐户的 Blob 存储终结点或 Data Lake Storage 终结点来发出请求。 若要详细了解存储帐户终结点，请参阅[存储帐户终结点](storage-account-overview.md#storage-account-endpoints)。

显示在[块 blob 定价](https://azure.microsoft.com/pricing/details/storage/blobs/)页中的事务价格仅适用于使用 Blob 存储终结点（例如 `https://<storage-account>.blob.core.windows.net`）的请求。 列出的价格不适用于使用 Data Lake Storage Gen2 终结点（例如 `https://<storage-account>.dfs.core.windows.net`）的请求。 若要了解这些请求的事务价格，请打开 [Azure Data Lake Storage Gen2 定价](https://azure.microsoft.com/pricing/details/storage/data-lake/)页并选择“平面命名空间”选项。

> [!div class="mx-imgBorder"]
> ![平面命名空间选项](media/storage-plan-manage-costs/select-flat-namespace.png)

对 Data Lake Storage Gen2 终结点的请求可能来自以下任何源：

- 使用 Azure Blob 文件系统驱动程序或 [ABFS 驱动程序](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html)的工作负荷。

- 使用 [Azure Data Lake Store REST API](/rest/api/storageservices/data-lake-storage-gen2) 的 REST 调用

- 使用 Azure 存储客户端库中的 Data Lake Storage Gen2 API 的应用程序。

### <a name="using-azure-prepayment-with-azure-blob-storage"></a>对 Azure Blob 存储使用 Azure 预付款

可以使用 Azure 预付款（之前称为货币承诺）额度支付 Azure Blob 存储费用。 但是，不能使用 Azure 预付款额度来支付第三方产品和服务（包括 Azure 市场中的）的费用。

## <a name="optimize-costs"></a>优化成本

请考虑使用以下方案来降低成本。

- 保留存储容量

- 将数据组织到访问层

- 在访问层之间自动移动数据

本部分详细介绍了每个方案。

#### <a name="reserve-storage-capacity"></a>保留存储容量

可以借助 Azure 存储预留容量来节约 blob 数据的存储成本。 如果你承诺预留一年或三年，Azure 存储预留容量可为标准存储帐户中的块 blob 和 Azure Data Lake Storage Gen2 数据提供容量折扣。 预留在预留期内提供固定数量的存储容量。 Azure 存储预留容量可大幅降低用于块 blob 和 Azure Data Lake Storage Gen2 数据的容量成本。

若要了解详细信息，请参阅[借助预留容量优化 Blob 存储的成本](../blobs/storage-blob-reserved-capacity.md)。

#### <a name="organize-data-into-access-tiers"></a>将数据组织到访问层

可通过将 blob 数据放入最经济高效的访问层来降低成本。 在三个专为优化数据使用成本设计的层中选择。 例如，热层的存储成本较高，但访问成本较低。 因此，如果计划频繁访问数据，则热层可能是最经济高效的选择。 如果计划不经常访问数据，冷层或存档层可能最适用，因为它们会提高访问数据的成本，同时降低存储数据的成本 。

若要了解详细信息，请参阅 [Azure Blob 存储：热、冷和存档层](../blobs/storage-blob-storage-tiers.md?tabs=azure-portal)。

#### <a name="automatically-move-data-between-access-tiers"></a>在访问层之间自动移动数据

使用生命周期管理策略定期在层之间移动数据，以最大化地节省成本。 这些策略可以使用指定的规则移动数据。 例如，可以创建一个规则，如果 blob 在 90 天内未修改，就将其移动到存档层。 通过创建调整数据访问层的策略，可根据需求设计出成本最低的存储方案。

若要了解详细信息，请参阅[管理 Azure Blob 存储生命周期](../blobs/lifecycle-management-overview.md?tabs=azure-portal)

## <a name="create-budgets"></a>创建预算

可以创建[预算](../../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)来管理成本，并创建警报以自动通知利益干系人支出异常和超支风险。 警报基于与预算和成本阈值相比的支出。 预算和警报是针对 Azure 订阅和资源组创建的，作为总体成本监视策略的一部分，它们非常有用。 但是，因为它们设计为在较高级别跟踪成本，因此在管理单个 Azure 服务成本（如 Azure 存储的成本）方面的功能可能有限。

## <a name="monitor-costs"></a>监视成本

将 Azure 资源用于 Azure 存储时，会产生成本。 资源使用情况单位成本因时间间隔（秒、分钟、小时和天）或单位使用情况（字节、MB 等）而异。Azure 存储一经使用，即会产生成本。 可以在 Azure 门户的[成本分析](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)窗格中看到成本。

使用成本分析时，可以在图表中查看不同时间间隔的 Azure 存储成本。 有些示例是按天、当前、上个月和年划分的。 还可以对比预算和预测的成本来查看成本。 切换到时间更长的视图有助于确定支出趋势，以及了解可能超支的领域。 如果已创建预算，还可以轻松查看超支的地方。

> [!NOTE]
> 成本分析支持各种 Azure 帐户类型。 若要查看支持的帐户类型的完整列表，请参阅[了解成本管理数据](../../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。 若要查看成本数据，你至少需要对 Azure 帐户具有读取访问权限。 若要了解如何分配对 Azure 成本管理数据的访问权限，请参阅[分配对数据的访问权限](../../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

在成本分析中查看 Azure 存储成本：

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 打开“成本管理 + 计费”窗口，从菜单中选择“成本管理”，然后选择“成本分析”  。 然后，可以从“范围”下拉列表中更改特定订阅的范围。

   ![显示范围的屏幕截图](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. 若仅查看 Azure 存储的成本，请选择“添加筛选器”，然后选择“服务名称” 。 然后，从列表中选择“存储”。

   下面的示例只显示了 Azure 存储的成本：

   ![显示按存储筛选的屏幕截图](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

在前面的示例中，可查看服务的当前成本。 还显示了按 Azure 区域（位置）和按资源组筛选的成本。 也可以添加其他筛选器（例如：用于查看特定存储帐户的成本的筛选器）。

## <a name="export-cost-data"></a>导出成本数据

还可以将[成本数据导出](../../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)到存储帐户。 当你或其他人需要进行有关成本的额外数据分析时，这非常有用。 例如，财务团队可以使用 Excel 或 Power BI 来分析数据。 可以按每天、每周或每月计划导出成本，并设置自定义的日期范围。 建议导出成本数据来检索成本数据集。

## <a name="faq"></a>常见问题解答

如果我每个月只使用几天 Azure 存储，是否按比例计费？

存储容量按一个月内每天的平均数据存储量 (GB) 计费。 例如，如果前半个月稳定地使用了 10 GB 的存储，而后半个月未使用任何存储，则按 5 GB 平均存储使用量计费。

## <a name="next-steps"></a>后续步骤

- 详细了解 Azure 存储的定价方式。 请参阅 [Azure 存储概述定价](https://azure.microsoft.com/pricing/details/storage/)。
- [借助预留容量优化 Blob 存储的成本](../blobs/storage-blob-reserved-capacity.md)。
- 了解[如何通过 Azure 成本管理优化云投资](../../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 详细了解如何通过[成本分析](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)来管理成本。
- 了解如何[防止意外成本](../../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 参与[成本管理](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)引导式学习课程。
