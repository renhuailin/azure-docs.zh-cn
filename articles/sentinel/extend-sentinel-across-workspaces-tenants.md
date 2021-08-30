---
title: 跨工作区和租户扩展 Azure Sentinel | Microsoft Docs
description: 如何使用 Azure Sentinel 查询与分析跨工作区和租户的数据。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2020
ms.author: yelevin
ms.openlocfilehash: 917bcf74adaaec4e354662ec25816bcad471025d
ms.sourcegitcommit: 0beea0b1d8475672456da0b3a4485d133283c5ea
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2021
ms.locfileid: "112991903"
---
# <a name="extend-azure-sentinel-across-workspaces-and-tenants"></a>跨工作区和租户扩展 Azure Sentinel

## <a name="the-need-to-use-multiple-azure-sentinel-workspaces"></a>使用多个 Azure Sentinel 工作区的需求

Azure Sentinel 是以 Log Analytics 工作区为基础而构建的。 你将注意到，加入 Azure Sentinel 的第一步是选择要用于该目的的 Log Analytics 工作区。

使用单个工作区时，你可以充分利用 Azure Sentinel 体验。 尽管如此，某些情况仍可能要求你使用多个工作区。 下表列出了其中的一些情况，并建议如何在可行的情况下使用单个工作区满足这种要求：

