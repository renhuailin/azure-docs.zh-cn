---
title: 示例 Azure Sentinel 工作区设计 | Microsoft Docs
description: 学习采用多个租户、云或区域的 Azure Sentinel 体系结构设计示例。
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 07/18/2021
ms.openlocfilehash: dca6beedfe8fa3d57674323490c2d79cf5aa048f
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179352"
---
# <a name="azure-sentinel-sample-workspace-designs"></a>Azure Sentinel 示例工作区设计

本文介绍了为具有以下示例要求的组织推荐的工作区设计：

- 多个租户和区域，具有欧洲数据主权要求
- 使用多个云的单个租户
- 多个租户，采用多个区域和集中式安全性

本文中的示例使用 [Azure Sentinel 工作区设计决策树](design-your-workspace-architecture.md)来确定最适合每个组织的工作区设计。 有关详细信息，请参阅 [Azure Sentinel 工作区体系结构最佳做法](best-practices-workspace-architecture.md)。

## <a name="sample-1-multiple-tenants-and-regions"></a>示例 1：多个租户和区域

Contoso Corporation 是一家跨国企业，总部位于伦敦。 Contoso 在世界各地设有办事处，其重要业务中心位于纽约市和东京。 最近，Contoso 已将其生产力套件迁移到 Office 365，并将许多工作负载迁移到 Azure。

### <a name="contoso-tenants"></a>Contoso 租户

由于几年前的收购，Contoso 现在拥有两个 Azure AD 租户：`contoso.onmicrosoft.com` 和 `wingtip.onmicrosoft.com`。 每个租户具有自己的 Office 365 实例和多个 Azure 订阅，如下图所示：

:::image type="content" source="media/best-practices/contoso-tenants.png" alt-text="Contoso 租户示意图，每个租户具有独立的订阅集" border="false":::。

### <a name="contoso-compliance-and-regional-deployment"></a>Contoso 合规性和区域部署

Contoso 目前在三个不同的区域（美国东部、欧洲北部和日本西部）托管 Azure 资源，并严格要求将欧洲生成的所有数据保留在欧洲区域。

Contoso 的这两个 Azure AD 租户在所有三个区域（美国东部、欧洲北部和日本西部）都有资源

### <a name="contoso-resource-types-and-collection-requirements"></a>Contoso 资源类型和收集要求

Contoso 需要从以下数据源收集事件：

-   Office 365
-   Azure AD 登录和审核日志
-   Azure 活动
-   来自本地和 Azure VM 源的 Windows 安全事件
-   来自本地和 Azure VM 源的 Syslog
-   来自多个本地网络设备（例如 Palo Alto、Cisco ASA 和 Cisco Meraki）的 CEF
-   多个 Azure PaaS 资源，例如 Azure 防火墙、AKS、密钥保管库、Azure 存储和 Azure SQL
-   Cisco Umbrella

Azure VM 主要位于欧洲北部区域，只有少量的 VM 位于美国东部和日本西部。 Contoso 在其所有 Azure VM 上使用适用于服务器的 Azure Defender。

Contoso 预计每天会从其所有数据源引入大约 300 GB 数据。

### <a name="contoso-access-requirements"></a>Contoso 访问要求

Contoso 的 Azure 环境已有一个 Log Analytics 工作区，供运营团队用来监视基础结构。 此工作区位于欧洲北部区域的 Contoso AAD 租户中，用于从所有区域的 Azure VM 收集日志。 他们目前每天引入大约 50 GB 数据。

Contoso 运营团队需有权访问他们当前在工作区中拥有的所有日志，其中包括 SOC 不需要的几种数据类型，例如 Perf、InsightsMetrics、ContainerLog 等  。 必须禁止运营团队访问 Azure Sentinel 中收集的新日志。

### <a name="contosos-solution"></a>Contoso 的解决方案

以下步骤应用 [Azure Sentinel 工作区设计决策树](design-your-workspace-architecture.md)来确定最适合 Contoso 的工作区设计：

