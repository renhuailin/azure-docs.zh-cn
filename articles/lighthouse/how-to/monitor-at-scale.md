---
title: 大规模监视委托资源
description: Azure Lighthouse 可帮助你在客户租户间以可缩放的方式来使用 Azure Monitor 日志。
ms.date: 09/30/2021
ms.topic: how-to
ms.openlocfilehash: 7ae54918ffad64e6b9790c4458717807cacd09ad
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129363233"
---
# <a name="monitor-delegated-resources-at-scale"></a>大规模监视委托资源

作为服务提供商，你可能已经将多个客户租户载入了 [Azure Lighthouse](../overview.md)。 Azure Lighthouse 允许服务提供商同时在多个租户之间大规模执行操作，从而提高管理任务的效率。

本主题介绍如何在你管理的客户租户之间以可伸缩方式使用 [Azure Monitor 日志](../../azure-monitor/logs/data-platform-logs.md)。 尽管本主题中提及的是服务提供商和客户，但本指南同样适用于[使用 Azure Lighthouse 管理多个租户的企业](../concepts/enterprise.md)。

> [!NOTE]
> 请确保已在委托客户订阅上向你管理的租户中的用户授予[管理 Log Analytics 工作区所需的角色](../../azure-monitor/logs/manage-access.md#manage-access-using-azure-permissions)。

## <a name="create-log-analytics-workspaces"></a>创建 Log Analytics 工作区

为了收集数据，需要创建 Log Analytics 工作区。 这些 Log Analytics 工作区是针对 Azure Monitor 所收集数据的唯一环境。 每个工作区都有其自己的数据存储库和配置，并且数据源和解决方案均配置为将其数据存储在特定工作区中。

建议直接在客户租户中创建这些工作区。 这样，它们的数据将保留在其租户中，而不是导出到您的租户中。 在客户租户中创建工作区便于集中监视 Log Analytics 支持的任何资源或服务，从而更灵活地选择所监视的数据类型。 要通过[诊断设置](../..//azure-monitor/essentials/diagnostic-settings.md)收集信息，需要在客户租户中创建工作区。

> [!TIP]
> 必须在与工作区相同的租户中创建用于访问 Log Analytics 工作区中数据的所有自动化帐户。

您可以通过使用 [Azure 门户](../../azure-monitor/logs/quick-create-workspace.md)、[Azure CLI](../../azure-monitor/logs/resource-manager-workspace.md) 或 [Azure PowerShell](../../azure-monitor/logs/powershell-workspace-configuration.md) 来创建 Log Analytics 工作区。

> [!IMPORTANT]
> 如果在客户租户中创建了所有工作区，也必须在管理租户的订阅中[注册](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider) Microsoft Insights 资源提供程序。 如果你的管理租户没有现有的 Azure 订阅，可以使用以下 PowerShell 命令手动注册资源提供程序：
>
> ```powershell
> $ManagingTenantId = "your-managing-Azure-AD-tenant-id"
> 
> # Authenticate as a user with admin rights on the managing tenant
> Connect-AzAccount -Tenant $ManagingTenantId
> 
> # Register the Microsoft.Insights resource providers Application Ids
> New-AzADServicePrincipal -ApplicationId 1215fb39-1d15-4c05-b2e3-d519ac3feab4
> New-AzADServicePrincipal -ApplicationId 6da94f3c-0d67-4092-a408-bb5d1cb08d2d 
> ```

## <a name="deploy-policies-that-log-data"></a>部署记录数据的策略

创建 Log Analytics 工作区后，可以在客户层次结构之间部署 [Azure 策略](../../governance/policy/index.yml)，以便将诊断数据发送到每个租户中的相应工作区。 根据要监视的资源类型，部署的确切策略可能会有所不同。

若要了解有关创建策略的详细信息，请参阅[教程：创建和管理策略以强制实施符合性](../../governance/policy/tutorials/create-and-manage.md)。 此[社区工具](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/azure-diagnostics-policy-generator)提供了一个脚本，用于帮助你创建策略来监视所选的特定资源类型。

确定要部署的策略后，可以[将它们大规模部署到委托订阅](policy-at-scale.md)。

## <a name="analyze-the-gathered-data"></a>分析收集的数据

部署策略后，会将数据记录在每个客户租户中创建的 Log Analytics 工作区中。 若要深入了解所有受管理客户，可以使用 [Azure Monitor 工作簿](../../azure-monitor/visualize/workbooks-overview.md)之类的工具从多个数据源收集和分析信息。

## <a name="query-data-across-customer-workspaces"></a>跨客户工作区查询数据

可以通过创建包含多个工作区的并集，运行[日志查询](../../azure-monitor/logs/log-query-overview.md)以在不同客户租户的 Log Analytics 工作区中检索数据。 通过包含 TenantID 列，可以查看哪些结果属于哪些租户。

以下示例查询在 AzureDiagnostics 表上跨两个单独客户租户中的工作区创建并集。 结果会显示 Category、ResourceGroup 和 TenantID 列。

``` Kusto
union AzureDiagnostics,
workspace("WS-customer-tenant-1").AzureDiagnostics,
workspace("WS-customer-tenant-2").AzureDiagnostics
| project Category, ResourceGroup, TenantId
```

有关跨多个 Log Analytics 工作区的查询的更多示例，请参阅[使用 Azure Monitor 跨资源进行查询](../../azure-monitor/logs/cross-workspace-query.md)。

> [!IMPORTANT]
> 如果使用用于从 Log Analytics 工作区查询数据的自动化帐户，则该自动化帐户必须在与工作区相同的租户中进行创建。

## <a name="view-alerts-across-customers"></a>跨客户查看警报

可以在你管理的客户租户中查看委派订阅的[警报](../../azure-monitor/alerts/alerts-overview.md)。

在你管理的租户中，你可以通过 Azure 门户或 API 和管理工具来[创建、查看和管理活动日志警报](../../azure-monitor/alerts/alerts-activity-log.md)。

若要跨多个客户自动刷新警报，请使用 [Azure Resource Graph](../../governance/resource-graph/overview.md) 查询来筛选警报。 可以将查询固定到仪表板，并选择所有相应的客户和订阅。 例如，下面的查询将显示严重性为 0 和 1 的警报，每 60 分钟刷新一次。

```kusto
alertsmanagementresources
| where type == "microsoft.alertsmanagement/alerts"
| where properties.essentials.severity =~ "Sev0" or properties.essentials.severity =~ "Sev1"
| where properties.essentials.monitorCondition == "Fired"
| where properties.essentials.startDateTime > ago(60m)
| project StartTime=properties.essentials.startDateTime,name,Description=properties.essentials.description, Severity=properties.essentials.severity, subscriptionId
| sort by tostring(StartTime)
```

## <a name="next-steps"></a>后续步骤

- 尝试使用 GitHub 中的[按域分类的活动日志](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain)工作簿。
- 探索[已构建 MVP 的示例工作簿](https://github.com/scautomation/Azure-Automation-Update-Management-Workbooks)，该工作簿通过在多个 Log Analytics 工作区中[查询更新管理日志](../../automation/update-management/query-logs.md)来跟踪补丁合规性报表。
- 了解其他[跨租户管理体验](../concepts/cross-tenant-management-experience.md)。