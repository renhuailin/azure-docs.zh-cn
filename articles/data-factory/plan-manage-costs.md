---
title: 计划和管理 Azure 数据工厂的成本
description: 了解如何使用 Azure 门户中的成本分析来计划和管理 Azure 数据工厂的成本。
author: shirleywangmsft
ms.author: shwang
ms.service: data-factory
ms.subservice: pricing
ms.topic: how-to
ms.custom: subject-cost-optimization
ms.date: 04/28/2021
ms.openlocfilehash: aba57e9ba46faef60f97819fa3de3e89fa94f00e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122638129"
---
# <a name="plan-to-manage-costs-for-azure-data-factory"></a>计划和管理 Azure 数据工厂的成本

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文介绍如何计划和管理 Azure 数据工厂的成本。 

首先，在 ETL 项目开始时，使用 Azure 定价和每个管道的消耗与定价计算器的组合，帮助计划 Azure 数据工厂成本，然后再添加所有服务资源来估算成本。 接下来，在添加 Azure 资源时，查看预估成本。 开始使用 Azure 数据工厂资源后，使用“成本管理”功能设置预算和监视成本。 还可以查看预测的成本并确定支出趋势，以确定可能需要实施措施的领域。Azure 数据工厂的成本只是 Azure 账单中每月成本的一部分。 尽管本文介绍了如何计划和管理数据工厂的成本，但你需要为 Azure 订阅中使用的所有 Azure 服务和资源（包括第三方服务）付费。

## <a name="prerequisites"></a>必备条件

