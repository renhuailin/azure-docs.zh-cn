---
title: 启动/停止 VM v2（预览版）概述
description: 本文介绍启动/停止 VM v2（预览版）功能，该功能可按计划启动或停止 Azure 资源管理器 VM 和经典 VM。
ms.topic: conceptual
ms.service: azure-functions
ms.subservice: start-stop-vms
ms.date: 06/25/2021
ms.openlocfilehash: 24872e96333aeb67661c462e54acebc62b32c8aa
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129455411"
---
# <a name="startstop-vms-v2-preview-overview"></a>启动/停止 VM v2（预览版）概述

启动/停止 VM v2（预览版）功能可跨多个订阅启动或停止 Azure 虚拟机 (VM)。 该功能按照用户定义的计划启动或停止 Azure VM，通过 [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) 提供见解，并使用[操作组](../../azure-monitor/alerts/action-groups.md)发送可选通知。 在大多数情况下，该功能可以同时管理 Azure 资源管理器 VM 和经典 VM。

此新版本启动/停止 VM v2（预览版）为想要降低 VM 成本的客户提供了一个分散的低成本自动化选项。 此版本提供与 Azure 自动化的[原始版本](../../automation/automation-solution-vm-management.md)相同的所有功能，但它旨在利用 Azure 中的新技术。

## <a name="overview"></a>概述

启动/停止 VM v2（预览版）经过重新设计，并且不依赖 Azure 自动化日志或 Azure Monitor 日志，而[以前版本](../../automation/automation-solution-vm-management.md)需要依赖这些日志。 此版本依赖 [Azure Functions](../../azure-functions/functions-overview.md) 处理 VM 启动和停止执行。

