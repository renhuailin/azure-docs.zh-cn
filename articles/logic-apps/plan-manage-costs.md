---
title: 计划管理 Azure 逻辑应用的成本
description: 了解如何使用 Azure 门户中的成本分析来计划和管理 Azure 逻辑应用的成本
ms.service: logic-apps
ms.reviewer: estfan, logicappspm, azla
ms.topic: how-to
ms.custom: subject-cost-optimization
ms.date: 05/25/2021
ms.openlocfilehash: b6e23d008a0171e8b71c032349943b809b4bfc92
ms.sourcegitcommit: aaaa6ee55f5843ed69944f5c3869368e54793b48
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113667088"
---
# <a name="plan-and-manage-costs-for-azure-logic-apps"></a>计划和管理 Azure 逻辑应用的成本

本文可帮助你计划和管理 Azure 逻辑应用的成本。 使用此服务创建或添加任何资源之前，请使用 Azure 定价计算器估算成本。 开始使用逻辑应用资源后，可以使用 [Azure 成本管理](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)来设置预算和监视成本。 若要确定可能需要实施措施的领域，还可以查看预测的成本并监视支出趋势。

请记住，逻辑应用的成本只是 Azure 账单中每月成本的一部分。 尽管本文介绍的是如何估算和管理逻辑应用的成本，但你需要为 Azure 订阅中使用的所有 Azure 服务和资源（包括任何第三方服务）付费。 在熟悉了如何管理逻辑应用的成本之后，可以应用类似的方法来管理订阅中使用的所有 Azure 服务的成本。

## <a name="prerequisites"></a>必备条件

<!--Note for Azure service writer: This section covers prerequisites for the Cost Management's Cost Analysis feature. Add other prerequisites needed for your service after the Cost Management prerequisites. -->