成本管理中的成本分析支持大多数 Azure 帐户类型，但不支持所有帐户类型。 若要查看支持的帐户类型的完整列表，请参阅[了解成本管理数据](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。 若要查看成本数据，你至少需要对 Azure 帐户具有读取访问权限。 若要了解如何分配对 Azure 成本管理数据的访问权限，请参阅[分配对数据的访问权限](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="estimate-costs-before-using-azure-data-factory"></a>使用 Azure 数据工厂之前估算成本
 
可以使用 [ADF 定价计算器](https://azure.microsoft.com/pricing/calculator/?service=data-factory)来估算在 Azure 数据工厂中运行 ETL 工作负载的成本。  若要使用计算器，必须输入详细信息，例如活动运行数、数据集成单位小时数、用于数据流的计算类型、核心数、实例数、执行持续时间等。

关于定价计算器的常见问题之一是应将哪些值用作输入。  在概念证明阶段，可以使用示例数据集执行试验运行，以了解各项 ADF 计量的消耗情况。  然后，根据示例数据集的消耗，可以得出完整数据集和操作化计划的消耗。

> [!NOTE]
> 以下示例中使用的是假定价格，并不是指实际定价。

例如，假设每日需要将 1 TB 的数据从 AWS S3 移动到 Azure Data Lake Gen2。  可以对移动 100 GB 数据进行 POC，以度量数据引入吞吐量并了解相应的计费消耗。

下面是复制活动运行详细信息的示例（实际数据会因特定数据集的形态、网络速度、S3 帐户的流出量限制、ADLS Gen2 上的流入量限制和其他因素而异）。

:::image type="content" source="media/plan-manage-costs/s3-copy-run-details.png" alt-text="S3 复制运行":::

通过利用[管道运行级别的消耗监视](#monitor-consumption-at-pipeline-run-level)，可以查看相应的数据移动计量消耗量：

:::image type="content" source="media/plan-manage-costs/s3-copy-pipeline-consumption.png" alt-text="S3 复制管道消耗":::

因此，在一个月中每天移动 1 TB 所需的 DIU 小时总数为：

1.2667（DIU 小时）*（1 TB/100 GB）* 30（一个月中的天数）= 380 DIU 小时

现在可以将 30 个活动运行和 380 DIU 小时插入 ADF 定价计算器中以估算每月的费用：

:::image type="content" source="media/plan-manage-costs/s3-copy-pricing-calculator.png" alt-text="S3 复制定价计算器":::

## <a name="understand-the-full-billing-model-for-azure-data-factory"></a>了解 Azure 数据工厂的完整计费模式

Azure 数据工厂在 Azure 基础结构上运行，部署新资源时，该基础结构会随之产生成本。 用户务必了解，是否可能产生其他基础结构成本。

### <a name="how-youre-charged-for-azure-data-factory"></a>Azure 数据工厂的收费方式

Azure 数据工厂是为实现云规模缩放而构建的无服务器和弹性数据集成服务。  也就是不需要为固定大小的计算规划峰值负载；可以指定每个操作按需分配的资源量，从而以可缩放性更强的方式设计 ETL 进程。 此外，ADF 基于消耗计费，即仅为所用的部分付费。

创建或使用 Azure 数据工厂资源时，可能需要按以下计量标准付费：

- 业务流程活动运行量 - 按活动运行编排量付费。
- 数据集成单元 (DIU) 小时数 – 对于在 Azure Integration Runtime 上运行的复制活动，按使用的 DIU 数和任务执行持续时间付费。
- vCore 小时数 – 对于数据流执行和调试，按计算类型、vCore 数量和任务执行持续时间付费。


在计费周期结束时，将对每个计量标准的费用求和。 帐单或发票显示的费用是所有 Azure 数据工厂成本的一部分。 每个计量标准都有单独的一行项目。

### <a name="other-costs-that-might-accrue-with-azure-data-factory"></a>Azure 数据工厂可能产生的其他成本

为 Azure 数据工厂创建资源时，还会创建其他 Azure 服务的资源。 它们包括：

- 管道活动执行
- 外部管道活动执行
- 创建/编辑/检索/监视数据工厂项目
- 基于实例类型和持续时间的 SSIS Integration Runtime 持续时间

### <a name="using-azure-prepayment-with-azure-data-factory"></a>对 Azure 数据工厂使用 Azure 预付款

可以使用 Azure 预付款额度支付 Azure 数据工厂费用。 但是，不能使用 Azure 预付款额度来支付第三方产品和服务（包括 Azure 市场中的）的费用。

## <a name="monitor-costs"></a>监视成本

可以在工厂、管道运行和活动运行级别监视 Azure 数据工厂成本。

### <a name="monitor-costs-at-factory-level"></a>在工厂级别监视成本

将 Azure 资源用于数据工厂时，会产生成本。 Azure 资源使用情况的单位成本因时间间隔（秒、分钟、小时和天）或单位使用情况（字节、MB 等）而异。一旦开始使用数据工厂，就会产生费用，你可以在[成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)中看到这些费用。

使用成本分析时，可以在关系图和表中查看不同时间间隔的数据工厂成本。 有些示例是按天、当前、上个月和年划分的。 还可以对比预算和预测的成本来查看成本。 随着时间的推移切换到较长的视图，可帮助你确定支出趋势。 你会看到可能出现超支的地方。 如果已创建预算，还可以轻松查看超支的地方。

在成本分析中查看数据工厂成本：

1. 登录到 Azure 门户。
2. 在 Azure 门户中打开范围，并在菜单中选择“成本分析”。 例如，转到“订阅”，从列表中选择一项订阅，然后在菜单中选择“成本分析” 。 选择“范围”，在成本分析中切换到不同的范围。
3. 默认情况下，服务的成本显示在第一个圆环图中。 选择图表中标记“Azure 数据工厂 v2”的区域。

最初打开成本分析时，会显示实际的每月成本。 下面是显示所有每月使用成本的示例。

:::image type="content" source="media/all-costs.png" alt-text="显示订阅累计成本的示例":::

- 若要缩小范围以查看单项服务（如数据工厂）的成本，请选择“添加筛选器”，然后选择“服务名称”。 然后，选择“Azure 数据工厂 v2”。

下面的示例显示了数据工厂的成本。

:::image type="content" source="media/service-specific-cost.png" alt-text="显示 ServiceName 累积费用的示例":::

在前面的示例中，你将看到该服务的当前成本。 其中还显示了按 Azure 区域（位置）提供的成本和按资源组提供的数据工厂成本。 从这里，你可以自行探索成本。

### <a name="monitor-consumption-at-pipeline-run-level"></a>监视管道运行级别的消耗情况

根据管道中的活动类型、移动和转换的数据量以及转换的复杂性，执行管道时将在 Azure 数据工厂中使用不同的计费计量。

在 Azure Data Factory 用户体验中，你可以查看各个管道运行的不同计量的消耗量。 若要打开监视体验，请选择 [Azure 门户](https://portal.azure.com/)的“数据工厂”边栏选项卡中的“监视和管理”磁贴。 如果已进入 ADF 用户体验，请单击左侧边栏上的“监视”图标。 默认监视视图是一个管道运行的列表。

单击管道名称旁边的“消耗”按钮将显示一个弹出窗口，其中显示了基于管道中所有活动的聚合量的管道运行的消耗。

:::image type="content" source="media/plan-manage-costs/pipeline-run-consumption.png" alt-text="管道运行消耗":::

:::image type="content" source="media/plan-manage-costs/pipeline-consumption-details.png" alt-text="管道消耗详细信息":::

“管道运行消耗”视图显示特定管道运行的每项 ADF 计量对应的使用量，但它不显示实际费用，因为收取的金额取决于你拥有的 Azure 帐户类型以及使用的货币类型。  若要查看支持的帐户类型的完整列表，请参阅[了解成本管理数据](../cost-management-billing/costs/understand-cost-mgt-data.md)。

### <a name="monitor-consumption-at-activity-run-level"></a>监视管道运行级别的消耗情况
了解管道运行级别的聚合消耗后，有时需要进一步深入了解并确定管道中成本最高的活动。

若要查看活动运行级别的消耗情况，请转到数据工厂中的“创作和监视”UI。 在“监视”选项卡中，可以查看管道运行列表，单击管道名称链接可以查看管道运行中的活动运行列表 。  单击活动名称旁边的“输出”按钮，然后在 JSON 输出中查找 billableDuration 属性 ：

下面是复制活动运行中的输出示例：

:::image type="content" source="media/plan-manage-costs/copy-output.png" alt-text="复制输出":::

下面是一个“映射数据流”活动运行中的输出示例：

:::image type="content" source="media/plan-manage-costs/dataflow-output.png" alt-text="数据流输出":::

## <a name="create-budgets"></a>创建预算

可以创建[预算](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)来管理成本，并创建[警报](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)以自动通知利益干系人支出异常和超支风险。 警报基于与预算和成本阈值相比的支出。 预算和警报是针对 Azure 订阅和资源组创建的，作为总体成本监视策略的一部分，它们非常有用。 

如果希望增加监视中的精度，可以在 Azure 中使用筛选器为特定资源或服务创建预算。 筛选器可帮助确保不会意外创建会产生额外成本的新资源。 有关创建预算时可用的筛选选项的详细信息，请参阅[分组和筛选选项](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="export-cost-data"></a>导出成本数据

还可以将[成本数据导出](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)到存储帐户。 当你或其他人需要进行有关成本的额外数据分析时，这非常有用。 例如，财务团队可以使用 Excel 或 Power BI 来分析数据。 可以按每天、每周或每月计划导出成本，并设置自定义的日期范围。 建议导出成本数据来检索成本数据集。

## <a name="next-steps"></a>后续步骤

- 了解[如何通过 Azure 成本管理优化云投资](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 详细了解如何通过[成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)来管理成本。
- 了解如何[防止意外成本](../cost-management-billing/understand/analyze-unexpected-charges.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 参与[成本管理](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)引导式学习课程。
- [Azure 数据工厂定价页](https://azure.microsoft.com/pricing/details/data-factory/ssis/)
- [通过示例了解 Azure 数据工厂](./pricing-concepts.md)
- [Azure 数据工厂定价计算器](https://azure.microsoft.com/pricing/calculator/?service=data-factory)