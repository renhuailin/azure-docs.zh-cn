---
title: 设计 Azure Sentinel 工作区体系结构 | Microsoft Docs
description: 使用决策树来了解自己可能希望如何设计 Azure Sentinel 工作区体系结构。
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 07/18/2021
ms.openlocfilehash: 930194c3a415a39edacd9ad8c680a872e1760e03
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178299"
---
# <a name="design-your-azure-sentinel-workspace-architecture"></a>设计 Azure Sentinel 工作区体系结构

本文提供了一个决策树，旨在帮助用户做出有关如何设计 Azure Sentinel 工作区体系结构的重要决策。 有关详细信息，请参阅 [Azure Sentinel 示例工作区设计](sample-workspace-designs.md)和 [Azure Sentinel 工作区体系结构最佳做法](best-practices-workspace-architecture.md)。

## <a name="prerequisites"></a>先决条件

在使用该决策树之前，请确保掌握以下信息：

|先决条件  | 说明 |
|---------|---------|
|**与 Azure 数据驻留有关的法规要求**     |  Azure Sentinel 可在 [Log Analytics 正式发布版支持的](https://azure.microsoft.com/global-infrastructure/services/?products=monitor)大部分区域的工作区中运行，但并不能在支持的所有区域中运行。 新支持的 Log Analytics 区域可能需要一些时间来加入 Azure Sentinel 服务。 <br><br> 由 Azure Sentinel 生成的数据，例如事件、书签和分析规则，可能包含来源于客户 Log Analytics 工作区的一些客户数据。<br><br> 有关详细信息，请参阅[地理可用性和数据驻留](quickstart-onboard.md#geographical-availability-and-data-residency)。|
|**数据源**     |   了解需要连接哪些[数据源](connect-data-sources.md)，包括 Microsoft 和非 Microsoft 解决方案的内置连接器。 也可以使用常用事件格式 (CEF)、Syslog 或 REST-API 将数据源与 Azure Sentinel 相连接。 <br><br>如果 Azure VM 位于多个 Azure 位置，则需要从这些位置收集日志，并且节省数据出口流量成本对用户来说很重要，用户需要使用[带宽定价计算器](https://azure.microsoft.com/pricing/details/bandwidth/#overview)计算每个 Azure 位置的数据出口流量成本。      |
|**用户角色和数据访问级别/权限**     |    Azure Sentinel 使用 [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) 提供可在 Azure 中分配给用户、组和服务的[内置角色](../role-based-access-control/built-in-roles.md)。 <br><br>所有 Azure Sentinel 内置角色都授予对 Azure Sentinel 工作区中数据的读取访问权限。 因此，需要确定是否需要按数据源或行级别控制数据访问，因为这会影响工作区设计决策。 有关详细信息，请参阅[自定义角色和高级 Azure RBAC](roles.md#custom-roles-and-advanced-azure-rbac)。     |
|**每日引入速率**     |  每日引入速率（通常以 GB/天为单位）是 Azure Sentinel 的成本管理和规划注意事项以及工作区设计的关键因素之一。 <br><br>在大多数云和混合环境中，网络设备（如防火墙或代理）以及 Windows 和 Linux 服务器生成的引入数据最多。 为了获得最准确的结果，Microsoft 建议使用详尽的数据源清单。 <br><br>此外，Azure Sentinel [成本计算器](https://cloudpartners.transform.microsoft.com/download?assetname=assets%2FAzure_Sentinel_Calculator.xlsx&download=1)还包括有助于估计数据源的空间占用情况的表格。 <br><br>重要说明：这些估计只是一个起点，日志详细程度设置和工作负载将会产生差异。 建议定期监视系统，以跟踪任何更改。 建议根据具体情况进行定期监视。 <br><br>有关详细信息，请参阅[通过 Azure Monitor 日志管理使用情况和成本](../azure-monitor/logs/manage-cost-storage.md)。       |
|     |         |

## <a name="decision-tree"></a>决策树

下图显示了一个完整的决策树流程图，有助于了解如何出色地设计工作区。

[![Azure Sentinel 工作区设计决策树。](media/best-practices/workspace-decision-tree.png)](media/best-practices/workspace-decision-tree.png#lightbox)

以下部分提供了该决策树的全文版本，包括图像中引用的以下备注：

[备注 1](#note1) | [备注 2](#note2)  | [备注 3](#note3)  | [备注 4](#note4)  | [备注 5](#note5)  | [备注 6](#note6)  | [备注 7](#note7)  | [备注 8](#note8)  | [备注 9](#note9) | [备注 10](#note10)



### <a name="step-1-new-or-existing-workspace"></a>步骤 1：新工作区还是现有工作区？

是否有可用于 Azure Sentinel 的现有工作区？

- 如果没有，无论如何都要创建一个新的工作区，则直接继续执行[步骤 2](#step-2-keeping-data-in-different-azure-geographies)。

- 如果有可以使用的现有工作区，请考虑要引入的数据量。

    - 如果引入量超过 100 GB/天，建议使用单独的工作区，以提高成本效益。

    - 如果引入量低于 100 GB/天，请继续执行[步骤 2](#step-2-keeping-data-in-different-azure-geographies)，以进一步评估。 当此问题在[步骤 5](#step-5-collecting-any-non-soc-data) 中出现时，请再次进行考虑。

### <a name="step-2-keeping-data-in-different-azure-geographies"></a>步骤 2：是否将数据保存在不同的 Azure 地理区域中？

- 如果根据法规要求，需要将数据保存在不同的 Azure 地理区域中，则为每个有合规性要求的 Azure 区域使用单独的 Azure Sentinel 工作区。 有关详细信息，请参阅[区域注意事项](best-practices-workspace-architecture.md#region-considerations)。

- 如果不需要将数据保存在不同的 Azure 区域，请继续执行[步骤 3](#step-3-do-you-have-multiple-azure-tenants)。

### <a name="step-3-do-you-have-multiple-azure-tenants"></a>步骤 3：是否有多个 Azure 租户？

- 如果只有单个租户，请直接继续执行[步骤 4](#step-4-splitting-billing--charge-back)。

- 如果有多个 Azure 租户，考虑是否要收集特定于租户边界的日志，如 Office 365 或 Microsoft 365 Defender。

    - 如果没有任何特定于租户的日志，请直接继续执行[步骤 4](#step-4-splitting-billing--charge-back)。

    - 如果要收集特定于租户的日志，请为每个 Azure AD 租户使用单独的 Azure Sentinel 工作区。 继续执行[步骤 4](#step-4-splitting-billing--charge-back)，了解其他注意事项。

        <a name="note1"></a>[决策树备注 1](#decision-tree)：特定于租户边界的日志（例如来自 Office 365 和 Microsoft Defender 的日志）只能存储在同一租户的工作区中。

        虽然可以使用自定义收集器从另一个租户的工作区收集特定于租户的日志，但由于存在以下缺点，不建议这样做：

        - 由自定义连接器收集的数据将引入自定义表中。 因此，用户将无法使用所有的内置规则和工作簿。
        - UEBA 和机器学习规则等部分内置功能不会考虑自定义表。
        - 自定义连接器需要额外的成本和工作量，例如使用 Azure Functions 和逻辑应用。

        如果这些缺点对你的组织不是问题，请继续执行[步骤 4](#step-4-splitting-billing--charge-back)，无需使用单独的 Azure Sentinel 工作区。

### <a name="step-4-splitting-billing--charge-back"></a>步骤 4：是否拆分计费/退款？

如果需要拆分计费或退款，请考虑使用情况报告或手动交叉记帐是否适用。

- 如果不需要拆分计费或退款，请继续执行[步骤 5](#step-5-collecting-any-non-soc-data)。

- 如果需要拆分计费或退款，，请考虑[使用情况报告或手动交叉记帐](azure-sentinel-billing.md)是否适用。

    - 如果使用情况报告或手动交叉记帐适用，请继续执行[步骤 5](#step-5-collecting-any-non-soc-data)。

    - 如果使用情况报告或手动交叉记帐均不适用，请为每个成本所有者使用单独的 Azure Sentinel 工作区。

    <a name="note2"></a>[决策树备注 2](#decision-tree)：有关详细信息，请参阅 [Azure Sentinel 成本和计费](azure-sentinel-billing.md)。

### <a name="step-5-collecting-any-non-soc-data"></a>步骤 5：是否收集任何非 SOC 数据？

- 如果不收集任何非 SOC 数据（如操作数据），可以直接跳到[步骤 6](#step-6-multiple-regions)。

- 如果要收集非 SOC 数据，请考虑是否存在任何重叠，因为 SOC 和非 SOC 数据需要使用相同的数据源。

    如果 SOC 数据和非 SOC 数据之间存在重叠，则只将重叠数据作为 SOC 数据。 然后，考虑 SOC 数据和非 SOC 数据的引入量是否都低于 100 GB/天，但合并后超过 100 GB/天：

    - “是”：继续执行[步骤 6](#step-6-multiple-regions)，以进一步评估。
    - “否”：为了提高成本效益，不建议使用相同的工作区。 继续执行[步骤 6](#step-6-multiple-regions)，以进一步评估。

    在任一情况下，如需了解详细信息，请参阅[备注 10](#note10)。

    如果没有重叠的数据，考虑 SOC 数据和非 SOC 数据的引入量是否都低于 100 GB/天，但合并后超过 100 GB/天：

    - “是”：继续执行[步骤 6](#step-6-multiple-regions)，以进一步评估。 有关详细信息，请参阅[备注 3](#combining-your-soc-and-non-soc-data)。
    - “否”：为了提高成本效益，不建议使用相同的工作区。 继续执行[步骤 6](#step-6-multiple-regions)，以进一步评估。

#### <a name="combining-your-soc-and-non-soc-data"></a>合并 SOC 数据和非 SOC 数据

<a name="note3"></a>[决策树备注 3](#decision-tree)：虽然我们通常建议客户为非 SOC 数据保留一个单独的工作区，以便非 SOC 数据不计入 Azure Sentinel 成本，但在某些情况下，合并 SOC 数据和非 SOC 数据比将其分离开的成本更低。

例如，假设一家组织的安全日志引入量为 50 GB/天，操作日志引入量为 50 GB/天，工作区在美国东部区域。

下表比较了包含和不包含单独工作区的工作区选项。

> [!NOTE]
> 下表所列的成本和条款均为虚设，仅用于说明目的。 有关最新成本的详细信息，请参阅 Azure Sentinel 定价计算器。
>

|工作区体系结构  |说明 |
|---------|---------|
|SOC 团队有自己的工作区，启用了 Azure Sentinel。 <br><br>Ops 团队有自己的工作区，未启用 Azure Sentinel。     |  SOC 团队： <br>按照 50 GB/天，Azure Sentinel 的每月成本为 6,500 美元。<br>前 3 个月保留期免费。 <br><br>Ops 团队：<br>- 按照 50 GB/天，Log Analytics 的每月成本约为 3,500 美元。<br>- 前 31 天保留期免费。<br><br>这两者的每月总成本等于 10,000 美元。       |
|SOC 和 Ops 团队共用相同的工作区，启用了 Azure Sentinel。 |通过合并这两种日志，引入量将达到 100 GB/天，符合享受承诺层级（Sentinel 为 50%，LA 为 15%）的资格。       <br><br>按照 100 GB/天，Azure Sentinel 的每月成本为 9,000 美元。      |
|     |         |

在此示例中，通过合并两个工作区，每月可节省 1,000 美元，而且 Ops 团队还将享有 3 个月免费保留期，而不是只有 31 天。

只有在 SOC 数据和非 SOC 数据的引入量均为高于或等于 50 GB/天且低于 100 GB/天时，此示例才适用。

<a name="note10"></a>[决策树备注 10](#decision-tree)：建议为非 SOC 数据使用单独的工作区，以便非 SOC 数据不计入 Azure Sentinel 成本。

然而，为非 SOC 数据使用单独工作区的建议纯粹是从成本角度考虑的，在决定使用单个或多个工作区时，还需要考虑其他一些重要的设计因素。 为了避免双重引入成本，可以考虑只使用表级别的 Azure RBAC 在单个工作区中收集重叠数据。

### <a name="step-6-multiple-regions"></a>步骤 6：是否有多个区域？

- 如果只从单个区域的 Azure VM 收集日志，请直接继续执行[步骤 7](#step-7-segregating-data-or-defining-boundaries-by-ownership)。

- 如果要从多个区域的 Azure VM 收集日志，对数据出口流量成本的关注程度如何？

    <a name="note4"></a>[决策树备注 4](#decision-tree)：数据出口流量是指将数据移出 Azure 数据中心所需的[带宽成本](https://azure.microsoft.com/pricing/details/bandwidth/)。 有关详细信息，请参阅[区域注意事项](best-practices-workspace-architecture.md#region-considerations)。

    - 如果减少维护单独工作区所需的工作量是优先事项，请继续执行[步骤 7](#step-7-segregating-data-or-defining-boundaries-by-ownership)。

    - 如果数据出口流量成本足以使维护单独的工作区变得非常重要，则在需要降低数据出口流量成本的每个区域使用单独的 Azure Sentinel 工作区。

        <a name="note5"></a>[决策树备注 5](#decision-tree)：建议使用尽可能少的工作区。 使用 [Azure 定价计算器](azure-sentinel-billing.md#estimate-azure-sentinel-costs)来估算成本，确定实际需要的区域，并为出口流量成本低的区域合并工作区。 与单独的 Azure Sentinel 和 Log Analytics 引入成本相比，带宽成本可能只占 Azure 账单的一小部分。

        例如，成本可能会按如下所示进行估算：

        - 1,000 个 VM，每个 VM 每天生成 1 GB；
        - 将数据从美国区域发送到欧洲区域；
        - 在代理中使用 2:1 的压缩率

        这一估算成本的计算方法为：`1000 VMs * (1GB/day ÷ 2) * 30 days/month * $0.05/GB = $750/month bandwidth cost`

        与单独的 Azure Sentinel 和 Log Analytics 工作区的每月成本相比，此示例成本要低得多。

        > [!NOTE]
        > 所列成本为虚设，仅用于说明目的。 有关最新成本的详细信息，请参阅 Azure Sentinel 定价计算器。
        >

### <a name="step-7-segregating-data-or-defining-boundaries-by-ownership"></a>步骤 7：是否分离数据或按所有权定义边界？

- 如果不需要分离数据或定义任何所有权边界，请直接继续执行[步骤 8](#step-8-controlling-data-access-by-data-source--table)。

- 如果需要分离数据或根据所有权定义边界，则每个数据所有者是否需要使用 Azure Sentinel 门户？

    - 如果每个数据所有者都必须拥有 Azure Sentinel 门户的访问权限，请为每个所有者使用单独的 Azure Sentinel 工作区。

        <a name="note6"></a>[决策树备注 6](#decision-tree)：访问 Azure Sentinel 门户要求每个用户至少拥有 [Azure Sentinel 读取者](../role-based-access-control/built-in-roles.md)的角色，并对工作区中的所有表拥有读取者权限。 如果用户无权访问工作区中的所有表，则需要使用 Log Analytics 访问搜索查询中的日志。

    - 如果通过 Log Analytics 来访问日志对于无权访问 Azure Sentinel 门户的任何所有者来说已足够，请继续执行[步骤 8](#step-8-controlling-data-access-by-data-source--table)。

    有关详细信息，请参阅 [Azure Sentinel 中的权限](roles.md)。

### <a name="step-8-controlling-data-access-by-data-source--table"></a>步骤 8：是否按数据源/表控制数据访问？

- 如果不需要按数据源或表控制数据访问，可使用单个 Azure Sentinel 工作区。

- 如果需要按数据源或表控制数据访问，可以考虑在以下情况下使用[资源上下文 RBAC](resource-context-rbac.md)：

    - 如果需要在行级别控制访问，例如，在每个数据源或表上提供多个所有者

    - 如果有多个自定义数据源/表，且其中每个都需要单独的权限

    在其他情况下，如果不需要在行级别控制访问，可以提供多个具有单独权限的自定义数据源/表，使用单个 Azure Sentinel 工作区，并使用表级别 RBAC 进行数据访问控制。

#### <a name="considerations-for-resource-context-or-table-level-rbac"></a>有关资源上下文 RBAC 或表级别 RBAC 的注意事项

计划使用资源上下文 RBAC 或表级别 RBAC 时，请注意以下几点：

- <a name="note7"></a>[决策树备注 7](#decision-tree)：要为非 Azure 资源配置资源上下文 RBAC，用户可能希望在向 Azure Sentinel 发送数据时将资源 ID 与数据关联，以便使用资源上下文 RBAC 对权限进行限定。 有关详细信息，请参阅[显式配置资源上下文 RBAC](resource-context-rbac.md#explicitly-configure-resource-context-rbac) 和[按部署划分的访问模式](../azure-monitor/logs/design-logs-deployment.md)。

- <a name="note8"></a>[决策树备注 8](#decision-tree)：[资源权限](../azure-monitor/logs/manage-access.md)或[资源上下文](../azure-monitor/logs/design-logs-deployment.md)允许用户只查看其有权访问的资源的日志。 工作区访问模式必须设置为“用户资源或工作区权限”。 在 Azure Sentinel 中的“日志”页面的搜索结果中，只包括与用户拥有权限的资源相关的表。

- <a name="note9"></a>[决策树备注 9](#decision-tree)：[表级别 RBAC](../azure-monitor/logs/manage-access.md) 允许在其他权限之外，对 Log Analytics 工作区中的数据进行更精细的控制。 使用此控制措施可以定义仅供特定一组用户访问的特定数据类型。 有关详细信息，请参阅 [Azure Sentinel 中的表级别 RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043)。


## <a name="next-steps"></a>后续步骤

有关此决策树的实际应用示例，请参阅 [Azure Sentinel 示例工作区设计](sample-workspace-designs.md)。

有关详细信息，请参阅：

- [Azure Sentinel 工作区体系结构最佳做法](best-practices-workspace-architecture.md)
- [Azure Sentinel 的最佳做法](best-practices.md)