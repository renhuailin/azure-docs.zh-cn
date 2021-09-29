---
title: 计划和管理 Azure API Management 的成本
description: 了解如何使用 Azure 门户中的成本分析来规划和管理 Azure API Management 的成本。
author: dlepow
ms.author: danlep
ms.custom: subject-cost-optimization
ms.service: api-management
ms.topic: how-to
ms.date: 06/11/2021
ms.openlocfilehash: 8baf9e9e7ff1ae12fc5c53af9aa2a479e1ce8342
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128670303"
---
# <a name="plan-and-manage-costs-for-api-management"></a>规划和管理 API Management 的成本

本文介绍如何规划和管理 Azure API Management 的成本。 首先，使用 Azure 定价计算器来帮助规划 Azure API Management 成本，然后再添加任何服务资源来估算成本。 在开始使用 API Management 资源之后，请使用“成本管理”功能来设置预算和监视成本。 还可以查看预测的成本并识别支出趋势，以确定可能需要实施措施的领域。 

API Management 的成本只是 Azure 账单中每月成本的一部分。 尽管本文介绍了如何为 API Management 规划和管理成本，但你需要为 Azure 订阅中使用的所有 Azure 服务和资源（包括第三方服务）付费。

## <a name="prerequisites"></a>先决条件

成本管理中的成本分析支持大多数 Azure 帐户类型，但不支持所有帐户类型。 若要查看支持的帐户类型的完整列表，请参阅[了解成本管理数据](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。 若要查看成本数据，你至少需要对 Azure 帐户具有读取访问权限。 若要了解如何分配对 Azure 成本管理数据的访问权限，请参阅[分配对数据的访问权限](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="estimate-costs-before-using-api-management"></a>使用 API Management 之前估算成本

添加 API Management 之前，请使用 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)估算成本。 

1. 搜索“API Management”，或选择“集成” > “API Management”。
1. 选择“查看”，为 API Management 服务实例添加默认成本估算。

> [!NOTE]
> 上面示例中所示的成本仅用于演示目的。 有关最新定价信息，请参阅 [API Management 定价](https://azure.microsoft.com/pricing/details/api-management/)。

:::image type="content" source="media/plan-manage-costs/pricing-calculator-developer-tier.png" alt-text="估计开发人员层级的成本":::

* 默认的成本估算基于“开发人员”[服务层级](api-management-features.md)（含 1 个[容量单位](api-management-capacity.md)）中的 API Management 服务实例。 开发人员层级适用于非生产用例和评估。 服务级别协议不支持这一层级。

* 要估算额外容量单位或不同服务层级的成本，请从“单位”和“层级”下拉列表中选择其他选项。 

* 根据功能可用性和服务层级，可能需要支付额外费用才能使用[自承载网关](self-hosted-gateway-overview.md)。

有关其他定价和功能的详细信息，请参阅：

* [API 管理定价](https://azure.microsoft.com/pricing/details/api-management/)
* [Azure API 管理层的基于功能的比较](api-management-features.md)

### <a name="using-monetary-credit-with-api-management"></a>将货币额度用于 API Management

可以使用 Azure 预付款（之前称为货币承诺）支付 API Management 费用。 但是，不能使用 Azure 预付款额度来支付第三方产品和服务的费用（包括 Azure 市场中的第三方产品和服务）。

## <a name="understand-the-full-billing-model"></a>了解完整的计费模式

将 Azure 资源用于 API 管理时，会产生成本或可计费标准。 Azure 资源使用的单位成本根据以下因素的不同有所不同：
* 时间间隔（秒、分钟、小时和天）
* 单位使用量（字节、兆字节等）
* 事务数

### <a name="how-youre-charged-for-api-management"></a>API 管理的收费方式

创建 Azure 资源或将其用于 API 管理时，需根据所用层级付费。 详细了解[选择最适合的层级](./api-management-features.md)。

| 层 | 说明 |
| ----- | ----------- |
| 消耗 | 不会产生固定成本。 收费的依据是 API 调用服务超过某一阈值的次数。 |
| 开发人员、基本、标准和高级 | 根据[单位](./api-management-capacity.md)数和[自承载网关](./self-hosted-gateway-overview.md)数产生每月成本。 对于开发人员层级，自承载网关是免费的。 可随时[升级](./upgrade-and-scale.md)到其他服务层级。 |

将虚拟网络、可用性区域和多区域写入等其他 Azure 资源用于 API 管理时，还可能会产生额外的费用。 在计费周期结束时，将对每个计量标准的费用求和。 帐单或发票显示的费用是所有 API 管理成本的一部分。 每个计量标准都有单独的一行项目。

## <a name="monitor-costs"></a>监视成本

开始使用 API 管理之后便会产生成本。 可以在[成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)中或通过 Azure 定价计算器查看成本。

使用成本分析时，可以在关系图和表中查看不同时间间隔（天、当月、上个月和年）的 API 管理成本。 还可以对比预算和预测的成本来查看成本。 随着时间的推移切换到较长的视图，可帮助你确定支出趋势。 你会看到可能出现超支的地方。 如果已创建预算，还可以轻松查看超支的地方。

> [!NOTE]
> 上面示例中所示的成本仅用于演示目的。 根据资源使用情况和当前定价，你的成本将有所不同。

要在成本分析中查看 API Management 成本，请执行以下操作：

1. 登录 [Azure 门户](https://azure.microsoft.com)。
1. 打开“成本管理 + 计费”窗口，从菜单中选择“成本管理”，然后选择“计费范围”。   例如，从列表中选择一个订阅。
1. 从菜单中选择“成本管理”，然后选择“成本分析”。
1. 默认情况下，将在第一个圆环图中显示所有服务的每月成本。 

    :::image type="content" source="media/plan-manage-costs/api-management-cost-analysis.png" alt-text="订阅的每月成本":::

1. 要缩小范围以查看单项服务（如 API Management）的成本，选择“添加筛选器”，然后选择“服务名称” 。 然后选择“API Management”。

    :::image type="content" source="media/plan-manage-costs/api-management-apim-cost-analysis.png" alt-text="显示 API Management 的累计成本的示例":::

在前面的示例中，你将看到该服务的当前成本。 还显示了按 Azure 区域（位置）的成本和按资源组的 API Management 成本。 从这里，你可以自行探索成本。

## <a name="create-budgets"></a>创建预算

可以创建[预算](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)来管理成本，并创建[警报](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)以自动通知利益干系人支出异常和超支风险。 警报基于与预算和成本阈值相比的支出。 预算和警报是针对 Azure 订阅和资源组创建的，作为总体成本监视策略的一部分，它们非常有用。 

如果希望增加监视中的精度，可以在 Azure 中使用筛选器为特定资源或服务创建预算。 筛选器可帮助确保不会意外创建会产生额外成本的新资源。 有关创建预算时筛选器选项的详细信息，请参阅[对选项进行分组和筛选](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。

## <a name="export-cost-data"></a>导出成本数据

还可以将[成本数据导出](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)到存储帐户。 当你需要让其他人进行有关成本的额外数据分析时，这非常有用。 例如，财务团队可以使用 Excel 或 Power BI 来分析数据。 可以按每天、每周或每月计划导出成本，并设置自定义的日期范围。 建议导出成本数据来检索成本数据集。

## <a name="other-ways-to-manage-and-reduce-costs-for-api-management"></a>管理和降低 API Management 成本的其他方式

### <a name="scale-using-capacity-units"></a>使用容量单位进行缩放

API Management 支持通过添加或删除[容量单位](api-management-capacity.md)来进行扩展（“消耗”服务层级除外）。 随着 API Management 实例上负载增加，增加容量单位可能比升级到更高的服务层级更合算。 最大单位数量取决于服务层级。

每个容量单位都有特定的请求处理能力，具体取决于服务所在的层。 例如，基本层级一个单位的最大预期吞吐量约为每秒 1,000 个请求。 

在你添加或删除单位后，容量和成本也会按比例增减。 例如，标准层级两个单位的预期吞吐量约为每秒 2,000 个请求。 由于请求或响应的大小、连接模式、发出请求的客户端的数量以及其他因素，实际吞吐量可能会有所不同。

[监视](api-management-howto-use-azure-monitor.md) API Management 实例的容量指标，以帮助确定是否扩展或升级 API Management 实例以适应更多负载。

## <a name="next-steps"></a>后续步骤

- 了解[如何通过 Azure 成本管理优化云投资](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 详细了解如何通过[成本分析](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)来管理成本。
- 了解如何[防止意外成本](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
- 参与[成本管理](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)引导式学习课程。
- 了解 API Management [容量](api-management-capacity.md)。
- 请参阅使用 [Azure 门户](upgrade-and-scale.md)缩放和升级 API Management 的步骤，并了解[自动扩展](api-management-howto-autoscale.md)。
