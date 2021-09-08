---
title: 有关 Azure Sentinel 工作区体系结构的最佳做法
description: 了解设计 Azure Sentinel 工作区的最佳做法。
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 07/28/2021
ms.openlocfilehash: 085b9a187ce574c5138867f7a2ce929218717c37
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123253815"
---
# <a name="azure-sentinel-workspace-architecture-best-practices"></a>Azure Sentinel 工作区体系结构最佳做法

在计划 Azure Sentinel 工作区部署时，还必须设计 Log Analytics 工作区体系结构。 工作区体系结构的相关决策通常基于业务和技术需求。 本文介绍关键决策因素，这些因素可帮助你确定适合组织的工作区体系结构，其中包括：

- 是使用单租户还是多个租户
- 你对数据收集和存储的任何合规性要求
- 如何控制对 Azure Sentinel 数据的访问
- 不同方案的成本影响

有关详细信息，请参阅[设计 Azure Sentinel 工作区体系结构](design-your-workspace-architecture.md)和常见方案的[示例工作区设计](sample-workspace-designs.md)，以及[有关部署 Azure Sentinel 的部署前活动和先决条件](prerequisites.md)。

请参阅我们的视频：[构造 SecOps 以获得成功：部署 Azure Sentinel 的最佳做法](https://youtu.be/DyL9MEMhqmI)


## <a name="tenancy-considerations"></a>租户注意事项

虽然较少的工作区更易于管理，但你可能在特定情况下需要多个租户和工作区。 例如，许多组织都有一个云环境，其中包含多个 [Azure Active Directory (Azure AD) 租户](../active-directory/develop/quickstart-create-new-tenant.md)，这些租户是因合并和收购或应标识分离要求而生成的。

在确定要使用多少个租户和工作区时，应考虑到大多数 Azure Sentinel 功能都使用单个工作区或 Azure Sentinel 实例运行，且 Azure Sentinel 会引入工作区中存储的所有日志。

> [!IMPORTANT]
> 成本是决定 Azure Sentinel 体系结构的一个主要因素。 有关详细信息，请参阅 [Azure Sentinel 成本和账单](azure-sentinel-billing.md)。
>
### <a name="working-with-multiple-tenants"></a>使用多个租户

如果有多个租户（例如，如果你是安全托管服务提供商 (MSSP)），建议为每个 Azure AD 租户至少创建一个工作区，以支持内置的[服务到服务数据连接器](connect-data-sources.md#service-to-service-integration)，且这些连接器仅在各自的 Azure AD 租户内工作。

所有基于诊断设置的连接器都无法连接到不位于资源所在租户的工作区。 这适用于 [Azure 防火墙](connect-azure-firewall.md)、[Azure 存储](connect-azure-storage-account.md)、[Azure 活动](connect-azure-activity.md)或 [Azure Active Directory](connect-azure-active-directory.md) 等连接器。

使用 [Azure Lighthouse](../lighthouse/how-to/onboard-customer.md) 帮助管理不同租户中的多个 Azure Sentinel 实例。

> [!NOTE]
> [合作伙伴数据连接器](data-connectors-reference.md)通常基于 API 或代理集合，因此不会连接到特定的 Azure AD 租户。
>



## <a name="compliance-considerations"></a>合规性考虑事项

完成对数据的收集、存储和处理后，合规性可能会成为一项重要的设计要求，并对 Azure Sentinel 体系结构产生重要影响。 在许多国家和地区，一项关键数据主权要求是能够验证和证明谁有权在所有条件下访问哪些数据，在 Azure Sentinel 工作流中评估风险并获取见解对于许多客户而言都很重要。

在 Azure Sentinel 中，数据大多存储在同一地理位置或区域中并在其中进行处理，但也有一些例外，例如在使用利用 Microsoft 机器学习的检测规则时。 在这种情况下，数据可能会复制到工作区地理位置之外的位置进行处理。

有关详细信息，请参阅：

- [地理可用性和数据驻留](quickstart-onboard.md#geographical-availability-and-data-residency)
- [Azure 中的数据驻留](https://azure.microsoft.com/global-infrastructure/data-residency/)
- [在 EU 存储和处理 EU 数据 - EU 政策博客](https://blogs.microsoft.com/eupolicy/2021/05/06/eu-data-boundary/)

若要开始验证合规性，请评估数据源，以及数据源发送数据的方式和位置。

> [!NOTE]
> [Log Analytics 代理](connect-windows-security-events.md)支持 TLS 1.2，以确保代理与 Log Analytics 服务之间传输的数据的安全性，还支持 FIPS 140 标准。 
>
> 如果要将数据发送到与 Azure Sentinel 工作区不同的地理位置或区域，无论发送的资源是否驻留在 Azure 中，应考虑使用同一地理位置或区域中的工作区。
>

## <a name="region-considerations"></a>区域注意事项

在每个区域使用单独的 Azure Sentinel 实例。 尽管 Azure Sentinel 可在多个区域使用，但可能会需要按团队、区域或站点，或法规和控制措施来分隔数据，这使得多区域模型无法实现或比所需的更复杂。 为每个区域使用单独的实例和工作区，有助于避免跨区域移动数据的带宽/传出成本。

使用多个区域时，请考虑以下事项：

- 当需要使用 [Log Analytics 或 Azure Monitor 代理](connect-windows-security-events.md)收集日志时，例如在虚拟机上，通常会产生传出成本。

- 也可能产生 Internet 出口费用，但只在将数据导出到 Log Analytics 工作区之外时可能才需要支出这项费用。 例如，如果将 Log Analytics 数据导出到本地服务器，可能会产生 Internet 出口费用。

- 带宽成本因源和目标区域以及收集方法而异。 有关详细信息，请参阅：

    - [带宽定价](https://azure.microsoft.com/pricing/details/bandwidth/)
    - [使用 Log Analytics 的数据传输费用](../azure-monitor/logs/manage-cost-storage.md)。

- 为分析规则、自定义查询、工作簿和其他资源使用模板，以提高部署效率。 部署模板，而不用在每个区域手动部署每项资源。

- 基于诊断设置的连接器不会产生带宽内成本。 有关详细信息，请参阅[通过 Azure Monitor 日志管理使用情况和成本](../azure-monitor/logs/manage-cost-storage.md#data-transfer-charges-using-log-analytics)。

例如，如果你决定从美国东部的虚拟机收集日志并将其发送到美国西部的 Azure Sentinel 工作区，则需要支付该数据传输的传出成本。 由于 Log Analytics 代理会压缩传输中的数据，因此需要付费的带宽大小可能低于 Azure Sentinel 中日志的大小。

如果要从世界各地的多个源收集 Syslog 和 CEF 日志，可能需要在 Azure Sentinel 工作区的同一区域设置 Syslog 收集器，以避免带宽成本，但前提是不存在合规性问题。

若要确定为单独的 Azure Sentinel 工作空间支付带宽成本是否合理，这取决于需要在区域之间传输的数据量。 使用 [Azure 定价计算器](https://azure.microsoft.com/pricing/details/bandwidth/)估算成本。

有关详细信息，请参阅 [Azure 中的数据驻留](https://azure.microsoft.com/global-infrastructure/data-residency/)。

## <a name="access-considerations"></a>访问注意事项

你可能计划了不同团队需要访问相同数据的情况。 例如，SOC 团队必须有权访问所有 Azure Sentinel 数据，而运营团队和应用程序团队只需访问特定数据。 独立安全团队可能还需要访问 Azure Sentinel 功能，但需要使用不同的数据集。

结合使用[资源上下文 RBAC](resource-context-rbac.md) 和[表级别 RBAC](../azure-monitor/logs/manage-access.md#table-level-azure-rbac)，为团队提供可支持大多数用例的广泛访问选项。

有关详细信息，请参阅 [Azure Sentinel 中的权限](roles.md)。

### <a name="resource-context-rbac"></a>资源上下文 RBAC

下图显示了一个简化版的工作区体系结构，其中安全团队和运营团队需要访问不同的数据集，并使用资源上下文 RBAC 提供所需的权限。


[ ![资源上下文 RBAC 的示例体系结构示意图。](media/resource-context-rbac/resource-context-rbac-sample.png) ](media/resource-context-rbac/resource-context-rbac-sample.png#lightbox)

在此图中，Azure Sentinel 工作区位于单独的订阅中，以便更好地隔离权限。

> [!NOTE]
> 另一种方法是将 Azure Sentinel 放在专注于安全性的独立管理组下，这可确保仅继承最小权限分配。 在安全团队中，根据组的功能为多个组分配权限。 由于这些团队有权访问整个工作区，因此能够获取完整的 Azure Sentinel 体验，其体验仅受其 Azure Sentinel 角色的限制。 有关详细信息，请参阅 [Azure Sentinel 中的权限](roles.md)。
>

除了安全性订阅，还为各应用程序团队使用了单独的订阅来托管其工作负载。 应用程序团队具有对各自资源组的访问权限，他们可在其中管理自己的资源。 这种独立的订阅和资源上下文 RBAC 允许这些团队查看他们有权访问的任何资源生成的日志，即使这些日志存储在他们无法直接访问的工作区中。 应用程序团队可以通过 Azure 门户的“日志”区域访问自己的日志，以查看特定资源的日志，也可通过 Azure Monitor 同时查看有权访问的所有日志。

Azure 资源具有对资源上下文 RBAC 的内置支持，但在使用非 Azure 资源时，可能需要进行其他微调。 有关详细信息，请参阅[显式配置资源上下文 RBAC](resource-context-rbac.md#explicitly-configure-resource-context-rbac)。

### <a name="table-level-rbac"></a>表级 RBAC

使用表级别 RBAC 可以定义仅供特定一组用户访问的特定数据类型（表）。

例如，考虑如果具有上图所示体系结构的组织还必须向内部审核团队授予对 Office 365 日志的访问权限。 在这种情况下，组织可以使用表级别 RBAC 向审核团队授予对整个 OfficeActivity 表的访问权限，而不授予对任何其他表的访问权限。

### <a name="access-considerations-with-multiple-workspaces"></a>拥有多个工作区时的访问注意事项

如果组织有多个实体、子公司或地理位置，其中每一个都有自己的安全团队需要访问 Azure Sentinel，请为每个实体或子公司使用单独的工作区。 在一个 Azure AD 租户中实现不同的工作区，或使用 Azure Lighthouse 在多个租户中实现不同的工作区。 

中央 SOC 团队还可使用额外可选的 Azure Sentinel 工作区来管理集中式项目，例如分析规则或工作簿。

有关详细信息，请参阅[简化使用多个工作区](#simplify-working-with-multiple-workspaces)。


## <a name="technical-best-practices-for-creating-your-workspace"></a>创建工作区的技术最佳做法

在创建要用于 Azure Sentinel 的 Log Analytics 工作区时，请遵循以下最佳做法指南：

- 命名工作区时，在名称中包含 Azure Sentinel 或其他指示符，方便在其他工作区中轻松识别。

- 将同一工作区用于 Azure Sentinel 和 Azure 安全中心，以便 Azure 安全中心收集的所有日志也可以由 Azure Sentinel 引入和使用。 Azure 安全中心创建的默认工作区不会显示为 Azure Sentinel 的可用工作区。

- 如果预计每天数据引入量大约为或超过 1 TB，请使用专用工作区群集。 使用[专用群集](../azure-monitor/logs/logs-dedicated-clusters.md)可保护 Azure Sentinel 数据的资源，从而为大型数据集提供更好的查询性能。 专用群集还提供对组织密钥进行更多加密和控制的选项。

## <a name="simplify-working-with-multiple-workspaces"></a>简化使用多个工作区

如果确实需要使用多个工作区，请简化事件管理和调查，方法是[紧缩每个 Azure Sentinel 实例中的所有事件并在一个位置列出](multiple-workspace-view.md)。

若要参考其他 Azure Sentinel 工作区（例如[跨工作区的工作簿](extend-sentinel-across-workspaces-tenants.md#cross-workspace-workbooks)）中保存的数据，请使用[跨工作区查询](extend-sentinel-across-workspaces-tenants.md)。

最适合使用跨工作区查询的情况是重要信息存储在其他工作区、订阅或租户中，并能为当前操作提供价值。 例如，以下代码显示了一个跨工作区查询示例：

```Kusto
union Update, workspace("contosoretail-it").Update, workspace("WORKSPACE ID").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

有关详细信息，请参阅[跨工作区和租户扩展 Azure Sentinel](extend-sentinel-across-workspaces-tenants.md)。
## <a name="next-steps"></a>后续步骤


> [!div class="nextstepaction"]
>[设计 Azure Sentinel 工作区体系结构](design-your-workspace-architecture.md)

> [!div class="nextstepaction"]
>[Azure Sentinel 示例工作区设计](sample-workspace-designs.md)

> [!div class="nextstepaction"]
>[加入 Azure Sentinel](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[了解警报](get-visibility.md)