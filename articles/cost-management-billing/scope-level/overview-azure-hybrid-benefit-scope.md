---
title: 什么是 Azure 混合权益的范围级别管理？
description: Azure 混合权益是一项许可权益，借助该权益，你可以将基于本地核心、附带有效软件保障（或订阅）的 Windows Server 和 SQL Server 许可证引入 Azure。
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/30/2021
ms.topic: overview
ms.service: cost-management-billing
ms.subservice: ahb
ms.reviewer: chrisrin
ms.openlocfilehash: 795c8c3509a25abc8244150f129eb9dfbf997557
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129547438"
---
# <a name="what-is-scope-level-management-of-azure-hybrid-benefit"></a>什么是 Azure 混合权益的范围级别管理？

Azure 混合权益是一种许可权益，可帮助你显著减少在云中运行工作负载的成本。 它的工作原理是让你在 Azure 上使用本地启用了软件保障的 Windows Server 和 SQL Server 许可证。 有关详细信息，请参阅 [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)。

可以在整个 Azure 订阅或整个计费帐户的范围内管理 SQL Server 的 Azure 混合权益。 概括而言，管理方式如下：

1. 首先，启用通过 IaaS 扩展自动注册自行安装的 SQL Server 映像的功能，以确认所有 SQL Server VM 对你和 Azure 可见。 有关详细信息，请参阅[使用 SQL IaaS 代理扩展在 Azure 中注册多个 SQL VM](../../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-vms-bulk.md)。
1. 你（计费管理员）在 Azure 门户中的“成本管理 + 计费”下，选择范围以及为了涵盖该范围内的资源而要分配的符合条件的许可证数。  
    :::image type="content" source="./media/overview-azure-hybrid-benefit-scope/set-scope-assign-licenses.png" alt-text="显示如何设置范围并分配许可证的屏幕截图。" lightbox="./media/overview-azure-hybrid-benefit-scope/set-scope-assign-licenses.png" :::

- 该范围内的资源每运行一小时，Azure 就会自动向其分配许可证，并正确地按折扣价计费。 在每小时可以涵盖不同的资源。
- 超过分配的许可证数量后使用任何资源将按正常的即用即付价格计费。
- 如果你选择通过在某个范围级别分配许可证来管理权益，则不再可以管理该范围内的单个资源。

最初的在资源级别启用 Azure 混合权益的方式仍适用于 SQL Server，并且目前是适用于 Windows Server 的唯一选项。 它涉及到 DevOps 角色在创建或管理每个资源（例如 SQL 数据库或 Windows Server VM）时为该资源选择权益。 这样做将会导致按照折扣价计收该资源的每小时费用。 有关详细信息，请参阅 [Windows Server 的 Azure 混合权益](../../azure-sql/azure-hybrid-benefit.md)。

在订阅或帐户范围级别为 SQL Server 启用 Azure 混合权益的功能目前以预览版提供。 该功能仅适用于企业客户。 我们正在努力将该功能扩展到 Windows Server 和其他客户。

## <a name="qualifying-sql-server-licenses"></a>符合条件的 SQL Server 许可证

附带有效软件保障的 SQL Server Enterprise 和 Standard 核心许可证符合此权益的条件。 此外，SQL Server 核心许可证订阅也符合条件。

## <a name="qualifying-azure-resources"></a>符合条件的 Azure 资源

范围级别 Azure 混合权益管理涵盖以下常见 Azure SQL 资源：

- SQL 数据库
- SQL 托管实例
- SQL 弹性池
- Azure VM 上的 SQL Server

资源级别 Azure 混合权益管理也可以涵盖上述所有资源。 目前它是涵盖以下资源的唯一选项：

- Azure 专用主机
- Azure 数据工厂 SQL Server Integration Services (SSIS)

## <a name="scope-level-management-advantages"></a>范围级别管理的优势

你将获得以下优势：

- 方法更简单，控制度更高 - 计费管理员直接将可用许可证分配到一个或多个 Azure 范围。 最初的大规模方法涉及到在许多资源和 DevOps 所有者之间协调 Azure 混合权益的使用。
- 优化成本的易用方法 - 管理员可以监视 Azure 混合权益的使用情况，并直接调整分配到 Azure 的许可证。 例如，管理员可能会注意到一个通过向 Azure 分配更多许可证来节省更多成本的机会。 然后，他们可以与采购部门沟通，以确认许可证的可用性。 最后，他们可以轻松地将许可证分配到 Azure 并开始节省成本。
- 在使用量高峰期管理成本的更好方法 - 在暂时性的高峰期，可以轻松纵向扩展同一资源或添加更多资源。 无需分配更多 SQL Server 许可证（例如，在结算期或节假日购物高峰期）。 对于短时间的工作负载高峰，按即用即付价格支付额外容量的费用，可能比购买更多许可证来为该容量使用 Azure 混合权益的成本更低。 在某个范围而不是资源级别管理权益有助于根据聚合使用量做出决策。
- 明确的职责分离可以保持合规 - 在资源级别 Azure 混合权益模型中，资源所有者无需知道许可证的可用性（通过咨询采购部门）或了解 SQL 许可证总体使用量（包括本地）就可以在 Azure 混合权益的选择方面做出决策。 Azure 混合权益的范围级别管理消除了资源所有者在没有许可证可用时选择 Azure 混合权益的风险。 集中管理 Azure 混合权益的计费管理员可与采购部门和软件资产管理部门确认有多少个许可证可分配到 Azure。 下图演示了这一点。