| 要求 | 说明 | 减少工作区计数的方法 |
|-------------|-------------|--------------------------------|
| 数据主权和合规性 | 工作区绑定到特定区域。 如果数据需保存在不同的 [Azure 地理区域](https://azure.microsoft.com/global-infrastructure/geographies/)以满足法规要求，则其必须拆分到单独的工作区中。 |  |
| 数据所有权 | 数据所有权（例如由子公司或附属公司拥有）的边界最好使用单独的工作区来划分。 |  |
| 多个 Azure 租户 | Azure Sentinel 仅在其自己的 Azure Active Directory (Azure AD) 租户边界内支持从 Microsoft 和 Azure SaaS 资源收集数据。 因此，每个 Azure AD 租户都需要一个单独的工作区。 |  |
| 精细数据访问控制 | 组织可能需要允许组织内外的不同组访问 Azure Sentinel 收集的某些数据。 例如：<br><ul><li>资源所有者访问与其资源相关的数据</li><li>区域或附属 SOC 访问其组织各个部分的相关数据</li></ul> | 使用[资源 Azure RBAC](resource-context-rbac.md) 或[表级 Azure RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043) |
| 精细保留设置 | 过去，使用多个工作区是为不同数据类型设置不同保留期的唯一方式。 现在，得益于表级保留设置的引入，在很多情况下不再需要这样做。 | 使用[表级保留设置](https://techcommunity.microsoft.com/t5/azure-sentinel/new-per-data-type-retention-is-now-available-for-azure-sentinel/ba-p/917316)或自动化[数据删除](../azure-monitor/logs/personal-data-mgmt.md#how-to-export-and-delete-private-data) |
| 拆分计费 | 通过将工作区放在单独的订阅中，不同的参与方都可以对其计费。 | 使用情况报告和交叉收费 |
| 旧体系结构 | 使用多个工作区可能源于历史设计，而该设计考虑到的限制或最佳做法已不再适用。 它也可能是一个任意的设计方案，经过修改后可以更好地适应 Azure Sentinel。<br><br>示例包括：<br><ul><li>部署 Azure 安全中心时使用按订阅的默认工作区</li><li>精细访问控制或保留设置的需求，这是相对较新的解决方案</li></ul> | 重新构建工作区 |

### <a name="managed-security-service-provider-mssp"></a>安全托管服务提供商 (MSSP)

MSSP Azure Sentinel 服务就是一个强制要求使用多个工作区的特殊用例。 在这种情况下，上述很多甚至全部要求都适用，使得创建跨租户的多个工作区成了最佳做法。 MSSP 可以使用 [Azure Lighthouse](../lighthouse/overview.md) 在租户之间扩展 Azure Sentinel 跨工作区功能。

## <a name="azure-sentinel-multiple-workspace-architecture"></a>Azure Sentinel 多个工作区体系结构

如上述要求规定的那样，在某些情况下，多个 Azure Sentinel 工作区（可能跨 Azure Active Directory (Azure AD) 租户）需要由单个 SOC 集中监视和管理。

- MSSP Azure Sentinel 服务。

- 为多家附属公司提供服务的全球 SOC，每家附属公司具有自身的本地 SOC。

- 监视组织中多个 Azure AD 租户的 SOC。

为了满足此要求，Azure Sentinel 提供了可以实现集中监视、配置和管理的多工作区功能，并针对 SOC 涵盖的任何内容提供单一视图，如下图所示。

:::image type="content" source="media/extend-sentinel-across-workspaces-tenants/cross-workspace-architecture.png" alt-text="跨工作区体系结构":::

此模型提供的优势比完全集中式模型（将所有数据复制到单个工作区）更为明显：

- 灵活地将角色分配到全球和本地的 SOC，或 MSSP 的客户。

- 在数据所有权、数据隐私和法律合规性方面面临的挑战更少。

- 网络延迟最短且收费最便宜。

- 可轻松加入和排除新的子公司或客户。

以下部分将介绍如何操作此模型，具体而言，如何实现以下目的：

- 集中监视多个可能跨租户的工作区，并为 SOC 提供单一视图。

- 使用自动化集中配置和管理可能跨租户的多个工作区。

## <a name="cross-workspace-monitoring"></a>跨工作区监视

### <a name="manage-incidents-on-multiple-workspaces"></a>管理多个工作区中的事件

Azure Sentinel 支持[多工作区事件视图](./multiple-workspace-view.md)，该视图有助于跨多个工作区进行集中式事件监视和管理。 在集中式事件视图中，可以直接管理事件，或者以透明方式向下钻取到来源工作区上下文中的事件详细信息。

### <a name="cross-workspace-querying"></a>跨工作区查询

Azure Sentinel 支持[在单个查询中查询多个工作区](../azure-monitor/logs/cross-workspace-query.md)，这样就可以通过单个查询搜索和关联多个工作区中的数据。 

- 使用 [workspace() 表达式](../azure-monitor/logs/workspace-expression.md)引用其他工作区中的表。 
- 将 [union 运算符](/azure/data-explorer/kusto/query/unionoperator?pivots=azuremonitor)与 workspace() 表达式结合使用，以跨多个工作区中的表应用查询。

可以使用保存的[函数](../azure-monitor/logs/functions.md)来简化跨工作区查询。 例如，如果对某个工作区的引用很长，你可以将表达式 `workspace("customer-A's-hard-to-remember-workspace-name").SecurityEvent` 另存为名为 `SecurityEventCustomerA` 的函数。 然后，可将查询编写为 `SecurityEventCustomerA | where ...`。

函数还可以简化常用的联合。 例如，可将以下表达式另存为名为 `unionSecurityEvent` 的函数：

`union workspace(“hard-to-remember-workspace-name-1”).SecurityEvent, workspace(“hard-to-remember-workspace-name-2”).SecurityEvent`

然后，可以从 `unionSecurityEvent | where ...` 开始，编写跨这两个工作区的查询。

#### <a name="cross-workspace-analytics-rules"></a>跨工作区分析规则<a name="scheduled-alerts"></a>
<!-- Bookmark added for backward compatibility with old heading -->
跨工作区查询现在可以包含在计划的分析规则中。 你可以在中央 SOC 中使用跨工作区分析规则，如果是 MSSP 则跨租户（使用 Azure Lighthouse），但存在以下限制：

- 在单个查询中最多可以包含 20 个工作区。
- 必须将 Azure Sentinel 部署在查询中引用的每个工作区上。
- 跨工作区分析规则生成的警报，以及从其创建的事件只存在于定义了该规则的工作区中。 它们不会显示在查询中引用的任何其他工作区中。

跨工作区分析规则创建的警报和事件将包含所有相关实体，其中包括所有引用的工作区以及“主”工作区（在其中定义规则）中的实体。 这将允许分析师全面了解警报和事件。

> [!NOTE] 
> 在同一查询中查询多个工作区可能影响性能，因此仅当逻辑需要此功能时，才建议这样做。

#### <a name="cross-workspace-workbooks"></a>跨工作区的工作簿<a name="using-cross-workspace-workbooks"></a>
<!-- Bookmark added for backward compatibility with old heading -->
[工作簿](./overview.md#workbooks)为 Azure Sentinel 提供仪表板和应用。 使用多个工作区时，工作簿将提供跨工作区的监视和操作。

工作簿可通过以下三种方法之一提供跨工作区查询，每种方法与不同的最终用户专业水平相适应：

| 方法  | 说明 | 何时使用？ |
|---------|-------------|--------------------|
| 编写跨工作区查询 | 工作簿创建者可以在工作簿中编写跨工作区查询（前面已介绍）。 | 此选项可让工作簿创建者在工作区结构中完全屏蔽用户。 |
| 将工作区选择器添加到工作簿 | 工作簿创建者可以实现一个工作区选择器作为工作簿的一部分，如[此处](https://techcommunity.microsoft.com/t5/azure-sentinel/making-your-azure-sentinel-workbooks-multi-tenant-or-multi/ba-p/1402357)所述。 | 用户可以使用此选项通过易用的下拉框来控制工作簿显示的工作区。 |
| 以交互方式编辑工作簿 | 修改现有工作簿的高级用户可在其中编辑查询（使用编辑器中的工作区选择器选择目标工作区）。 | Power User 可以使用此选项轻松将现有工作簿修改为使用多个工作区。 |
|

#### <a name="cross-workspace-hunting"></a>跨工作区搜寻

Azure Sentinel 提供预加载的查询示例，旨在帮助你入门并熟悉表和查询语言。 这些内置的搜寻查询是由 Microsoft 安全研究人员持续努力开发出来的，他们添加了新查询并微调了现有查询，目的是为你提供一个用于查找新检测结果的入口点，并识别出你的安全工具未能检测到的入侵迹象。  

借助跨工作区搜寻功能，威胁搜寻者可以使用前面所示的 union 运算符和 workspace() 表达式创建新的搜寻查询或改编现有查询，以涵盖多个工作区。

## <a name="cross-workspace-management-using-automation"></a>使用自动化进行跨工作区管理

若要配置和管理多个 Azure Sentinel 工作区，需要自动化 Azure Sentinel 管理 API 的使用。 有关如何自动部署 Azure Sentinel 资源（包括警报规则、搜寻查询、工作簿和 playbook）的详细信息，请参阅[扩展 Azure sentinel：API、集成和管理自动化](https://techcommunity.microsoft.com/t5/azure-sentinel/extending-azure-sentinel-apis-integration-and-management/ba-p/1116885)。

另请参阅[部署和管理 Azure Sentinel 即代码](https://techcommunity.microsoft.com/t5/azure-sentinel/deploying-and-managing-azure-sentinel-as-code/ba-p/1131928)以及[如何将 Azure Lighthouse 与 Azure Sentinel 的 DevOps 功能结合使用](https://techcommunity.microsoft.com/t5/azure-sentinel/combining-azure-lighthouse-with-sentinel-s-devops-capabilities/ba-p/1210966)，其中提供了社区贡献的有关如何管理 Azure Sentinel 即代码，以及从专用 GitHub 存储库部署和配置资源的综合方法。 

## <a name="managing-workspaces-across-tenants-using-azure-lighthouse"></a>使用 Azure Lighthouse 跨租户管理工作区

如前所述，在许多情况下，不同的 Azure Sentinel 工作区可能位于不同的 Azure AD 租户中。 可以使用 [Azure Lighthouse](../lighthouse/overview.md) 跨租户边界扩展所有跨工作区活动，使管理租户中的用户能够跨所有租户使用 Azure Sentinel 工作区。 [加入](../lighthouse/how-to/onboard-customer.md) Azure Lighthouse 后，使用 Azure 门户上的[目录 + 订阅选择器](./multiple-tenants-service-providers.md#how-to-access-azure-sentinel-in-managed-tenants)选择包含你要管理的工作区的所有订阅，以确保在门户上的不同工作区选择器中提供这些订阅。

使用 Azure Lighthouse 时，建议为每个 Azure Sentinel 角色创建一个组，并将每个租户中的权限委托给这些组。

## <a name="next-steps"></a>后续步骤
在本文档中，你已了解如何跨多个工作区和租户扩展 Azure Sentinel 的功能。 有关实施 Azure Sentinel 跨工作区体系结构的实用指导，请参阅以下文章：

- 了解如何通过 Azure Lighthouse 在 Azure Sentinel 中[使用多个租户](./multiple-tenants-service-providers.md)。
- 了解如何无缝[查看和管理多个工作区中的事件](./multiple-workspace-view.md)。
