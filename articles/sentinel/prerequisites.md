---
title: 部署 Azure Sentinel 的先决条件
description: 了解有关部署 Azure Sentinel 的部署前活动和先决条件。
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 07/21/2021
ms.openlocfilehash: b288a826a0911e73f516fa13a00d3e26b25d2ace
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178616"
---
# <a name="pre-deployment-activities-and-prerequisites-for-deploying-azure-sentinel"></a>有关部署 Azure Sentinel 的部署前活动和先决条件

本文介绍了有关部署 Azure Sentinel 的部署前活动和先决条件。

## <a name="pre-deployment-activities"></a>部署前活动

在部署 Azure Sentinel 之前，建议你执行以下步骤，以便尽快将部署重点放在提供最大价值上。

1. 确定你需要的[数据源](connect-data-sources.md)和数据大小要求，以便准确地预测部署的预算和时间线。

    你可以在业务用例评审过程中确定此信息，也可以通过评估现有的 SIEM 来这样做。 如果 SIEM 已就位，请分析你的数据，以了解哪些数据源可以提供最大的价值，因此应当引入到 Azure Sentinel 中。

1. 设计 Azure Sentinel 工作区。 请考虑以下参数：

    - 是使用单租户还是多租户
    - 你对数据收集和存储的任何合规性要求
    - 如何控制对 Azure Sentinel 数据的访问

    有关详细信息，请参阅[工作区体系结构最佳做法](best-practices-workspace-architecture.md)和[示例工作区设计](sample-workspace-designs.md)。

1. 在确定业务用例、数据源和数据大小要求后，[开始计划预算](azure-sentinel-billing.md)并考虑所计划的每个方案的成本影响。

    请确保预算涵盖了 Azure Sentinel 和 Azure Log Analytics 的数据引入成本、将部署的任何 playbook 的成本，等等。

    有关详细信息，请参阅：

    - [Azure Sentinel 成本和账单](azure-sentinel-billing.md)
    - [Azure Sentinel 定价](https://azure.microsoft.com/pricing/details/azure-sentinel/)
    - [Log Analytics 定价](https://azure.microsoft.com/pricing/details/monitor/)
    - [逻辑应用 (playbook) 定价](https://azure.microsoft.com/pricing/details/logic-apps/)
    - [集成 Azure 数据资源管理器以实现长期日志保留](store-logs-in-azure-data-explorer.md)

1. 根据要求和时间线，指定一名工程师或架构师来领导部署。 此人员应领导部署，是团队的主要联系点。

## <a name="azure-tenant-requirements"></a>Azure 租户要求

在部署 Azure Sentinel 之前，请确保你的 Azure 租户满足以下要求：

- 若要访问 Azure 并部署资源，需要一个 [Azure Active Directory 许可证和租户](../active-directory/develop/quickstart-create-new-tenant.md)或[具有有效付款方式的个人帐户](https://azure.microsoft.com/free/)。

- 在有了租户后，你必须有一个 [Azure 订阅](../cost-management-billing/manage/create-subscription.md)以跟踪资源创建和计费。

- 在有了订阅后，你需要拥有[相关权限](../role-based-access-control/index.yml)才能开始使用你的订阅。 如果你使用新的订阅，则应将 AAD 租户的管理员或权限更高的角色指定为订阅的[所有者/参与者](../role-based-access-control/rbac-and-directory-admin-roles.md)。

    - 若要使最低特权访问权限保持可用，请在资源组级别分配角色。
    - 若要更好地控制权限和访问权限，请设置自定义角色。 有关详细信息，请参阅[基于角色的访问控制](../role-based-access-control/custom-roles.md)。
    - 为了在用户与安全用户之间进行进一步的分隔，你可能需要使用[资源上下文 RBAC](resource-context-rbac.md) 或[表级 RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043)。

    有关 Azure Sentinel 支持的其他角色和权限的详细信息，请参阅 [Azure Sentinel 中的权限](roles.md)。

- 需要一个 [Log Analytics 工作区](../azure-monitor/logs/quick-create-workspace.md)来容纳 Azure Sentinel 将要引入并用于其检测、分析和其他功能的所有数据。 有关详细信息，请参阅 [Azure Sentinel 工作区体系结构最佳做法](best-practices-workspace-architecture.md)。

> [!TIP]
> 设置 Azure Sentinel 工作区时，请[创建一个资源组](../azure-resource-manager/management/manage-resource-groups-portal.md)，将其专用于 Azure Sentinel 和 Azure Sentinel 使用的资源，包括 Log Analytics 工作区、任何 playbook、工作簿，等等。
>
> 使用专用资源组，可以在资源组级别分配权限一次，权限便自动应用于任何相关资源。 通过资源组管理访问权限有助于确保你有效地使用 Azure Sentinel，避免意外授予不恰当的权限。 当资源分散在多个资源组中时，如果没有 Azure Sentinel 的资源组，则用户或服务主体可能会发现自己由于权限不足而无法执行所需的操作或查看数据。
>
> 若要按层级实现对资源的更多访问控制，请使用额外的资源组来容纳只应由这些组访问的资源。 通过使用多个层级的资源组，可以在这些层级之间分隔访问权限。
>

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
>[加入 Azure Sentinel](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[了解警报](get-visibility.md)