---
title: 教程 - 优化 SQL Server 的范围级别 Azure 混合权益
description: 本教程指导你在 Azure 中主动分配 SQL Server 许可证，以管理和优化 Azure 混合权益。
author: bandersmsft
ms.author: banders
ms.date: 09/30/2021
ms.topic: tutorial
ms.service: cost-management-billing
ms.subservice: ahb
ms.reviewer: chrisrin
ms.openlocfilehash: fa928181b20a5975f4c6e42079695aede223ddac
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129547478"
---
# <a name="tutorial-optimize-scope-level-azure-hybrid-benefit-for-sql-server"></a>教程：优化 SQL Server 的范围级别 Azure 混合权益

本教程指导你在 Azure 中主动分配 SQL Server 许可证，以管理和优化 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)。 优化权益可以降低 Azure SQL 运行成本。

在本教程中，你将了解：

> [!div class="checklist"]
> * 收集许可证使用量和可用性详细信息
> * 根据需要购买更多许可证
> * 将许可证分配到 Azure
> * 监视使用量并进行调整
> * 建立管理计划

## <a name="prerequisites"></a>先决条件

在开始之前，请确保：

已阅读并理解[什么是 Azure 混合权益的范围级别管理？](overview-azure-hybrid-benefit-scope.md)一文。 本文将介绍有资格享受 Azure 混合权益的 SQL Server 许可证类型，以及如何在所选的订阅范围或整个计费帐户级别启用和使用 Azure 混合权益。

> [!NOTE]
> 在某个范围使用 Azure 混合权益的功能目前为公共预览版，仅供企业客户使用。

在开始使用新体验之前，请验证你自行安装的、在 Azure 中运行 SQL Server 的虚拟机是否已注册。 这可以确保运行 SQL Server 的 Azure 资源对你和 Azure 都可见。 有关在 Azure 中注册 SQL VM 的详细信息，请参阅[使用 SQL IaaS 代理扩展注册 SQL Server VM](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md) 和[使用 SQL IaaS 代理扩展在 Azure 中注册多个 SQL VM](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-vms-bulk.md)。

## <a name="gather-license-usage-and-availability-details"></a>收集许可证使用量和可用性详细信息

第一步是做好准备。 让组织中的其他部门沟通，以解答两个问题：

- 当前以及为下一年规划的 Azure SQL 和 SQL Server 使用量是多少？
- 可将多少个 SQL Server 核心许可证分配到 Azure？

接下来，确定当前以及为下一年或其他较长时间范围（至少一个月）规划的 Azure SQL 使用量。

[为 Azure 混合权益创建 SQL Server 许可证分配](create-sql-license-assignments.md)时，将显示你的 Azure SQL 使用量详细信息。

我们建议你咨询组织中的相应人员以验证上述信息。 这些信息包括规划的使用量和预期的 SQL Server 使用量增长情况。 可以由某个核心决策人员或小组专门负责此类规划，也可以将规划工作交由多个团队来处理。