1. Contoso 已有一个工作区，因此我们可以探索如何在该工作区中启用 Azure Sentinel。

    非 SOC 数据引入量小于 100 GB/天，因此我们可以继续执行[步骤 2](design-your-workspace-architecture.md#step-2-keeping-data-in-different-azure-geographies)，并确保在[步骤 5](design-your-workspace-architecture.md#step-5-collecting-any-non-soc-data) 中选择相关选项。

1.  Contoso 必须遵守法规要求，因此在欧洲至少需要一个 Azure Sentinel 工作区。

1.  Contoso 有两个不同的 Azure AD 租户，并从租户级数据源（例如 Office 365 以及 Azure AD 登录和审核日志）收集数据，因此每个租户至少需要一个工作区。

1.  Contoso 无需[分摊成本](design-your-workspace-architecture.md#step-4-splitting-billing--charge-back)，因此我们可以继续执行[步骤 5](design-your-workspace-architecture.md#step-5-collecting-any-non-soc-data)。

1.  Contoso 确实需要收集非 SOC 数据，不过，SOC 数据与非 SOC 数据之间不存在任何重叠。 此外，SOC 数据约为 250 GB/天，因此出于成本效益的考虑，Contoso 应使用独立的工作区。

1.  Contoso 的大多数 VM 位于欧洲北部区域，在该区域他们已有一个工作区。 因此，在这种情况下，带宽成本不是考虑因素。

1.  Contoso 只有一个 SOC 团队使用 Azure Sentinel，因此无需额外的分隔。

1.  Contoso SOC 团队的所有成员都有权访问所有数据，因此无需额外的分隔。

下图演示了适合 Contoso 的最终 Azure Sentinel 工作区设计：

:::image type="content" source="media/best-practices/contoso-solution.png" alt-text="Contoso 解决方案示意图，其中的运营团队使用一个独立的工作区。" border="false":::

建议的解决方案包括：

- Contoso 运营团队使用一个独立的 Log Analytics 工作区。 此工作区仅包含 Contoso SOC 团队不需要的数据，例如 Perf、InsightsMetrics 或 ContainerLog 表  。

- 两个 Azure Sentinel 工作区（每个 Azure AD 租户各有一个），用于从 Office 365、Azure 活动、Azure AD 和所有 Azure PaaS 服务引入数据。

- 来自本地数据源的所有其他数据可以路由到两个 Azure Sentinel 工作区中的一个。


## <a name="sample-2-single-tenant-with-multiple-clouds"></a>示例 2：使用多个云的单个租户

Fabrikam 是一家总部位于纽约市的组织，在美国各地设有办事处。 Fabrikam 已开启其云历程，但仍然需要部署其第一个 Azure 登陆区域并迁移首批工作负载。 Fabrikam 在 AWS 上已有一些工作负载，他们打算使用 Azure Sentinel 来监视这些工作负载。

### <a name="fabrikam-tenancy-requirements"></a>Fabrikam 租户要求

Fabrikam 有一个 Azure AD 租户。

### <a name="fabrikam-compliance-and-regional-deployment"></a>Fabrikam 合规性和区域部署

Fabrikam 没有合规性要求。 Fabrikam 在美国的多个 Azure 区域具有资源，但跨区域的带宽成本不是重要考虑因素。

### <a name="fabrikam-resource-types-and-collection-requirements"></a>Fabrikam 资源类型和收集要求

Fabrikam 需要从以下数据源收集事件：

-   Azure AD 登录和审核日志
-   Azure 活动
-   来自本地和 Azure VM 源的安全事件
-   来自本地和 Azure VM 源的 Windows 事件
-   来自本地和 Azure VM 源的性能数据
-   AWS CloudTrail
-   AKS 审核和性能日志

### <a name="fabrikam-access-requirements"></a>Fabrikam 访问要求

Fabrikam 运营团队需要访问：

-   来自本地和 Azure VM 源的安全事件与 Windows 事件
-   来自本地和 Azure VM 源的性能数据
-   AKS 性能（容器见解）和审核日志
-   所有 Azure 活动数据

Fabrikam SOC 团队需要访问：
-   Azure AD 登录和审核日志
-   所有 Azure 活动数据
-   来自本地和 Azure VM 源的安全事件
-   AWS CloudTrail 日志
-   AKS 审核日志
-   整个 Azure Sentinel 门户

### <a name="fabrikams-solution"></a>Fabrikam 的解决方案

以下步骤应用 [Azure Sentinel 工作区设计决策树](design-your-workspace-architecture.md)来确定最适合 Fabrikam 的工作区设计：

1.  Fabrikam 没有现有的工作区，因此继续执行[步骤 2](design-your-workspace-architecture.md#step-2-keeping-data-in-different-azure-geographies)。

1.  Fabrikam 无需遵守法规要求，因此继续执行[步骤 3](design-your-workspace-architecture.md#step-3-do-you-have-multiple-azure-tenants)。

1.  Fabrikam 有一个单租户环境。 因此继续执行[步骤 4](design-your-workspace-architecture.md#step-4-splitting-billing--charge-back)。

1.  Fabrikam 无需拆分费用，因此继续执行[步骤 5](design-your-workspace-architecture.md#step-5-collecting-any-non-soc-data)。

1.  Fabrikam 需要为其 SOC 和运营团队提供独立的工作区：

    Fabrikam 运营团队需要从 VM 和 AKS 收集性能数据。 由于 AKS 基于诊断设置，因此 Fabrikam 可以选择要发送到特定工作区的特定日志。 Fabrikam 可以选择将 AKS 审核日志发送到 Azure Sentinel 工作区，并将所有 AKS 日志发送到未启用 Azure Sentinel 的独立工作区。 在未启用 Azure Sentinel 的工作区中，Fabrikam 将启用容器见解解决方案。

    对于 Windows VM，Fabrikam 可以使用 [Azure Monitoring Agent (AMA)](connect-windows-security-events.md#connector-options) 拆分日志，并将安全事件发送到 Azure Sentinel 工作区，将性能事件和 Windows 事件发送到未启用 Azure Sentinel 的工作区。

    Fabrikam 选择将其重叠数据（例如安全事件和 Azure 活动事件）视为只是 SOC 数据，并将这些数据发送到已启用 Azure Sentinel 的工作区。

1.  带宽成本不是 Fabrikam 的重要考虑因素，因此继续执行[步骤 7](design-your-workspace-architecture.md#step-7-segregating-data-or-defining-boundaries-by-ownership)。

1.  Fabrikam 已决定为 SOC 和运营团队使用独立的工作区。 无需进一步分隔。

1.  Fabrikam 确实需要控制对重叠数据（包括安全事件和 Azure 活动事件）的访问，但没有行级要求。

    安全事件和 Azure 活动事件都不是自定义日志，因此 Fabrikam 可以使用表级 RBAC 为运营团队授予对这两个表的访问权限。

下图演示了适合 Fabrikam 的最终 Azure Sentinel 工作区设计，出于设计简洁性，其中只包括了关键的日志源：

:::image type="content" source="media/best-practices/fabrikam-solution.png" alt-text="Diagram 解决方案示意图，其中的运营团队使用一个独立的工作区。" border="false" :::

建议的解决方案包括：

- 在美国区域有两个独立的工作区：一个已启用 Azure Sentinel 的工作区供 SOC 团队使用，另一个未启用 Azure Sentinel 的工作区供运营团队使用。

- [Azure Monitoring Agent (AMA)](connect-windows-security-events.md#connector-options)，用于确定要将来自 Azure 和本地 VM 的哪些日志发送到每个工作区。

- 诊断设置，用于确定要将来自 Azure 资源（例如 AKS）的哪些日志发送到每个工作区。

- 发送到 Azure Sentinel 工作区的重叠数据，根据需要使用表级 RBAC 为运营团队授予访问权限。

## <a name="sample-3-multiple-tenants-and-regions-and-centralized-security"></a>示例 3：多个租户和区域，使用集中化安全性

Adventure Works 是一家跨国公司，总部位于东京。 Adventure Works 在全球的不同国家/地区设立了 10 个不同的子实体。

Adventure Works 是 Microsoft 365 E5 客户，在 Azure 中已有工作负载。

### <a name="adventure-works-tenancy-requirements"></a>Adventure Works 租户要求

Adventure Works 有三个不同的 Azure AD 租户，在设立了子实体的每个大陆各有一个租户：亚洲、欧洲和非洲。 不同子实体所在的国家/地区在其所属大陆的租户中具有标识。 例如，日本用户位于“亚洲”租户中，德国用户位于“欧洲”租户中，埃及用户位于“非洲”租户中  。

### <a name="adventure-works-compliance-and-regional-requirements"></a>Adventure Works 合规性和区域要求

Adventure Works 目前使用三个 Azure 区域，每个区域与子实体所在的大陆相对应。 Adventure Works 没有严格的合规性要求。

### <a name="adventure-works-resource-types-and-collection-requirements"></a>Adventure Works 资源类型和收集要求

Adventure Works 需要收集每个子实体的以下数据源：

-   Azure AD 登录和审核日志
-   Office 365 日志
-   Microsoft 365 Defender for Endpoint 原始日志
-   Azure 活动
-   Azure Defender
-   Azure PaaS 资源，例如来自 Azure 防火墙、Azure 存储、Azure SQL 和 Azure WAF 的资源
-   来自 Azure VM 的安全事件和 Windows 事件
-   来自本地网络设备的 CEF 日志

Azure VM 分散在三个大陆，但带宽成本不是考虑因素。

### <a name="adventure-works-access-requirements"></a>Adventure Works 访问要求

Adventure Works 有一个集中式 SOC 团队，该团队负责监督所有不同子实体的安全运营。

Adventure Works 还有三个独立的 SOC 团队，每个大陆各有一个 SOC 团队。 每个大陆的 SOC 团队应该只能访问其所在区域内生成的数据，而不能查看其他大陆的数据。 例如，亚洲 SOC 团队应该只能访问来自亚洲部署的 Azure 资源的数据、来自亚洲租户的 AAD 登录事件，以及来自亚洲租户的 Defender for Endpoint 日志。

每个大陆的 SOC 团队需要访问整个 Azure Sentinel 门户体验。

Adventure Works 的运营团队独立运作，具有自己的未启用 Azure Sentinel 的工作区。

### <a name="adventure-works-solution"></a>Adventure Works 解决方案

以下步骤应用 [Azure Sentinel 工作区设计决策树](design-your-workspace-architecture.md)来确定最适合 Adventure Works 的工作区设计：

1.  Adventure Works 的运营团队具有自己的工作区，因此继续执行[步骤 2](design-your-workspace-architecture.md#step-2-keeping-data-in-different-azure-geographies)。

1.  Adventure Works 无需遵守法规要求，因此继续执行[步骤 3](design-your-workspace-architecture.md#step-3-do-you-have-multiple-azure-tenants)。

1.  Adventure Works 有三个 Azure AD 租户，需要收集租户级数据源，例如 Office 365 日志。 因此，Adventure Works 至少应创建 Azure Sentinel 工作区，为每个租户各创建一个。

1.  Adventure Works 无需拆分费用，因此继续执行[步骤 5](design-your-workspace-architecture.md#step-5-collecting-any-non-soc-data)。

1.  由于 Adventure Works 运营团队有自己的工作区，因此 Adventure Works SOC 团队将使用此决策中考虑的所有数据。

1.  带宽成本不是 Adventure Works 的重要考虑因素，因此继续执行[步骤 7](design-your-workspace-architecture.md#step-7-segregating-data-or-defining-boundaries-by-ownership)。

1.  Adventure Works 确实需要按所有权隔离数据，因为每种内容的 SOC 团队只需访问与该内容相关的数据。 但是，每个大陆的 SOC 团队还需要访问整个 Azure Sentinel 门户。

1.  Adventure Works 不需要按表控制数据访问。

下图演示了适合 Adventure Works 的最终 Azure Sentinel 工作区设计，出于设计简洁性，其中只包括了关键的日志源：

:::image type="content" source="media/best-practices/adventure-works-solution.png" alt-text="Adventure Works 解决方案示意图，其中每个 Azure AD 租户具有独立的工作区。" border="false":::

建议的解决方案包括：

- 每个 Azure AD 租户使用的独立 Azure Sentinel 工作区。 每个工作区将收集与其租户相关的、所有数据源的数据。

- 每个大陆的 SOC 团队只能访问其自己租户中的工作区，从而确保每个 SOC 团队只能访问租户边界内生成的日志。

- 中心 SOC 团队仍可从独立的 Azure AD 租户操作，并使用 Azure Lighthouse 访问每个不同的 Azure Sentinel 环境。 如果没有其他租户，则中心 SOC 团队仍可使用 Azure Lighthouse 来访问远程工作区。

- 如果中心 SOC 团队需要存储对大陆 SOC 团队隐藏的项目，或者想要引入与大陆 SOC 团队不相关的其他数据，则他们还可以创建一个额外的工作区。



## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
>[加入 Azure Sentinel](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[了解警报](get-visibility.md)