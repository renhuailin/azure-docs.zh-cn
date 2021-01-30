---
title: 计划管理 Azure 逻辑应用的成本
description: 了解如何使用 Azure 门户中的成本分析来规划和管理 Azure 逻辑应用的成本
ms.service: logic-apps
ms.reviewer: estfan, logicappspm, azla
ms.topic: how-to
ms.custom: subject-cost-optimization
ms.date: 01/29/2021
ms.openlocfilehash: 58e12862cf00b500bced105d67fede8599c2a257
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2021
ms.locfileid: "99180432"
---
# <a name="plan-and-manage-costs-for-azure-logic-apps"></a>规划和管理 Azure 逻辑应用的成本

本文可帮助你规划和管理 Azure 逻辑应用的成本。 使用此服务创建或添加任何资源之前，请使用 Azure 定价计算器估算成本。 开始使用逻辑应用资源后，可以使用 [Azure 成本管理](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)来设置预算和监视成本。 若要确定可能要执行的操作，还可以查看预测的成本并监视支出趋势。

请记住，逻辑应用的成本只是 Azure 帐单中每月成本的一部分。 尽管本文介绍了如何评估和管理逻辑应用的成本，但你需要为 Azure 订阅中使用的所有 Azure 服务和资源（包括任何第三方服务）付费。 熟悉管理逻辑应用的成本后，可以应用类似的方法来管理订阅中使用的所有 Azure 服务的成本。

## <a name="prerequisites"></a>先决条件

<!--Note for Azure service writer: This section covers prerequisites for the Cost Management's Cost Analysis feature. Add other prerequisites needed for your service after the Cost Management prerequisites. -->

