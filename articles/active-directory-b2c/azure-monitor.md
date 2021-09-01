---
title: 使用 Azure Monitor 监视 Azure AD B2C
titleSuffix: Azure AD B2C
description: 了解如何使用委托资源管理通过 Azure Monitor 记录 Azure AD B2C 事件。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.author: mimart
ms.subservice: B2C
ms.date: 07/19/2021
ms.openlocfilehash: 4a7fdf12ecf123c1fb741dcbd2706f7ca9a1d5c2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732445"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>使用 Azure Monitor 监视 Azure AD B2C

使用 Azure Monitor 将 Azure Active Directory B2C (Azure AD B2C) 登录日志和[审核](view-audit-logs.md)日志路由到不同的监视解决方案。 然后，可以保留日志供长期使用，或者将其与第三方安全信息和事件管理 (SIEM) 工具集成，以获取有关环境的见解。

可将日志事件路由到：

- Azure [存储帐户](../storage/blobs/storage-blobs-introduction.md)。
- [Log Analytics 工作区](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)（以分析数据、创建仪表板以及针对特定事件发出警报）。
- Azure [事件中心](../event-hubs/event-hubs-about.md)（并与 Splunk 和 Sumo Logic 实例集成）。

![Azure Monitor](./media/azure-monitor/azure-monitor-flow.png)

本文介绍如何将日志传输到 Azure Log Analytics 工作区。 然后，可以创建仪表板或创建基于 Azure AD B2C 用户活动的警报。

> [!IMPORTANT]
> 当你计划将 Azure AD B2C 日志传输到不同的监视解决方案或存储库时，请注意以下几点。 Azure AD B2C 日志包含个人数据。 此类数据的处理方式应确保个人数据的适当安全，包括使用适当的技术或组织措施防范未经授权或非法处理。

## <a name="deployment-overview"></a>部署概述

Azure AD B2C 使用 [Azure Active Directory 监视](../active-directory/reports-monitoring/overview-monitoring.md)。 不同于 Azure AD 租户，Azure AD B2C 租户不能具有与之关联的订阅，因此，我们需要执行一些额外的步骤来启用 Azure AD B2C 与 Log Analytics 之间的集成，我们将在此过程中发送日志。
若要在 Azure AD B2C 租户内启用 Azure Active Directory 中的诊断设置，请使用 [Azure Lighthouse](../lighthouse/overview.md) [委托资源](../lighthouse/concepts/architecture.md)，这样 Azure AD B2C（服务提供商）就可以管理 Azure AD（客户）资源。 

> [!TIP]
> Azure Lighthouse 通常用于管理多个客户的资源。 但是，其还可用于管理本身具有多个 Azure AD 租户的企业中的资源，我们会在此期间这样做，只不过我们仅委托对单个资源组的管理。

完成本文中的步骤后，你将创建一个新的资源组（此处称为“azure-ad-b2c-monitor”）并且具有在 Azure AD B2C 门户中访问包含 [Log Analytics 工作区](../azure-monitor/logs/quick-create-workspace.md)的相同资源组的权限。 还可以将日志从 Azure AD B2C 传输到 Log Analytics 工作区。

在此部署期间，授权 Azure AD B2C 目录中的某个用户或组在包含你的 Azure 订阅的租户中配置 Log Analytics 工作区实例。 若要创建授权，需要将 [Azure 资源管理器](../azure-resource-manager/index.yml)模板部署到包含 Log Analytics 工作区的订阅。

下图描绘了要在 Azure AD 和 Azure AD B2C 租户中配置的组件。

![资源组投影](./media/azure-monitor/resource-group-projection.png)