系统将在 Azure Active Directory (Azure AD) 中为此 Azure Functions 应用程序创建托管标识，使启动/停止 VM v2（预览版）可以轻松访问其他受 Azure AD 保护的资源，例如逻辑应用和 Azure VM。 有关 Azure AD 中的托管标识的详细信息，请参阅 [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。

系统将创建 HTTP 触发器终结点函数，来为此功能随附的计划方案和序列方案提供支持，如下表所示。

|名称 |触发器 |说明 |
|-----|--------|------------|
|计划 |HTTP |此函数适用于计划方案和序列方案（根据有效负载架构区分）。 它是从逻辑应用调用的入口点函数，并采用有效负载来处理 VM 启动操作或停止操作。 |
|AutoStop |HTTP |此函数支持 AutoStop 方案，这是从逻辑应用调用的入口点函数。|
|AutoStopVM |HTTP |当满足警报条件时，VM 警报会自动触发此函数。|
|VirtualMachineRequestOrchestrator |队列 |此函数将从 Scheduled 函数获取有效负载信息，并协调 VM 启动请求和停止请求。|
|VirtualMachineRequestExecutor |队列 |此函数将对 VM 执行实际的启动操作和停止操作。|
|CreateAutoStopAlertExecutor |队列 |此函数将从 AutoStop 函数获取有效负载信息，以创建关于 VM 的警报。|
|HeartBeatAvailabilityTest |计时器 |此函数监视主要 HTTP 函数的可用性。|
|CostAnalyticsFunction |计时器 |此函数计算每月运行启动/停止 V2 解决方案所需的成本。|
|SavingsAnalyticsFunction |计时器 |此函数计算启动/停止 V2 解决方案每月可节省的总成本。|
|VirtualMachineSavingsFunction |队列 |此函数计算在 VM 上通过启动/停止 V2 解决方案实现的实际成本节省。|

例如，Scheduled HTTP 触发器函数用于处理计划方案和序列方案。 同样，AutoStop HTTP 触发器函数用于处理自动停止方案。

支持此功能需要基于队列的触发器函数。 所有基于计时器的触发器均用于执行可用性测试，以及监视系统的运行状况。

 [Azure 逻辑应用](../../logic-apps/logic-apps-overview.md)用于配置以及管理 VM 操作的启动计划和停止计划，方法是使用 JSON 有效负载来调用函数。 默认情况下，在初始部署期间，它会为下列方案创建总共 5 个逻辑应用：

- “计划”- 启动操作和停止操作基于你针对 Azure 资源管理器 VM 和经典 VM 指定的计划。 ststv2_vms_Scheduled_start 和 ststv2_vms_Scheduled_stop 分别用于配置计划的启动和计划的停止 。

- “序列”- 启动操作和停止操作基于针对具有预定义排序标记的 VM 的计划。 仅支持两个命名标记，即 sequencestart 和 sequencestop 。 ststv2_vms_Sequenced_start 和 ststv2_vms_Sequenced_stop 分别用于配置排序启动和排序停止 。

    > [!NOTE]
    > 此方案仅支持 Azure 资源管理器 VM。

- AutoStop - 此功能仅用于根据 Azure 资源管理器 VM 和经典 VM 的 CPU 使用率对它们执行停止操作。 此功能也可以是基于计划的操作，可创建关于 VM 的警报，并根据条件触发警报来执行停止操作。 ststv2_vms_AutoStop 用于配置自动停止功能。

每个启动/停止操作都支持分配一个或多个订阅、资源组或 VM 列表。

启动/停止 VM v2（预览版）也使用 Functions 所需的 Azure 存储帐户，该帐户用于以下两个目的：

   - 使用 Azure 表存储来存储执行操作元数据（即，启动/停止 VM 操作）。

   - 使用 Azure 队列存储来支持 Azure Functions 基于队列的触发器。

所有遥测数据（即函数应用执行的跟踪日志）将发送到连接的 Application Insights 实例。 可以通过共享 [Azure 仪表板](../../azure-portal/azure-portal-dashboards.md)中提供的一组预定义可视化效果查看存储在 Application Insights 中的遥测数据。

:::image type="content" source="media/overview/shared-dashboard-sml.png" alt-text="启动/停止 VM 共享状态仪表板":::

对 VM 执行操作后，系统还会发送电子邮件通知。

## <a name="new-releases"></a>最新发布

当启动/停止 VM v2（预览版）的新版本发布时，实例会自动更新，你无需手动重新部署。

## <a name="supported-scoping-options"></a>支持的范围选项

### <a name="subscription"></a>订阅

如果需要对整个订阅中的所有 VM 执行启动操作和停止操作，你可以使用将范围限定于订阅的功能，并且可以根据需要选择多个订阅。  

你还可以指定要排除的 VM 的列表，这样就不会对排除的 VM 执行相应操作。 你还可以使用通配符来指定可同时忽略的所有 VM 的名称。

### <a name="resource-group"></a>资源组

如果需要对所有 VM 执行启动操作和停止操作，你可以通过指定一个或多个资源组名称来使用将范围限定于资源组的功能，还可以跨一个或多个订阅执行启动操作和停止操作。

你还可以指定要排除的 VM 的列表，这样就不会对排除的 VM 执行相应操作。 你还可以使用通配符来指定可同时忽略的所有 VM 的名称。

### <a name="vmlist"></a>VMList

如果需要对一组特定的虚拟机执行启动操作和停止操作，以及跨多个订阅执行启动操作和停止操作，你可以使用指定 VM 列表的功能。 此选项不支持指定要排除的 VM 的列表。

## <a name="prerequisites"></a>必备条件

- 你的 Azure 帐户必须具有有效订阅。 [免费创建帐户](https://azure.microsoft.com/free/)。

- 你的帐户已获得订阅中的[参与者](../../role-based-access-control/built-in-roles.md#contributor)权限。

- 启动/停止 VM v2（预览版）在所有 Azure 全球云区域和美国政府云区域可用，Azure Functions 的[可用产品(按区域)](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=functions)页中列出了这些区域。

## <a name="next-steps"></a>后续步骤

若要部署此功能，请参阅[部署启动/停止 VM](deploy.md)（预览版）。