[Azure 成本管理](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 支持大多数 Azure 帐户类型。 若要查看所有支持的帐户类型，请参阅 [了解成本管理数据](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。 若要查看成本数据，你至少需要对 Azure 帐户具有读取访问权限。

若要了解如何分配对 Azure 成本管理数据的访问权限，请参阅[分配对数据的访问权限](https://docs.microsoft.com/azure/cost-management/assign-access-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

<!--Note for Azure service writer: If you have other prerequisites for your service, add them here -->

<a name="understand-billing-model"></a>

## <a name="understand-the-billing-model"></a>了解计费模型

Azure 逻辑应用在 Azure 基础结构上运行，在部署新资源时，会产生 [成本](https://azure.microsoft.com/pricing/details/logic-apps/) 。 请确保了解 [逻辑应用服务的计费模型以及相关的 Azure 资源](logic-apps-pricing.md)，并在对已部署的资源进行更改时，通过这些依赖关系来管理成本。

<a name="typical-costs"></a>

### <a name="costs-that-typically-accrue-with-azure-logic-apps"></a>通常通过 Azure 逻辑应用进行计费的成本

逻辑应用服务基于创建和使用的资源应用不同的定价模型：

* 在多租户逻辑应用服务中创建和运行的逻辑应用资源使用 [消耗定价模型](../logic-apps/logic-apps-pricing.md#consumption-pricing)。

* 在 [integration service 环境 ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 中创建和运行的逻辑应用资源 (ISE) 使用 [固定定价模型](../logic-apps/logic-apps-pricing.md#fixed-pricing)。

下面是在创建逻辑应用时产生成本的其他资源：

* [集成帐户](../logic-apps/logic-apps-pricing.md#integration-accounts)是一种单独的资源，你可以创建并链接到用于构建 B2B 集成的逻辑应用。 集成帐户使用 [固定定价模型](../logic-apps/logic-apps-pricing.md#integration-accounts) ，其中的比率基于你使用的集成帐户类型或 *层* 。

* [ISE](../logic-apps/logic-apps-pricing.md#fixed-pricing)是一个单独的资源，你可以将其创建为需要直接访问虚拟网络中的资源的逻辑应用的部署位置。 ISEs 使用 [固定定价模型](../logic-apps/logic-apps-pricing.md#fixed-pricing) ，其中的费率基于你创建的 ISE SKU 和其他设置。

* [自定义连接器](../logic-apps/logic-apps-pricing.md#consumption-pricing)是为没有预建连接器的 REST API 创建的单独资源，用于在逻辑应用中使用。 自定义连接器执行使用 [消耗定价模型](../logic-apps/logic-apps-pricing.md#consumption-pricing) ，但在 ISE 中使用它时除外。

* 在多租户逻辑应用服务中， [数据保留和存储消耗](../logic-apps/logic-apps-pricing.md#data-retention) 使用 [固定定价模型](../logic-apps/logic-apps-pricing.md#fixed-pricing)来产生成本。 例如，运行历史记录的输入和输出保留在幕后存储中，这不同于你从逻辑应用中单独创建、管理和访问的存储资源。

  在 ISE 中，数据保留和存储消耗不会产生成本。

<a name="costs-after-resource-deletion"></a>

### <a name="costs-might-accrue-after-resource-deletion"></a>删除资源后可能会产生成本

<!--Note to Azure service writer: You might need to sync with your product team to identify resources that continue to exist after those ones for your service are deleted. If you're certain that no resources can exist after those for your service are deleted, then omit this section. -->

删除逻辑应用后，逻辑应用服务不会创建或运行新的工作流实例。 但是，所有正在进行的和挂起的运行将继续，直到它们完成。 此过程可能需要一段时间，具体取决于这些运行的数量。 有关详细信息，请参阅 [管理逻辑应用](manage-logic-apps-with-azure-portal.md#delete-logic-apps)。

如果在删除逻辑应用后拥有这些资源，这些资源将继续存在，并会产生成本，直到将其删除：

* 独立于连接到这些资源的逻辑应用（例如，Azure 函数应用、事件中心、事件网格等）而创建和管理的 azure 资源

* 集成帐户

* Integration service 环境 (ISEs) 

  如果 [删除 ISE](ise-manage-integration-service-environment.md#delete-ise)，则关联的 Azure 虚拟网络、子网和其他相关资源会继续存在。 删除 ISE 后，你可能需要等待特定的小时数，然后才能尝试删除虚拟网络或子网。

### <a name="using-monetary-credit-with-azure-logic-apps"></a>使用 Azure 逻辑应用的货币信用额度

可以按 EA 货币承诺信用额度支付 Azure 逻辑应用费用。 但是，不能使用 EA 货币承诺信用额度来支付第三方产品和服务（包括来自 Azure Marketplace 的产品和服务）的费用。

<a name="estimate-costs"></a>

## <a name="estimate-costs"></a>估算成本

使用 azure 逻辑应用创建资源之前，请使用 [azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)估算成本。 有关详细信息，请参阅 [Azure 逻辑应用的定价模型](../logic-apps/logic-apps-pricing.md)。

1. 在 [Azure 定价计算器页](https://azure.microsoft.com/pricing/calculator/)的左侧菜单中，选择 "**集成**  >  **Azure 逻辑应用**"。

   ![显示 Azure 定价计算器的屏幕截图，其中已选择 "Azure 逻辑应用"。](./media/plan-manage-costs/add-azure-logic-apps-pricing-calculator.png)

1. 向下滚动页面，直到可以查看 Azure 逻辑应用定价计算器。 在与 Azure 逻辑应用直接相关的 Azure 资源的各个部分，输入你计划使用的资源数，以及你可以使用这些资源的间隔数。

   此屏幕截图显示了使用计算器的成本估算示例：

   ![示例显示 Azure 定价计算器中的预估成本](./media/plan-manage-costs/example-logic-apps-pricing-calculator.png)

1. 若要在创建和使用新的相关资源时更新成本估算，请返回到此计算器，并在此处更新这些资源。

<a name="create-budgets-alerts"></a>

## <a name="create-budgets-and-alerts"></a>创建预算和警报

为了帮助你主动管理 Azure 帐户或订阅的成本，你可以使用[Azure 成本管理和计费](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)服务和功能来创建[预算](https://docs.microsoft.com/azure/cost-management/tutorial-acm-create-budgets?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)和[警报](https://docs.microsoft.com/azure/cost-management/cost-mgt-alerts-monitor-usage-spending?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。  预算和警报是针对 Azure 订阅和资源组创建的，作为总体成本监视策略的一部分，它们非常有用。

根据与预算和成本阈值相比的支出，警报会自动通知利益干系人有关支出异常和超支风险的信息。 如果需要在监视中获得更多粒度，还可以在 Azure 中创建对特定资源或服务使用筛选器的预算。 筛选器可帮助确保不会意外地创建新的资源，从而降低额外的费用。 有关筛选器选项的详细信息，请参阅 [组和筛选选项](https://docs.microsoft.com/azure/cost-management-billing/costs/group-filter?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

<a name="monitor-costs"></a>

## <a name="monitor-costs"></a>监视成本

资源使用情况单位成本因时间间隔（例如秒、分钟、小时、天）或单位使用量（如字节、mb 等）而异。 有些示例是按天、当前和上个月和年。 随着时间的推移切换到较长的视图，可帮助你确定支出趋势。 使用成本分析功能时，可以将成本作为不同时间间隔的关系图和表来查看。 如果你创建了预算和成本预测，则还可以轻松找到预算超出的位置，并且可能会发生超支。

开始在 Azure 中创建或开始使用的资源的成本后，可以通过以下方式查看和监视这些成本：

* 使用 Azure Monitor[监视逻辑应用执行和存储](#monitor-billing-metrics)使用情况

* 使用[Azure 成本管理和计费](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)运行[成本分析](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<a name="monitor-billing-metrics"></a>

### <a name="monitor-logic-app-executions-and-storage-consumption"></a>监视逻辑应用执行和存储消耗

使用 Azure Monitor，可以查看特定逻辑应用的以下度量值：

* 计费操作执行
* 可计费触发器执行次数
* 本机操作执行的计费使用情况
* 标准连接器执行的计费使用情况
* 存储消费的计费使用情况
* 计费的执行总数

<a name="execution-storage-metrics"></a>

#### <a name="view-execution-and-storage-consumption-metrics"></a>查看执行和存储消耗指标

1. 在 Azure 门户中，查找并打开逻辑应用。 在逻辑应用的菜单的 " **监视**" 下，选择 " **指标**"。

1. 在右侧窗格中的 " **图表标题**" 下，在 "指标" 栏中打开 " **指标** " 列表，然后选择所需的度量值。

   > [!NOTE]
   > 存储消耗是指逻辑应用使用并计费的存储单元 (GB) 的数量。 在存储中使用小于 500 MB 的运行可能不会出现在 "监视" 视图中，但仍会计费。

   ![显示具有打开的 "指标" 列表的 "指标" 窗格的屏幕截图。](./media/logic-apps-pricing/select-metric.png)

1. 在窗格的右上角，选择所需的时间段。

1. 若要查看其他存储消耗数据（特别是逻辑应用的运行历史记录中的操作输入和输出大小）， [请执行以下步骤](#view-input-output-sizes)。

<a name="view-input-output-sizes"></a>

#### <a name="view-action-input-and-output-sizes-in-run-history"></a>在运行历史记录中查看操作的输入和输出大小

1. 在 Azure 门户中，查找并打开逻辑应用。

1. 在逻辑应用的菜单中，选择“概述”。

1. 在右侧窗格中的 " **运行历史记录**" 下，选择包含要查看的输入和输出的运行。

1. 在 " **逻辑应用运行**" 下，选择 " **运行详细信息**"。

1. 在 " **逻辑应用运行详细信息** " 窗格中，在 "操作" 表中列出每个操作的状态和持续时间，选择要查看的操作。

1. 在 " **逻辑应用操作** " 窗格中，查找该操作的输入和输出的大小。 在 " **输入链接** 和 **输出链接**" 下，找到这些输入和输出的链接。

   > [!NOTE]
   > 对于循环，仅顶级操作显示其输入和输出的大小。 对于嵌套循环内的操作，输入和输出显示零大小和无链接。

<a name="run-cost-analysis"></a>

### <a name="run-cost-analysis-by-using-azure-cost-management-and-billing"></a>使用 Azure 成本管理和计费运行成本分析

若要基于特定作用域（例如，Azure 订阅）查看逻辑应用服务的成本，可以使用[Azure 成本管理和计费](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)中的[成本分析](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)功能。

1. 在 Azure 门户中，打开所需的范围，例如 Azure 订阅。 从左侧菜单的 " **成本管理**" 下，选择 " **成本分析**"。

   当你首次打开 "成本分析" 窗格时，上面的图表显示当前月订阅中的所有服务的实际和预测的使用成本。

   ![显示 "Azure 门户和成本分析" 窗格的屏幕截图，其中包含订阅中实际和预测成本的示例。](./media/plan-manage-costs/cost-analysis-total-actual-forecasted-costs.png)

   > [!TIP]
   > 若要更改范围，请在 " **成本分析** " 窗格中的 "筛选器" 栏上，选择 " **范围** " 筛选器。 在 " **选择作用域** " 窗格中，切换到所需的范围。

   在下面，环形图按 azure 服务、Azure 区域 (位置) 和资源组显示当前成本。

   ![显示 Azure 门户和成本分析窗格，其中包含服务、区域和资源组的环形图示例的屏幕截图。](./media/plan-manage-costs/cost-analysis-donut-charts.png)

1. 若要将图表筛选为特定区域（如服务或资源），请在 "筛选器" 栏中选择 " **添加筛选器**"。

1. 从左侧列表中，选择 "筛选器类型"，例如 " **服务名称**"。 从右侧列表中，选择筛选器，例如 " **逻辑应用**"。 完成后，选择绿色复选标记。

   ![显示具有筛选器选择 Azure 门户和成本分析窗格的屏幕截图。](./media/plan-manage-costs/cost-analysis-add-service-name-filter.png)

   例如，以下是逻辑应用服务的结果：

   ![显示 Azure 门户和成本分析窗格，并在 "逻辑应用" 上筛选结果的屏幕截图。](./media/plan-manage-costs/cost-analysis-total-actual-costs-service.png)

### <a name="export-cost-data"></a>导出成本数据

需要对成本进行更多数据分析时，可以将 [成本数据导出](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 到存储帐户。 例如，财务团队可以使用 Excel 或 Power BI 来分析此数据。 您可以按每天、每周或每月计划导出您的成本，并设置自定义的日期范围。 建议使用导出成本数据来检索成本数据集。

## <a name="other-ways-to-manage-and-reduce-costs"></a>其他管理和降低成本的方法

<!-- Note to Azure service writer: This section is optional. Other than using the Cost Management methods above, your service probably has other specific ways to minimize costs. For example, you might have best practice advice or specific ways to reduce costs that are specific to your service. If so, try to add that guidance here or at least summarize key points. Try to be as prescriptive as possible. If you have more comprehensive content, add links to your other published articles or sections here.

Add a statement that discusses any recommended settings for your service that might help keep the charges minimal if a service isn't being actively used by the customer. For example: Will turning off a VM help to get no charges for the specific VM resource?

Otherwise, if no other cost-saving recommendations or best practices exist to reduce costs, cut this section.
-->

若要帮助你降低逻辑 ap 和相关资源的成本，请尝试以下选项：

* 如果可能，请使用 [内置触发器和操作，这些触发器和操作](../connectors/apis-list.md#built-in)比 [托管连接器触发器和操作](../connectors/apis-list.md#managed-connectors)运行的成本更低。

  例如，在使用 [HTTP 操作](../connectors/connectors-native-http.md) 或通过调用通过使用 [Azure Functions 服务](../azure-functions/functions-overview.md) 创建的函数和使用 [内置 Azure Functions 操作](../logic-apps/logic-apps-azure-functions.md)来访问其他资源时，可能会降低成本。 但是，使用 Azure Functions 也会产生费用，因此请确保比较选项。

* 指定运行工作流的[确切触发器条件](logic-apps-workflow-actions-triggers.md#trigger-conditions)。

  例如，可以指定仅当目标网站返回内部服务器错误时才触发触发器。 在触发器的 JSON 定义中，使用 `conditions` 属性指定引用触发器状态代码的条件。

* 如果触发器具有轮询版本和 webhook 版本，请尝试 webhook 版本，该版本等待指定的事件在触发之前发生，而不是定期检查事件。

* 通过其他服务调用逻辑应用，以便仅在工作流运行时触发触发器。

  例如，可以从创建并使用 Azure Functions 服务运行的函数中调用逻辑应用。 例如，请参阅 [使用 Azure Functions 和 Azure 服务总线调用或触发逻辑应用](logic-apps-scenario-function-sb-trigger.md)。

* 禁用无需持续运行的[逻辑应用](manage-logic-apps-with-azure-portal.md#disable-or-enable-logic-apps)，或者删除不再需要的[逻辑应用](manage-logic-apps-with-azure-portal.md#delete-logic-apps)。 如果可能，禁用不需要的任何其他资源。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 成本管理优化云投资](https://docs.microsoft.com/azure/cost-management-billing/costs/cost-mgt-best-practices?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [使用成本分析管理成本](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [防止意外成本](https://docs.microsoft.com/azure/cost-management-billing/understand/analyze-unexpected-charges?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* 采用 [成本管理](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) 引导式学习课程