[Azure 成本管理](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)支持大多数 Azure 帐户类型。 若要查看所有受支持帐户类型，请参阅[了解成本管理数据](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。 若要查看成本数据，你至少需要对 Azure 帐户具有读取访问权限。

若要了解如何分配对 Azure 成本管理数据的访问权限，请参阅[分配对数据的访问权限](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

<!--Note for Azure service writer: If you have other prerequisites for your service, add them here -->

<a name="understand-billing-model"></a>

## <a name="understand-the-billing-model"></a>了解计费模型

Azure 逻辑应用在 Azure 基础结构上运行，部署新资源时，该基础结构会随之[产生成本](https://azure.microsoft.com/pricing/details/logic-apps/)。 请确保了解[逻辑应用服务的计费模型以及相关 Azure 资源](logic-apps-pricing.md)，并在对已部署的资源进行更改时，管理由于这些依赖项而产生的成本。

<a name="typical-costs"></a>

### <a name="costs-that-typically-accrue-with-azure-logic-apps"></a>通常会随 Azure 逻辑应用产生的成本

Azure 逻辑应用服务基于创建和使用的资源应用不同的定价模型：

* 在多租户 Azure 逻辑应用中创建和运行的逻辑应用资源使用[消耗量（即用即付）定价模型](../logic-apps/logic-apps-pricing.md#consumption-pricing)。

* 在单租户 Azure 逻辑应用中创建和运行的逻辑应用资源使用[托管计划定价模型](../logic-apps/logic-apps-pricing.md#standard-pricing)。

* 在[集成服务环境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 中创建和运行的逻辑应用资源使用 [ISE 定价模型](../logic-apps/logic-apps-pricing.md#ise-pricing)。

下面是在创建以用于逻辑应用时产生成本的其他资源：

* [集成帐户](../logic-apps/logic-apps-pricing.md#integration-accounts)是一种单独的资源，可创建并链接到逻辑应用以便构建 B2B 集成。 集成帐户使用[固定定价模型](../logic-apps/logic-apps-pricing.md#integration-accounts)，其中的费率基于使用的集成帐户类型或层级。

* [ISE](../logic-apps/logic-apps-pricing.md#ise-pricing) 是一种单独的资源，可以创建它作为需要直接访问虚拟网络中资源的逻辑应用的部署位置。 ISE 使用 [ISE 定价模型](../logic-apps/logic-apps-pricing.md#ise-pricing)，其中的费率基于创建的 ISE SKU 和其他设置。 但是，数据保留和存储消耗不会产生成本。

* [自定义连接器](../logic-apps/logic-apps-pricing.md#consumption-pricing)是一种为没有预生成连接器的 REST API 创建的单独资源，可在逻辑应用中使用。 自定义连接器执行使用[消耗量定价模型](../logic-apps/logic-apps-pricing.md#consumption-pricing)，但在 ISE 中使用它们时除外。

<a name="storage-operations-costs"></a>

#### <a name="storage-operations-and-costs"></a>存储操作和成本

Azure 逻辑应用使用 [Azure 存储](../storage/index.yml)进行任何存储操作。 使用多租户 Azure 逻辑应用时，任何存储使用情况和成本都将附加到该逻辑应用。 [数据保留和存储消耗](../logic-apps/logic-apps-pricing.md#storage-operations)使用[固定定价模型](../logic-apps/logic-apps-pricing.md#storage-operations)来产生成本。 例如，来自运行历史记录的输入和输出保留在后台存储中，这与从逻辑应用独立创建、管理和访问的存储资源不同。

使用单租户 Azure 逻辑应用时，可以使用自己的 Azure [存储帐户](../azure-functions/storage-considerations.md#storage-account-requirements)。 借助此功能，可以更灵活、更好地控制逻辑应用数据。 有状态工作流运行其操作时，Azure 逻辑应用运行时会执行存储事务。 例如，使用队列来进行计划，而使用表和 Blob 来存储工作流状态。 存储成本根据工作流的内容而变化。 不同的触发器、操作和有效负载会导致不同的存储操作和需求。 存储事务遵循 [Azure 存储定价模型](https://azure.microsoft.com/pricing/details/storage/)。 存储成本在 Azure 计费发票中单独列出。

对于单租户 Azure 逻辑应用，你可以使用[逻辑应用存储计算器](https://logicapps.azure.com/calculator)，了解工作流可能运行的存储操作的数量及其成本。 可以选择一个示例工作流或使用现有的工作流定义。 第一个计算估计工作流中的存储操作数。 然后，可以通过 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)使用这些数字来估算可能的成本。 有关详细信息，请参阅[估算单租户 Azure 逻辑应用中工作流的存储需求和成本](estimate-storage-costs.md)。

<a name="costs-after-resource-deletion"></a>

### <a name="costs-might-accrue-after-resource-deletion"></a>删除资源后可能会产生成本

<!--Note to Azure service writer: You might need to sync with your product team to identify resources that continue to exist after those ones for your service are deleted. If you're certain that no resources can exist after those for your service are deleted, then omit this section. -->

删除逻辑应用后，逻辑应用服务不会创建或运行新的工作流实例。 但是，所有正在进行的以及挂起的运行将会继续，直到完成。 此过程可能需要一段时间，具体取决于这些运行的数量。 有关详细信息，请参阅[管理逻辑应用](manage-logic-apps-with-azure-portal.md#delete-logic-apps)。

如果在删除逻辑应用后拥有这些资源，则这些资源会继续存在并产生成本，直到删除它们：

* 独立于连接到这些资源的逻辑应用而创建和管理的 Azure 资源，例如，Azure 函数应用、事件中心、事件网格等

* 集成帐户

* 集成服务环境 (ISE)

  如果[删除 ISE](ise-manage-integration-service-environment.md#delete-ise)，则关联 Azure 虚拟网络、子网和其他相关资源会继续存在。 删除 ISE 后，可能需要等待特定的小时数，然后才能尝试删除虚拟网络或子网。

### <a name="using-monetary-credit-with-azure-logic-apps"></a>将货币额度用于 Azure 逻辑应用

可以使用 EA 货币承诺额度支付 Azure 逻辑应用费用。 但是，不能使用 EA 货币承诺额度支付第三方产品和服务的费用（包括 Azure 市场中的第三方产品和服务）。

<a name="estimate-costs"></a>

## <a name="estimate-costs"></a>估算成本

使用 Azure 逻辑应用创建资源之前，请使用 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)估算成本。 有关详细信息，请参阅 [Azure 逻辑应用的定价模型](../logic-apps/logic-apps-pricing.md)。

1. 在 [Azure 定价计算器页](https://azure.microsoft.com/pricing/calculator/)的左侧菜单中，选择“集成”  > “Azure 逻辑应用”。

   ![显示 Azure 定价计算器的屏幕截图，其中选择了“Azure 逻辑应用”。](./media/plan-manage-costs/add-azure-logic-apps-pricing-calculator.png)

1. 向下滚动页面，直到可以查看 Azure 逻辑应用定价计算器。 在与 Azure 逻辑应用直接相关的 Azure 资源的各个部分中，输入计划使用的资源数，以及可以使用这些资源的间隔数。

   此屏幕截图显示使用计算器的成本估算示例：

   ![显示 Azure 定价计算器中的预估成本的示例](./media/plan-manage-costs/example-logic-apps-pricing-calculator.png)

1. 若要在创建和使用新的相关资源时更新成本估算，请返回到此计算器，并在此处更新这些资源。

<a name="create-budgets-alerts"></a>

## <a name="create-budgets-and-alerts"></a>创建预算和警报

为了帮助你主动管理 Azure 帐户或订阅的成本，可以使用 [Azure 成本管理和计费](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)服务和功能来创建[预算](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)和[警报](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。  预算和警报是针对 Azure 订阅和资源组创建的，作为总体成本监视策略的一部分，它们非常有用。

根据与预算和成本阈值进行比较的支出，警报会自动向利益干系人通知有关支出异常和超支风险的信息。 如果希望可以更加精细地进行监视，还可以在 Azure 中创建对特定资源或服务使用筛选器的预算。 筛选器可帮助确保不会意外创建会产生额外成本的新资源。 有关筛选选项的详细信息，请参阅[分组和筛选选项](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

<a name="monitor-costs"></a>

## <a name="monitor-costs"></a>监视成本

资源使用单位成本因时间间隔（如秒、分钟、小时和天）或单位使用量（如字节、MB 等）而异。 有些示例是按天、当前、上个月和年划分的。 随着时间的推移切换到较长的视图，可帮助你确定支出趋势。 使用成本分析功能时，可以按各种时间间隔以图和表的形式查看成本。 如果创建了预算和成本预测，则还可以轻松查找超出预算以及可能发生超支的位置。

开始针对在 Azure 中创建或开始使用的资源产生成本后，可以通过以下方式查看和监视这些成本：

* 使用 Azure Monitor [监视逻辑应用执行和存储使用情况](#monitor-billing-metrics)

* 使用 [Azure 成本管理和计费](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)运行[成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<a name="monitor-billing-metrics"></a>

### <a name="monitor-logic-app-executions-and-storage-usage"></a>监视逻辑应用执行和存储使用情况

使用 Azure Monitor 可以查看特定逻辑应用的以下指标：

* 计费的操作执行数
* 计费的触发器执行数
* 本机操作执行的计费使用情况
* 标准连接器执行的计费使用情况
* 存储消耗的计费使用情况
* 计费的执行总数

<a name="execution-storage-metrics"></a>

#### <a name="view-execution-and-storage-consumption-metrics"></a>查看执行和存储消耗指标

1. 在 Azure 门户中，查找并打开逻辑应用。 在逻辑应用的菜单中的“监视”下，选择“指标” 。

1. 在右侧窗格中的“图表标题”下，在指标栏中打开“指标”列表，然后选择所需指标 。

   > [!NOTE]
   > 存储消耗按照逻辑应用使用和进行计费的存储单位 (GB) 数进行度量。 使用的存储小于 500 MB 的运行可能不会出现在监视视图中，但仍会进行计费。

   ![显示“指标”窗格的屏幕截图，其中打开了“指标”列表。](./media/logic-apps-pricing/select-metric.png)

1. 在窗格的右上角，选择所需时间段。

1. 若要查看其他存储消耗数据（特别是逻辑应用运行历史记录中的操作输入和输出大小），请[执行以下步骤](#view-input-output-sizes)。

<a name="view-input-output-sizes"></a>

#### <a name="view-action-input-and-output-sizes-in-run-history"></a>在运行历史记录中查看操作输入和输出大小

1. 在 Azure 门户中，查找并打开逻辑应用。

1. 在逻辑应用的菜单中，选择“概述”。

1. 在右侧窗格的“运行历史记录”下，选择具有要查看的输入和输出的运行。

1. 在“逻辑应用运行”下，选择“运行详细信息” 。

1. 在“逻辑应用运行详细信息”窗格的“操作表”中（其中列出了每个操作的状态和持续时间），选择要查看的操作。

1. 在“逻辑应用操作”窗格中，找到该操作的输入和输出的大小。 在“输入链接”和“输出链接”下，找到这些输入和输出的链接 。

   > [!NOTE]
   > 对于循环，只有顶级操作才会显示其输入和输出的大小。 对于嵌套循环内的操作，输入和输出会显示零大小和无链接。

<a name="run-cost-analysis"></a>

### <a name="run-cost-analysis-by-using-azure-cost-management-and-billing"></a>使用 Azure 成本管理和计费运行成本分析

若要基于特定范围（例如，Azure 订阅）查看逻辑应用服务的成本，可以使用 [Azure 成本管理和计费](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)中的[成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)功能。

1. 在 Azure 门户中，打开所需范围，如 Azure 订阅。 在左侧菜单的“成本管理”下，选择“成本分析” 。

   首次打开成本分析窗格时，顶部图显示订阅中所有服务在当前月份的实际和预测使用成本。

   ![显示 Azure 门户和成本分析窗格的屏幕截图，其中包含订阅中实际和预测成本的示例。](./media/plan-manage-costs/cost-analysis-total-actual-forecasted-costs.png)

   > [!TIP]
   > 若要更改范围，请在“成本分析”窗格中的筛选器栏上，选择“范围”筛选器 。 在“选择范围”窗格中，切换到所需范围。

   在下方，圆环图按 Azure 服务、Azure 区域（位置）和资源组显示当前成本。

   ![显示 Azure 门户和成本分析窗格的屏幕截图，其中包含服务、区域和资源组的示例圆环图。](./media/plan-manage-costs/cost-analysis-donut-charts.png)

1. 若要将图表筛选到特定区域（如服务或资源），请在筛选器栏中选择“添加筛选器”。

1. 从左侧列表中，选择筛选器类型，例如“服务名称”。 从右侧列表中，选择筛选器，例如“逻辑应用”。 完成后，选中绿色复选标记。

   ![显示 Azure 门户和成本分析窗格的屏幕截图，其中包含筛选器选择。](./media/plan-manage-costs/cost-analysis-add-service-name-filter.png)

   例如，下面是逻辑应用服务的结果：

   ![显示 Azure 门户和成本分析窗格的屏幕截图，其中对“逻辑应用”筛选了结果。](./media/plan-manage-costs/cost-analysis-total-actual-costs-service.png)

### <a name="export-cost-data"></a>导出成本数据

需要对成本进行更多数据分析时，可以将[成本数据导出](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)到存储帐户。 例如，财务团队可以使用 Excel 或 Power BI 来分析数据。 可以按每天、每周或每月计划导出成本，并设置自定义的日期范围。 建议导出成本数据来检索成本数据集。

## <a name="other-ways-to-manage-and-reduce-costs"></a>管理和降低成本的其他方式

<!-- Note to Azure service writer: This section is optional. Other than using the Cost Management methods above, your service probably has other specific ways to minimize costs. For example, you might have best practice advice or specific ways to reduce costs that are specific to your service. If so, try to add that guidance here or at least summarize key points. Try to be as prescriptive as possible. If you have more comprehensive content, add links to your other published articles or sections here.

Add a statement that discusses any recommended settings for your service that might help keep the charges minimal if a service isn't being actively used by the customer. For example: Will turning off a VM help to get no charges for the specific VM resource?

Otherwise, if no other cost-saving recommendations or best practices exist to reduce costs, cut this section.
-->

若要帮助降低逻辑应用和相关资源的成本，请尝试以下选项：

* 如果可能，请使用[内置触发器和操作](../connectors/built-in.md)，它们在每次执行时的运行成本要低于[托管连接器触发器和操作](../connectors/managed.md)。

  例如，使用 [HTTP 操作](../connectors/connectors-native-http.md)或是通过调用使用 [Azure Functions 服务](../azure-functions/functions-overview.md)创建的函数并使用[内置 Azure Functions 操作](../logic-apps/logic-apps-azure-functions.md)来访问其他资源时，可能能够降低成本。 但是，使用 Azure Functions 也会产生成本，因此请确保对选项进行比较。

* [指定精确触发器条件](logic-apps-workflow-actions-triggers.md#trigger-conditions)来运行工作流。

  例如，可以指定触发器仅当目标网站返回内部服务器错误时才会激发。 在触发器的 JSON 定义中，使用 `conditions` 属性可指定引用触发器状态代码的条件。

* 如果触发器具有轮询版本和 Webhook 版本，请尝试 Webhook 版本，该版本会在激发之前等待指定事件发生，而不是定期检查事件。

* 通过其他服务调用逻辑应用，以便触发器仅当工作流应运行时才会激发。

  例如，可以从使用 Azure Functions 服务创建并运行的函数调用逻辑应用。 例如，请参阅[使用 Azure Functions 和 Azure 服务总线调用或触发逻辑应用](logic-apps-scenario-function-sb-trigger.md)。

* [禁用无需持续运行的逻辑应用](manage-logic-apps-with-azure-portal.md#disable-or-enable-logic-apps)，或者[删除完全不再需要的逻辑应用](manage-logic-apps-with-azure-portal.md#delete-logic-apps)。 如果可能，请禁用不需要持续保持活动状态的任何其他资源。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 成本管理优化云投资](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [使用成本分析管理成本](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [防止意外成本](../cost-management-billing/understand/analyze-unexpected-charges.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* 参与[成本管理](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)引导式学习课程