调查 Azure SQL 使用量（包括 Azure 混合权益在资源级别的使用量）的一种可选但有效的方法是使用 Azure 混合权益 [sql-license-usage PowerShell 脚本](https://github.com/anosov1960/sql-server-samples/tree/master/samples/manage/azure-hybrid-benefit)。 该脚本可以分析和跟踪特定订阅或整个帐户中所有 SQL 资源的 SQL Server 许可证总使用量。

### <a name="determine-the-number-of-sql-server-core-licenses-available-to-assign-to-azure"></a>确定可分配到 Azure 的 SQL Server 核心许可证数量

该数量取决于你购买的许可证数量，以及已在本地服务器和 Azure VM 之间使用的许可证数量。

我们为客户提供 180 天的 SQL Server 许可证双重使用权利，以确保迁移无缝运行。 180 天期限过后，你只能在 Azure 中使用 SQL Server 许可证。 规划许可证可用性时请考虑到这一点。 例如，可以将正在迁移的许可证视为可分配的许可证。

软件采购或软件资产管理部门也许可以提供此信息。

## <a name="buy-more-licenses-if-needed"></a>根据需要购买更多许可证

查看收集的信息后，如果你确信可用的 SQL Server 许可证数量不足以满足规划的 Azure SQL 使用量需求，请与采购部门沟通，以购买更多附带软件保障的 SQL Server 核心许可证（或订阅许可证）。

与在 Azure 中按小时支付 SQL Server 费用相比，购买 SQL Server 许可证并应用 Azure 混合权益更划算一些。 购买足以满足所有规划 Azure SQL 使用量需求的许可证后，你的组织可以通过权益最大程度地节省成本。

## <a name="assign-licenses-to-azure"></a>将许可证分配到 Azure

1. 按照 Azure 门户和文档中的说明，选择至少一个范围并向其分配 SQL Server 许可证。 有关详细信息，请参阅[为 Azure 混合权益创建 SQL Server 许可证分配](create-sql-license-assignments.md)。
2. 分配许可证时，请再次查看检测到的 Azure SQL 使用量，以验证详细信息是否与收集的其他信息一致。

## <a name="monitor-usage-and-adjust"></a>监视使用量并进行调整

1. 导航到“成本管理 + 计费” > “预留 + 混合权益” 。
1. 此时将显示一个表格，其中包含你创建的 Azure 混合权益许可证分配，以及每个分配的利用率百分比。
1. 如有任何利用率百分比达到 100%，则意味着你的组织正在为某些 SQL Server 资源按小时付费。 再次与组织中的其他小组沟通，确认当前使用量水平是暂时性的，还是今后会继续。 如果是后者，则组织应考虑购买更多许可证，并将其分配到 Azure 以降低成本。
1. 如果利用率接近但没有超过 100%，请确定使用量在近期是否会上升。 如果是，你可以提前获取并分配更多许可证。

## <a name="establish-a-management-schedule"></a>建立管理计划

上一部分讨论了持续监视。 我们还建议建立可反复遵循的年度或季度计划。 该计划包括本文中所述的主要步骤：

- 收集许可证使用量和可用性详细信息。
- 根据需要购买更多许可证以应对将来的使用量增长。
- 将许可证分配到 Azure。
- 监视使用量并根据需要即时调整。
- 每年或按照最符合需求的任何频率重复该过程。

## <a name="example-walkthrough"></a>示例演练

以下示例假设你是 Contoso 保险公司的计费管理员。 你正在管理 Contoso 中 SQL Server 的 Azure 混合权益。

采购部门通知你可以在整个帐户级别管理 SQL Server 的 Azure 混合权益。 采购部门从其 Microsoft 帐户团队那里了解到了相关情况。 你之所以对此工作感兴趣，是因为近来 Azure 混合权益管理颇具挑战性。 部分原因是，开发人员在相互共享脚本时，总是随意对资源启用（或不启用）权益。

你可以在 Azure 门户的“成本管理 + 计费”区域找到新的 Azure 混合权益体验。

阅读本文中前面的说明后，你已了解到：

  - Contoso 需要在执行其他操作之前注册 SQL Server VM。
  - 新功能的理想使用方式是提前分配许可证以满足预期使用量需求。

然后执行以下步骤。

1. 使用前面的说明确保自行安装的 SQL VM 已注册。 这包括与订阅所有者沟通，以便在你没有足够权限的情况下能够为订阅完成注册。
1. 查看最近几个月的 Azure 资源使用量数据，并与 Contoso 中的其他人员沟通。 你已确定需要 2000 个 SQL Server Enterprise Edition 和 750 个 SQL Server Standard Edition 核心许可证或者 8750 个规范化核心许可证，才能满足下一年的预期 Azure SQL 使用量需求。 预期使用量还包括迁移工作负载（1500 个 SQL Server Enterprise Edition + 750 个 SQL Server Standard Edition = 6750 个规范化核心许可证）和全新的 Azure SQL 工作负载（另外 500 个 SQL Server Enterprise Edition 核心许可证或 2000 个规范化核心许可证）。
1. 接下来，与采购团队确认所需的许可证已购买或即将购买。 进行这种确认可以确保有许可证可分配到 Azure。
   - 如果正在将关联的工作负载迁移到 Azure，则本地使用的许可证可被视为可分配到 Azure。 如前所述，Azure 混合权益提供长达 180 天的双重使用权利。
   - 你已确定有 1800 个 SQL Server Enterprise Edition 许可证和 2000 个 SQL Server Standard Edition 许可证可分配到 Azure。 可用许可证数等于 9200 个规范化核心许可证。 这比所需的 8750 个许可证 (2000 x 4 + 750 = 8750) 略多一些。
1. 然后，将 1800 个 SQL Server Enterprise Edition 许可证和 2000 个 SQL Server Standard Edition 许可证分配到 Azure。 执行该操作后，可将 9200 个规范化核心许可证应用于每小时运行的 Azure SQL 资源。 如果使用量增长速度超过预期，则分配超过现在所需数量的许可证可以提供缓冲。

之后，你可以定期（最好是每月）监视分配的许可证使用量。 10 个月后，使用率将接近 95%，这表示 Azure SQL 使用量增长速度超过了预期。 与采购团队沟通以获取更多可分配的许可证。

最后，采用许可证年度审查计划。 在审查过程中，你将：

- 收集并分析许可证使用量数据。
- 确认许可证可用性。
- 根据需要与采购团队合作，以获取更多许可证。
- 更新许可证分配。
- 不断监视。

## <a name="next-steps"></a>后续步骤

- 了解如何[过渡到 Azure 混合权益的范围级别管理](transition-existing.md)。
- 查看[范围级别 Azure 混合权益管理常见问题解答](faq-azure-hybrid-benefit-scope.yml)。