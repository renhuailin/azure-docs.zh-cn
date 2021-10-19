---
title: Azure 成本管理和计费概述
description: 可使用成本管理 + 计费功能执行计费管理任务，并管理对成本的计费访问。 还可以使用该功能监视和控制 Azure 支出，并优化 Azure 资源的使用。
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/07/2021
ms.topic: overview
ms.service: cost-management-billing
ms.subservice: common
ms.custom: contperf-fy21q2
ms.openlocfilehash: 51b33d90a1dda0878f3c0fa39ed35c7d63d905dd
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129706227"
---
# <a name="what-is-cost-management--billing"></a>什么是“成本管理 + 计费”？

通过使用 Microsoft 云，可以显著提高业务工作负载的技术性能。 还可以降低管理组织资产所需的成本和开销。 不过，该业务机遇会带来风险，因为云部署中引入了潜在的浪费和效率低下因素。 成本管理 + 计费是 Microsoft 提供的一套工具，有助于分析、管理和优化工作负载的成本。 使用此套件有助于确保组织充分利用云提供的权益。

可以将 Azure 工作负载视为你家中的灯。 如果你某天要出门，你会不关灯就走吗？ 你能否使用不同的更节能的灯泡来帮助减少每月能源开销？ 某个房间内的灯光是否比所需的灯光更多？ 可使用成本管理 + 计费功能将类似的思维过程应用于组织使用的工作负载。

使用 Azure 产品和服务，只需为你使用的部分付费。 创建和使用 Azure 资源时，需要为资源付费。 由于新资源易于部署，因此如果没有恰当的分析和监视，工作负载的成本可能会显著变化。 可使用成本管理 + 计费功能来：

- 执行计费管理任务，例如支付帐单
- 管理对成本的计费访问权限
- 下载用于生成月度发票的成本和使用情况数据
- 主动将数据分析应用于成本
- 设置支出阈值
- 确定可优化支出的工作负载更改机会

若要详细了解如何作为组织来处理成本管理，请参阅[成本管理最佳做法](./costs/cost-mgt-best-practices.md)一文。

![“成本管理 + 计费”优化过程的示意图。](./media/cost-management-optimization-process.png)

## <a name="understand-azure-billing"></a>了解 Azure 计费

Azure 计费功能用于查看已开票成本并管理对计费信息的访问。 在较大的组织中，采购和财务团队通常执行计费任务。

当你注册使用 Azure 时，系统将为你创建计费帐户。 可以使用计费帐户管理发票、付款并跟踪成本。 可以访问多个计费帐户。 例如，可以为个人项目注册 Azure。 因此，你可能有一个带有计费帐户的单独的 Azure 订阅。 也可通过组织的企业协议或 Microsoft 客户协议进行访问。 对于每个方案，你将有一个单独的计费帐户。

### <a name="billing-accounts"></a>计费帐户

Azure 门户当前支持以下类型的计费帐户：

- **Microsoft Online Services 计划**：当你通过 Azure 网站注册 Azure 时，将创建 Microsoft Online Services 计划的个人计费帐户。 例如，当你注册 [Azure 免费帐户](./manage/create-free-services.md)、采用即用即付费率的帐户，或者以 Visual Studio 订阅者的身份进行注册时。

- **企业协议**：当组织签署使用 Azure 的企业协议 (EA) 时，将为企业协议创建一个计费帐户。

- **Microsoft 客户协议**：当组织与 Microsoft 代表一起签署 Microsoft 客户协议时，将会为 Microsoft 客户协议创建一个计费帐户。 特定区域的某些客户通过 Azure 网站注册采用即用即付费率的帐户或者升级其 [Azure 免费帐户](./manage/create-free-services.md)，这些客户可能也有一个适用于 Microsoft 客户协议的计费帐户。

## <a name="understand-cost-management"></a>了解成本管理

虽然与计费相关，但计费不等同于成本管理。 计费是为客户开具商品或服务的发票及管理商业关系的过程。

成本管理会借助高级分析显示组织的成本和使用模式。 成本管理中的报告显示 Azure 服务和第三方市场产品/服务所消耗的基于使用情况的成本。 成本基于协商价格和预留因素以及 Azure 混合权益折扣。 总的来说，这些报表显示了内部和外部使用成本以及 Azure 市场费用。 报告中尚未显示其他费用，例如预订购买费用、支持费用和税费。 报表可以帮助用户了解支出和资源使用情况，并帮助查找支出异常情况。 另外，还可以进行预测分析。 成本管理使用 Azure 管理组、预算和建议来清楚地显示费用规划情况，以及如何能够降低成本。

你可以使用 Azure 门户或各种 API 执行自动导出，从而将成本数据与外部系统和过程进行整合。 另外，还可自动导出计费数据和计划的报表。

观看成本管理概述视频，快速了解成本管理如何帮助你在 Azure 中节省费用。 若要观看其他视频，请访问[成本管理 YouTube 频道](https://www.youtube.com/c/AzureCostManagement)。

>[!VIDEO https://www.youtube.com/embed/el4yN5cHsJ0]

### <a name="plan-and-control-expenses"></a>计划和控制支出

成本管理帮助你规划和控制成本的方式包括：成本分析、预算、建议和导出成本管理数据。

可以通过成本分析来了解和分析组织成本。 可以按组织查看合计成本，了解哪些方面持续产生成本并确定支出趋势。 此外，还可以查看一段时间的累计成本，根据预算预估每月、每季度，甚至每年的成本趋势。

预算可帮助你规划并满足组织中的财务责任。 它们有助于防止超出成本阈值或限制。 而且，预算还可以帮助你通知他人有关他们的支出，实现主动管理成本。 随着时间的推移，你可以看到支出的进展情况。

建议说明如何通过识别闲置和未充分利用的资源来优化和提高效率。 或者，它们可以显示价格更优惠的资源选项。 根据建议采取措施时，可以更改资源使用方式以节省资金。 为此，首先查看成本优化建议，以查看潜在的低效使用情况。 接下来，根据建议将 Azure 资源使用修改为更具成本效益的选择。 然后，验证操作以确保所做的更改成功。

如果你使用外部系统来访问或查看成本管理数据，则可以轻松地从 Azure 导出数据。 你可以设置一个每日计划导出，将数据文件以 CSV 格式导出并存储在 Azure 存储中。 然后，即可从外部系统访问数据。

### <a name="additional-azure-tools"></a>其他 Azure 工具

Azure 还包含成本管理 + 计费功能集中没有的其他一些工具。 但它们在成本管理过程中都发挥着重要作用。 若要详细了解这些工具，请参阅以下链接。

- [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/) - 使用此工具可估计前期云成本。
- [Azure Migrate](../migrate/migrate-services-overview.md) - 评估当前数据中心的工作负载，获取有关 Azure 替代解决方案所需内容的见解。
- [Azure 顾问](../advisor/advisor-overview.md) - 识别未使用的 VM，并获得有关 Azure 预留实例购买的建议。
- [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/) - 使用当前用于 Azure 中 VM 的本地 Windows Server 或 SQL Server 许可证来实现成本节省。

## <a name="next-steps"></a>后续步骤

至此，你已熟悉成本管理和计费，接下来将了解如何使用该服务。

- 开始使用成本管理来[分析成本](./costs/quick-acm-cost-analysis.md)。
- 此外，还可详细了解[成本管理最佳做法](./costs/cost-mgt-best-practices.md)。