:::image type="content" source="./media/overview-azure-hybrid-benefit-scope/duty-separation.svg" alt-text="显示职责分离的示意图。" border="false" lightbox="./media/overview-azure-hybrid-benefit-scope/duty-separation.svg":::

## <a name="how-licenses-apply-to-azure-resources"></a>许可证如何应用于 Azure 资源

附带软件保障的 SQL Server Enterprise（核心）和 SQL Server Standard（核心）许可证均符合条件，但根据 [Microsoft 产品条款](https://www.microsoft.com/licensing/terms/productoffering/MicrosoftAzureServices/EAEAS)中所述，通过 Azure 混合权益将它们引入 Azure 时，将会应用不同的转换率。

需要了解的一条规则：对于所有符合条件的 Azure SQL 资源类型，一个 SQL Server Enterprise Edition 许可证的涵盖范围相当于四个 SQL Server Standard Edition 许可证。

为了进一步解释这种转换，我们使用了术语“规范化核心许可证”(NCL)。 与上述规则相符，一个 SQL Server Standard 核心许可证生成一个 NCL。 一个 SQL Server Enterprise 核心许可证生成四个 NCL。 例如，如果分配四个 SQL Server Enterprise 核心许可证和七个 SQL Server Standard 核心许可证，则总涵盖范围和 Azure 混合权益折扣力度等于 23 个 NCL (4\*4+7\*1)。

下表汇总了不同资源类型为了享受 SQL Server 许可证完全折扣价而需要的 NCL 数量。 Azure 混合权益的范围级别管理严格应用产品条款中的规则，下面汇总了这些规则。

| Azure 数据服务 | **服务层** | 所需的 NCL 数量 |
| --- | --- | --- |
| SQL 托管实例或实例池 | 业务关键 | 每个 vCore 4 个 |
| SQL 托管实例或实例池 | 常规用途 | 每个 vCore 1 个 |
| SQL 数据库或弹性池<sup>1</sup> | 业务关键 | 每个 vCore 4 个 |
| SQL 数据库或弹性池<sup>1</sup> | 常规用途 | 每个 vCore 1 个 |
| SQL 数据库或弹性池<sup>1</sup> | 超大规模 | 每个 vCore 1 个 |
| Azure 数据工厂 SQL Server Integration Services | 企业 | 每个 vCore 4 个 |
| Azure 数据工厂 SQL Server Integration Services | Standard | 每个 vCore 1 个 |
| SQL Server 虚拟机<sup>2</sup> | 企业 | 每个 vCPU 4 个 |
| SQL Server 虚拟机<sup>2</sup> | Standard | 每个 vCPU 1 个 |

<sup>1</sup> Azure 混合权益在 Azure SQL 数据库的无服务器计算层中不可用。

<sup>2</sup> 每个虚拟机至少需要四个 vCore 许可证。

## <a name="ongoing-scope-level-management"></a>持续范围级别管理

下面是我们建议在范围级别管理 Azure 混合权益时重复执行的工作，可能适合你：

- 在组织中进行沟通，了解下个月、下一季度或下一年要使用的 Azure SQL 资源和 vCore 数量。
- 与采购和软件资产管理部门合作，确定是否有足够的附带软件保障的 SQL 核心许可证可用。 权益允许在长达 180 天的时间内使用许可证来支持迁移要在本地和 Azure 中使用的工作负载。 因此，可将这些许可证算作可用许可证。
- 分配可用许可证，以满足当前使用量需求以及为将来一段时间规划的使用量需求。
- 监视分配的许可证使用量。
  - 如果使用率接近 100%，请咨询组织中的其他人以了解预期的使用量增长情况。 如果需要，请确认许可证可用性，然后向相应范围分配更多许可证。
  - 如果使用率达到 100%，则可能表示使用的资源超过了分配的许可证数量。 返回[创建许可证分配体验](create-sql-license-assignments.md)并查看 Azure 显示的使用量。 另请查看许可证使用量详细信息。 接下来，与同事沟通以确认或获取许可证，然后向相应范围分配这些许可证以扩大涵盖范围。
- 定期重复主动流程。

## <a name="next-steps"></a>后续步骤

- 按照[管理和优化 SQL Server 的 Azure 混合权益](tutorial-azure-hybrid-benefits-sql.md)教程进行操作。
- 了解如何[过渡到 Azure 混合权益的范围级别管理](transition-existing.md)。
- 查看[范围级别 Azure 混合权益管理常见问题解答](faq-azure-hybrid-benefit-scope.yml)。