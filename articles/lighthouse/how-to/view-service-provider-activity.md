---
title: 监视服务提供商活动
description: 客户可监视记录的活动，了解服务提供商通过 Azure Lighthouse 执行的操作。
ms.date: 07/16/2021
ms.topic: how-to
ms.openlocfilehash: 9f24126bc8d3252de3926e72aea2915092206e38
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114389145"
---
# <a name="monitor-service-provider-activity"></a>监视服务提供商活动

已为 [Azure Lighthouse](../overview.md) 委托了订阅的客户可以[查看 Azure 活动日志](../../azure-monitor/essentials/platform-logs-overview.md)数据，以了解所有执行的操作。 这样，客户就能够全面了解服务提供商正在执行的操作，以及用户自己的 Azure Active Directory (Azure AD) 租户中的用户完成的操作。

## <a name="view-activity-log-data"></a>查看活动日志数据

可以从 Azure 门户中“监视器”菜单[查看活动日志](../../azure-monitor/essentials/activity-log.md#view-the-activity-log)。 若要只查看特定订阅的结果，请使用筛选器选择特定订阅。 你还可以通过编程方式[查看和检索活动日志事件](../../azure-monitor/essentials/activity-log.md#view-the-activity-log)。

> [!NOTE]
> 如果将客户租户中委托的订阅载入到 Azure Lighthouse 时已授予服务提供商租户中的用户[读者](../../role-based-access-control/built-in-roles.md#reader)角色（或包含读者访问权限的另一个内置角色），则这些用户可以查看有关该订阅的活动日志结果。

活动日志将显示操作的名称及其状态，以及执行该操作的日期和时间。 “事件发起者”列显示执行该操作的用户（无论是通过 Azure Lighthouse 操作的服务提供商租户中的用户还是客户自己的租户中的用户）。 请注意，日志将显示用户名，而不显示已分配给用户的用于该订阅的租户或角色。

可通过 Azure 门户查看过去 90 天内日志记录的活动。 若要了解如何将此数据存储 90 天以上，请参阅[在 Log Analytics 工作区中收集和分析 Azure 活动日志](../../azure-monitor/essentials/activity-log.md)。

> [!NOTE]
> 活动日志中显示服务提供商的用户，但访问控制 (IAM) 不显示这些用户及其角色分配，通过 API 检索角色分配时也不显示这些内容。

## <a name="set-alerts-for-critical-operations"></a>针对关键操作设置警报

建议创建[活动日志警报](../../azure-monitor/alerts/activity-log-alerts.md)，以便了解服务提供商（或自己租户中的用户）执行的关键操作。 例如，你需要跟踪对订阅执行的所有管理操作，或者需要在特定资源组中的任意虚拟机被删除时收到通知。 你创建警报后，警报将包含客户自己的租户以及任何托管租户中的用户执行的操作。

有关详细信息，请参阅[创建和管理活动日志警报](../../azure-monitor/alerts/alerts-activity-log.md)。

## <a name="create-log-queries"></a>创建日志查询

日志查询可帮助分析记录的活动或重点查看特定项目。 例如，可能审核要求报告对订阅执行的所有管理级别的操作。 你可以创建一个查询，以用于仅筛选这些操作并按用户、日期或其他值对结果进行排序。

有关详细信息，请参阅 [Azure Monitor 中的日志查询概述](../../azure-monitor/logs/log-query-overview.md)。

## <a name="view-user-activity-across-domains"></a>查看各个域的用户活动

若要查看多个域的各个用户执行的活动，请使用[按域列出的活动日志](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain)示例工作簿。

可以按域名筛选结果。 你还可以应用其他筛选器，例如类别、级别或资源组。

## <a name="next-steps"></a>后续步骤

- 了解如何[审核和限制委托](view-manage-service-providers.md#audit-and-restrict-delegations-in-your-environment)。
- 详细了解 [Azure Monitor](../../azure-monitor/index.yml)。
- 了解如何在 Azure 门户中[查看和管理服务提供商产品/服务](view-manage-service-providers.md)。