在此部署过程中，你将配置将托管 Log Analytics 工作区的 Azure AD B2C 租户和 Azure AD 租户。 应为使用的 Azure AD B2C 帐户（例如管理员帐户）分配 Azure AD B2C 租户的[全局管理员](../active-directory/roles/permissions-reference.md#global-administrator)角色。 必须为用于运行部署的 Azure AD 帐户分配 Azure AD 订阅的[所有者](../role-based-access-control/built-in-roles.md#owner)角色。 另外，请务必在完成所述的每个步骤时登录到正确的目录。

总之，你将使用 Azure Lighthouse 来允许 Azure AD B2C 租户中的用户或组管理与其他租户（Azure AD 租户）关联的订阅中的资源组。 完成此授权后，可以在 Azure AD B2C 的“诊断”设置中，将订阅和日志分析工作区选作目标。

## <a name="1-create-or-choose-resource-group"></a>1.创建或选择资源组

首先，创建或选择一个资源组，其中包含将从 Azure AD B2C 接收数据的目标 Log Analytics 工作区。 部署 Azure 资源管理器模板时请指定资源组名称。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD 租户的目录。
1. [创建资源组](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)或选择现有资源组。 此示例使用名为 azure-ad-b2c-monitor 的资源组。

## <a name="2-create-a-log-analytics-workspace"></a>2.创建 Log Analytics 工作区

Log Analytics 工作区是适用于 Azure Monitor 日志数据的唯一环境。 你将使用此 Log Analytics 工作区从 Azure AD B2C [审核日志](view-audit-logs.md)收集数据，然后使用查询和工作簿将其可视化，或创建警报。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD 租户的目录。
1. [创建 Log Analytics 工作区](../azure-monitor/logs/quick-create-workspace.md)。 此示例在名为 azure-ad-b2c-monitor 的资源组中使用名为 AzureAdB2C 的 Log Analytics 工作区。

## <a name="3-delegate-resource-management"></a>3.委托资源管理

在此步骤中，你将选择 Azure AD B2C 租户作为“服务提供商”。 还要定义将相应的 Azure 内置角色分配到 Azure AD 租户中的组所需的授权。

### <a name="31-get-your-azure-ad-b2c-tenant-id"></a>3.1 转到 Azure AD B2C 租户 ID

首先，获取 Azure AD B2C 目录的“租户 ID”（也称为目录 ID）。

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录。
1. 依次选择“Azure Active Directory”、“概述”。
1. 记录“租户 ID”。

### <a name="32-select-a-security-group"></a>3.2 选择安全组

现在选择要向其授予对前面在包含订阅的目录中创建的资源组的权限的 Azure AD B2C 组或用户。

为了简化管理，建议为每个角色使用 Azure AD 用户组，这使你能够向组添加或删除单个用户，而不是直接向此用户分配权限。 在本演练中，我们将添加一个安全组。

> [!IMPORTANT]
> 若要为 Azure AD 组添加权限，“组类型”必须设置为“安全”。 此选项是在创建组时选择的。 有关详细信息，请参阅[使用 Azure Active Directory 创建基本组并添加成员](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。

1. 在“Azure AD B2C”目录中的“Azure Active Directory”仍处于选中状态时，选择“组”，然后选择一个组。 如果没有现有组，请创建一个“安全”组，然后添加成员。 有关详细信息，请遵循流程[使用 Azure Active Directory 创建基本组并添加成员](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。
1. 选择“概述”，并记录组的“对象 ID”。

### <a name="33-create-an-azure-resource-manager-template"></a>3.3 创建 Azure 资源管理器模板

若要在 Azure Lighthouse 中创建自定义授权和委派，我们会使用 Azure 资源管理器模板，该模板可向之前创建的 Azure AD 资源组授予 Azure AD B2C 访问权限（例如 azure-ad-b2c-monitor）。 使用“部署到 Azure”按钮从 GitHub 示例部署模板，这将打开 Azure 门户，并允许直接在门户中配置和部署模板。 对于这些步骤，请确保登录到 Azure AD 租户（而不是 Azure AD B2C 租户）。

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD 租户的目录。
3. 使用“部署到 Azure”按钮打开 Azure 门户，并直接在门户中部署模板。 有关详细信息，请参阅[创建 Azure 资源管理器模板](../lighthouse/how-to/onboard-customer.md#create-an-azure-resource-manager-template)。

   [![部署到 Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure-ad-b2c%2Fsiem%2Fmaster%2Ftemplates%2FrgDelegatedResourceManagement.json)

4. 在“自定义部署”页面上，输入以下信息：

   | 字段                 | 定义                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
   | --------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
   | 订阅          | 选择包含 Azure 订阅的目录，其中创建了 azure-ad-b2c-monitor 资源组。                                                                                                                                                                                                                                                                                                                                                                                                       |
   | Region                | 选择将部署资源的区域。                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
   | Msp 套餐名称        | 描述此定义的名称。 例如 Azure AD B2C 监视。 这是将在 Azure Lighthouse 中显示的名称。                                                                                                                                                                                                                                                                                                                                                                                          |
   | Msp 套餐说明 | 套餐的简短说明。 例如，“在 Azure AD B2C 中启用 Azure Monitor”。                                                                                                                                                                                                                                                                                                                                                                                                                                     |
   | 按租户 ID 管理  | Azure AD B2C 租户的“租户 ID”（也称为目录 ID）。                                                                                                                                                                                                                                                                                                                                                                                                                                              |
   | 授权        | 指定包含 Azure AD `principalId`、`principalIdDisplayName` 和 Azure `roleDefinitionId` 的对象的 JSON 数组。 `principalId` 是有权访问此 Azure 订阅中的资源的 B2C 组或用户的“对象 ID”。 对于本演练，请指定前面记下的组对象 ID。 对于 `roleDefinitionId`，请使用“参与者”角色的[内置角色](../role-based-access-control/built-in-roles.md)值 `b24988ac-6180-42a0-ab88-20f7382dd24c`。 |
   | 资源组名称               | 前面在 Azure AD 租户中创建的资源组的名称。 例如 _azure-ad-b2c-monitor_。                                                                                                                                                                                                                                                                                                                                                                                                              |

   下面的示例演示一个具有一个安全组的授权数组。

   ```json
   [
     {
       "principalId": "<Replace with group's OBJECT ID>",
       "principalIdDisplayName": "Azure AD B2C tenant administrators",
       "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
     }
   ]
   ```

部署模板后，可能需要花费几分钟时间（通常不超过五分钟）来完成资源投影。 可以验证 Azure AD 租户中的部署并获取资源投影的详细信息。 有关详细信息，请参阅[查看和管理服务提供商](../lighthouse/how-to/view-manage-service-providers.md)。

## <a name="4-select-your-subscription"></a>4.选择订阅

部署模板并等待几分钟让资源投影完成之后，请通过以下步骤将订阅关联到 Azure AD B2C 目录。

1. 如果当前已登录，请注销 Azure 门户（这允许在下一步中刷新会话凭据）。
2. 使用 Azure AD B2C 管理帐户登录到 [Azure 门户](https://portal.azure.com)。 此帐户必须是在[委派资源管理](#3-delegate-resource-management)步骤中指定的安全组的成员。
3. 在门户工具栏中选择“目录 + 订阅”图标。
4. 选择包含 Azure 订阅和你创建的 azure-ad-b2c-monitor 资源组的 Azure AD 目录。

   ![切换目录](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)

5. 确认是否选择了正确的目录和订阅。 在此示例中，已选择所有目录和所有订阅。

   ![在目录和订阅筛选器中选择了所有目录](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="5-configure-diagnostic-settings"></a>5.配置诊断设置

诊断设置定义要将资源的日志和指标发送到的位置。 可能的目标为：

- [Azure 存储帐户](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)
- [事件中心](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)解决方案
- [Log Analytics 工作区](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)

在此示例中，我们使用 Log Analytics 工作区来创建仪表板。

### <a name="51-create-diagnostic-settings"></a>5.1 创建诊断设置

你已准备好在 Azure 门户中[创建诊断设置](../active-directory/reports-monitoring/overview-monitoring.md)。

为 Azure AD B2C 活动日志配置监视设置：

1. 使用 Azure AD B2C 管理帐户登录到 [Azure 门户](https://portal.azure.com/)。 此帐户必须是在[选择安全组](#32-select-a-security-group)步骤中指定的安全组的成员。
1. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录。
1. 选择“Azure Active Directory”
1. 在“监视”下，选择“诊断设置” 。
1. 如果有用于此资源的现有设置，则会看到已配置的设置列表。 如果要添加新设置，请选择“添加诊断设置”；如果要编辑现有设置，请选择“编辑”。 每个设置最多只能包含一个目标类型。

   ![Azure 门户中的诊断设置窗格](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. 为设置指定名称（如果未指定）。
1. 选中要将日志发送到的每个目标对应的框。 选择“配置”并 **根据下表中所述** 指定其设置。
1. 选择“发送到 Log Analytics”，然后选择先前创建的工作区名称 (`AzureAdB2C`)。
1. 选择“AuditLogs”和“SignInLogs” 。
1. 选择“保存” 。

> [!NOTE]
> 事件发出后，最多可能需要 15 分钟它才会[在 Log Analytics 工作区中显示](../azure-monitor/logs/data-ingestion-time.md)。 此外，详细了解 [Active Directory 报告延迟](../active-directory/reports-monitoring/reference-reports-latencies.md)，这可能会影响数据过期，并在报告中发挥重要作用。

如果看到错误消息“若要将诊断设置设为将 Azure Monitor 用于 Azure AD B2C 目录，则需要设置委托的资源管理”，请确保以[安全组](#32-select-a-security-group)成员的用户身份登录，并[选择你的订阅](#4-select-your-subscription)。

## <a name="6-visualize-your-data"></a>6.可视化数据

现在，你可以配置 Log Analytics 工作区以可视化数据并配置警报。 可以在 Azure AD 租户和 Azure AD B2C 租户中进行这些配置。

### <a name="61-create-a-query"></a>6.1 创建查询

日志查询可帮助你充分利用 Azure Monitor 日志中收集的数据的价值。 使用功能强大的查询语言，只需编写极少量的代码即可联接多个表中的数据、聚合大型数据集，以及执行复杂的操作。 只要收集了支持数据，并且你了解如何构造适当的查询，就几乎能够解答任何问题和执行分析。 有关详细信息，请参阅 [Azure Monitor 日志查询入门](../azure-monitor/logs/get-started-queries.md)。

1. 从 **Log Analytics 工作区**，选择“日志”
1. 在查询编辑器中，粘贴以下 [Kusto 查询语言](/azure/data-explorer/kusto/query/)查询。 此查询显示过去 x 天内按操作列出的策略使用情况。 默认持续时间设置为 90 天 (90d)。 请注意，查询只关注由策略发出令牌/代码的操作。

   ```kusto
   AuditLogs
   | where TimeGenerated  > ago(90d)
   | where OperationName contains "issue"
   | extend  UserId=extractjson("$.[0].id",tostring(TargetResources))
   | extend Policy=extractjson("$.[1].value",tostring(AdditionalDetails))
   | summarize SignInCount = count() by Policy, OperationName
   | order by SignInCount desc  nulls last
   ```

1. 选择“运行”。 查询结果显示在屏幕底部。
1. 要保存查询以供以后使用，请选择“保存”。

   ![Log Analytics 日志编辑器](./media/azure-monitor/query-policy-usage.png)

1. 填写以下详细信息：

   - 名称 - 输入查询的名称。
   - 另存为 - 选择 `query`。
   - 类别 - 选择 `Log`。

1. 选择“保存”。

还可以通过使用 [render](/azure/data-explorer/kusto/query/renderoperator?pivots=azuremonitor) 运算符更改查询，以实现数据可视化。

```kusto
AuditLogs
| where TimeGenerated  > ago(90d)
| where OperationName contains "issue"
| extend  UserId=extractjson("$.[0].id",tostring(TargetResources))
| extend Policy=extractjson("$.[1].value",tostring(AdditionalDetails))
| summarize SignInCount = count() by Policy
| order by SignInCount desc  nulls last
| render  piechart
```

![Log Analytics 日志编辑器饼图](./media/azure-monitor/query-policy-usage-pie.png)

有关更多示例，请参阅 Azure AD B2C [SIEM GitHub 存储库](https://aka.ms/b2csiem)。

### <a name="62-create-a-workbook"></a>6.2 创建工作簿

工作簿提供了一块灵活的画布，以用于分析数据以及在 Azure 门户中创建丰富的视觉报告。 使用工作簿可以在整个 Azure 中接入多个数据源，并将其组合成统一的交互式体验。 有关详细信息，请参阅 [Azure Monitor 工作簿](../azure-monitor/visualize/workbooks-overview.md)。

按照以下说明使用 JSON 库模板创建新工作簿。 此工作簿为 Azure AD B2C 租户提供“用户见解”和“身份验证”仪表板。

1. 从“Log Analytics 工作区”中选择“工作簿”。
1. 从工具栏中选择“+ 新建”选项，以创建新工作簿。
1. 在“新建工作簿”页上，使用工具栏上的 </> 选项选择“高级编辑器”。

   ![库模板](./media/azure-monitor/wrkb-adv-editor.png)

1. 选择“库模板”。
1. 将库模板中的 JSON 替换为 [Azure AD B2C 基本工作簿](https://raw.githubusercontent.com/azure-ad-b2c/siem/master/workbooks/dashboard.json)中的内容：
1. 使用“应用”按钮应用该模板。
1. 从工具栏中选择“完成编辑”按钮，完成工作簿编辑。
1. 最后，通过使用工具栏中的“保存”按钮保存工作簿。
1. 提供“标题”，如 Azure AD B2C 仪表板。
1. 选择“保存”。

   ![保存工作簿](./media/azure-monitor/wrkb-title.png)

工作簿将以仪表板的形式显示报表。

![工作簿第一仪表板](./media/azure-monitor/wkrb-dashboard-1.png)

![工作簿第二仪表板](./media/azure-monitor/wrkb-dashboard-2.png)

![工作簿第三仪表板](./media/azure-monitor/wrkb-dashboard-3.png)

## <a name="create-alerts"></a>创建警报

警报通过警报规则在 Azure Monitor 中创建，可以按固定的时间间隔自动运行保存的查询或自定义日志搜索。 可按以下条件创建警报：基于特定性能指标、在创建某些事件时、缺少某事件时，或者在特定时间范围内创建大量事件时。 例如，当登录平均数超过特定阈值时，可以使用警报通知你。 有关详细信息，请参阅[创建警报](../azure-monitor/alerts/alerts-log.md)。

使用以下说明创建新的 Azure 警报，该警报将在请求总数与上一时段相比下降 25% 时发送[电子邮件通知](../azure-monitor/alerts/action-groups.md#configure-notifications)。 警报将每 5 分钟运行一次，并查找过去一小时与其前面的一小时相比的下降情况。 警报是使用 Kusto 查询语言创建的。

1. 从 Log Analytics 工作区，选择“日志”。
1. 使用以下查询创建新的 Kusto 查询。

   ```kusto
   let start = ago(2h);
   let end = now();
   let threshold = -25; //25% decrease in total requests.
   AuditLogs
   | serialize TimeGenerated, CorrelationId, Result
   | make-series TotalRequests=dcount(CorrelationId) on TimeGenerated from start to end step 1h
   | mvexpand TimeGenerated, TotalRequests
   | serialize TotalRequests, TimeGenerated, TimeGeneratedFormatted=format_datetime(todatetime(TimeGenerated), 'yyyy-MM-dd [HH:mm:ss]')
   | project   TimeGeneratedFormatted, TotalRequests, PercentageChange= ((toreal(TotalRequests) - toreal(prev(TotalRequests,1)))/toreal(prev(TotalRequests,1)))*100
   | order by TimeGeneratedFormatted desc
   | where PercentageChange <= threshold   //Trigger's alert rule if matched.
   ```

1. 选择“运行”以测试查询。 如果过去一小时内请求总数下降了 25% 或更多，则会看到结果。
1. 若要基于上面的查询创建警报规则，请使用工具栏中可用的“+ 新建警报规则”选项。
1. 在“创建警报规则”页上，选择“条件名称”
1. 在“配置信号逻辑”页上，设置以下值，然后使用“完成”按钮保存更改。

   - 警报逻辑：将“结果数”设置为“大于”“0”。
   - 评估依据：为“时段(分钟)”选择“120”，为“频率(分钟)”选择“5”

   ![创建警报规则条件](./media/azure-monitor/alert-create-rule-condition.png)

创建警报后，转到“Log Analytics 工作区”，然后选择“警报”。 此页面显示在“时间范围”选项设置的持续时间内触发的所有警报。

### <a name="configure-action-groups"></a>配置操作组

Azure Monitor 和服务运行状况警报使用操作组来通知用户某个警报已触发。 可以包括发送语音呼叫、短信、电子邮件，或者触发各种类型的自动化操作。 请按照[在 Azure 门户中创建和管理器操作组](../azure-monitor/alerts/action-groups.md)指南操作

下面是警报通知电子邮件的示例。

![电子邮件通知](./media/azure-monitor/alert-email-notification.png)

## <a name="multiple-tenants"></a>多个租户

若要将多个 Azure AD B2C 租户日志集成到同一个 Log Analytics 工作区（或 Azure 存储帐户或事件中心），需要使用不同的“Msp 套餐名称”值进行单独部署。 请确保 Log Analytics 工作区与你在[创建或选择资源组](#1-create-or-choose-resource-group)中配置的工作区位于同一资源组中。

使用多个 Log Analytics 工作区时，请使用[跨工作区查询](../azure-monitor/logs/cross-workspace-query.md)创建跨多个工作区的查询。 例如，下面的查询从基于同一类别（例如身份验证）的不同租户执行两个审核日志的联接：

```kusto
workspace("AD-B2C-TENANT1").AuditLogs
| join  workspace("AD-B2C-TENANT2").AuditLogs
  on $left.Category== $right.Category
```

## <a name="change-the-data-retention-period"></a>更改数据保留期

Azure Monitor 日志用于调整和支持来自任何源的巨量数据的每日收集、索引和存储，这些源部署在企业或 Azure 中。 默认情况下，日志保留 30 天，但保留期可以延长到两年。 了解如何[使用 Azure Monitor 日志管理使用情况和成本](../azure-monitor/logs/manage-cost-storage.md)。 选择定价层后，可以[更改数据保留期](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)。

## <a name="next-steps"></a>后续步骤

- 在 Azure AD B2C [SIEM 库](https://aka.ms/b2csiem)中查找更多示例。

- 有关在 Azure Monitor 中添加和配置诊断设置的详细信息，请参阅[教程：从 Azure 资源收集和分析资源日志](../azure-monitor/essentials/monitor-azure-resource.md)。

- 有关将 Azure AD 日志流式传输到事件中心的信息，请参阅[教程：将 Azure Active Directory 日志流式传输到 Azure 事件中心](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)。
