---
title: 在 Azure 自动化中管理角色权限和安全性
description: 本文介绍如何使用 Azure 基于角色的访问控制 (Azure RBAC)，这种访问控制可用于对 Azure 资源进行访问管理。
keywords: 自动化 rbac, 基于角色的访问控制, azure rbac
services: automation
ms.subservice: shared-capabilities
ms.date: 06/15/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4af5a6d105867df7d5c7a00f6fc47bd0032f4336
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122597409"
---
# <a name="manage-role-permissions-and-security"></a>管理角色权限和安全性

Azure 基于角色的访问控制 (Azure RBAC) 可用于对 Azure 资源进行访问管理。 使用 [Azure RBAC](../role-based-access-control/overview.md)，可以在团队中对职责进行分配，仅向用户、组和应用程序授予执行作业所需的访问权限。 可以使用 Azure 门户、Azure 命令行工具或 Azure 管理 API 将基于角色的访问权限授予用户。

## <a name="roles-in-automation-accounts"></a>自动化帐户中的角色

在 Azure 自动化中，访问权限是通过将相应的 Azure 角色分配给自动化帐户作用域的用户、组和应用程序来授予的。 以下是自动化帐户所支持的内置角色：

| **角色** | **说明** |
|:--- |:--- |
| “所有者” |“所有者”角色允许访问自动化帐户中的所有资源和操作，包括访问其他用户、组和应用程序以管理自动化帐户。 |
| 参与者 |“参与者”角色允许管理所有事项，修改其他用户对自动化帐户的访问权限除外。 |
| 读取器 |“读取者”角色允许查看自动化帐户中的所有资源，但不能进行任何更改。 |
| 自动化运算符 |自动化操作员角色允许针对某个自动化帐户中的所有 Runbook 查看 Runbook 名称和属性，以及为其创建和管理作业。 如果想要防止他人查看或修改自动化帐户资源（例如凭据资产和 Runbook），但仍允许所在组织的成员执行这些 Runbook，则可使用此角色。 |
|自动化作业操作员|自动化作业操作员角色允许针对某个自动化帐户中的所有 Runbook 创建和管理作业。|
|自动化 Runbook 操作员|自动化 Runbook 操作员角色允许查看某个 Runbook 的名称和属性。|
| Log Analytics 参与者 | Log Analytics 参与者可以读取所有监视数据并编辑监视设置。 编辑监视设置包括向 VM 添加 VM 扩展、读取存储帐户密钥以便能够从 Azure 存储配置日志收集、创建和配置自动化帐户、添加 Azure 自动化功能以及配置对所有 Azure 资源的 Azure 诊断。|
| Log Analytics 读者 | Log Analytics 读取者可以读取所有监视数据并编辑监视设置。 这包括查看 Azure 诊断在所有 Azure 资源上的配置。 |
| 监视参与者 | 监视参与者可以读取所有监视数据并更新监视设置。|
| 监视查阅者 | 监视读取者角色，可以读取所有监视数据。 |
| 用户访问管理员 |“用户访问管理员”角色允许管理用户对 Azure 自动化帐户的访问。 |

## <a name="role-permissions"></a>角色权限

下表描述授予每个角色的特定权限。 这可能包括授予权限的操作和限制权限的不操作。

### <a name="owner"></a>“所有者”

所有者可管理所有内容，包括访问权限。 下表显示了授予角色的权限：

|操作|说明|
|---|---|
|Microsoft.Automation/automationAccounts/|创建和管理所有类型的资源。|

### <a name="contributor"></a>参与者

参与者可管理访问权限以外的所有内容 下表显示了授予和拒绝角色的权限：

|**操作**  |**说明**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/|创建和管理所有类型的资源|
|**无操作**||
|Microsoft.Authorization/*/Delete| 删除角色和角色分配。       |
|Microsoft.Authorization/*/Write     |  创建角色和角色分配。       |
|Microsoft.Authorization/elevateAccess/Action    | 拒绝创建用户访问管理员。       |

### <a name="reader"></a>读取器

读取者可以查看自动化帐户中的所有资源，但不能进行任何更改。

|**操作**  |**说明**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/read|查看自动化帐户中的所有资源。 |

### <a name="automation-operator"></a>自动化运算符

自动化操作员能够针对某个自动化帐户中的所有 Runbook 创建和管理作业，以及读取 Runbook 名称和属性。

>[!NOTE]
>如果希望控制操作员对各个 runbook 的访问，请勿设置此角色。 请改为结合使用“自动化作业操作员”和“自动化 Runbook 操作员”角色 。

下表显示了授予角色的权限：

|**操作**  |**说明**  |
|---------|---------|
|Microsoft.Authorization/*/read|读取授权。|
|Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read|读取混合 Runbook 辅助角色资源。|
|Microsoft.Automation/automationAccounts/jobs/read|列出 runbook 的作业。|
|Microsoft.Automation/automationAccounts/jobs/resume/action|恢复已暂停的作业。|
|Microsoft.Automation/automationAccounts/jobs/stop/action|取消正在进行的作业。|
|Microsoft.Automation/automationAccounts/jobs/streams/read|读取作业流和输出。|
|Microsoft.Automation/automationAccounts/jobs/output/read|获取作业的输出。|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|暂停正在进行的作业。|
|Microsoft.Automation/automationAccounts/jobs/write|创建作业。|
|Microsoft.Automation/automationAccounts/jobSchedules/read|获取 Azure 自动化作业计划。|
|Microsoft.Automation/automationAccounts/jobSchedules/write|创建 Azure 自动化作业计划。|
|Microsoft.Automation/automationAccounts/linkedWorkspace/read|获取链接到自动化帐户的工作区。|
|Microsoft.Automation/automationAccounts/read|获取 Azure 自动化帐户。|
|Microsoft.Automation/automationAccounts/runbooks/read|获取 Azure 自动化 Runbook。|
|Microsoft.Automation/automationAccounts/schedules/read|获取 Azure 自动化计划资产。|
|Microsoft.Automation/automationAccounts/schedules/write|创建或更新 Azure 自动化计划资产。|
|Microsoft.Resources/subscriptions/resourceGroups/read      |读取角色和角色分配。         |
|Microsoft.Resources/deployments/*      |创建和管理资源组部署。         |
|Microsoft.Insights/alertRules/*      | 创建和管理警报规则。        |
|Microsoft.Support/* |创建和管理支持票证。|

### <a name="automation-job-operator"></a>自动化作业操作员

自动化作业操作员角色是在自动化帐户范围内授予的。 这将向操作员授予权限来为帐户中的所有 Runbook 创建和管理作业。 如果为作业操作员角色授予了对包含自动化帐户的资源组的读取权限，则该角色的成员能够启动 runbook。 但他们不能创建、编辑或删除 Runbook。

下表显示了授予角色的权限：

|**操作**  |**说明**  |
|---------|---------|
|Microsoft.Authorization/*/read|读取授权。|
|Microsoft.Automation/automationAccounts/jobs/read|列出 runbook 的作业。|
|Microsoft.Automation/automationAccounts/jobs/resume/action|恢复已暂停的作业。|
|Microsoft.Automation/automationAccounts/jobs/stop/action|取消正在进行的作业。|
|Microsoft.Automation/automationAccounts/jobs/streams/read|读取作业流和输出。|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|暂停正在进行的作业。|
|Microsoft.Automation/automationAccounts/jobs/write|创建作业。|
|Microsoft.Resources/subscriptions/resourceGroups/read      |  读取角色和角色分配。       |
|Microsoft.Resources/deployments/*      |创建和管理资源组部署。         |
|Microsoft.Insights/alertRules/*      | 创建和管理警报规则。        |
|Microsoft.Support/* |创建和管理支持票证。|

### <a name="automation-runbook-operator"></a>自动化 Runbook 操作员

自动化 Runbook 操作员角色在 Runbook 范围授予。 自动化 Runbook 操作员可以查看 Runbook 的名称和属性。 将此角色与“自动化作业操作员”角色组合使用时，也会使操作员能够为 Runbook 创建和管理作业。 下表显示了授予角色的权限：

|**操作**  |**说明**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/runbooks/read     | 列出 runbook。        |
|Microsoft.Authorization/*/read      | 读取授权。        |
|Microsoft.Resources/subscriptions/resourceGroups/read      |读取角色和角色分配。         |
|Microsoft.Resources/deployments/*      | 创建和管理资源组部署。         |
|Microsoft.Insights/alertRules/*      | 创建和管理警报规则。        |
|Microsoft.Support/*      | 创建和管理支持票证。        |

### <a name="log-analytics-contributor"></a>Log Analytics 参与者

Log Analytics 参与者可以读取所有监视数据并编辑监视设置。 编辑监视设置包括向 VM 添加 VM 扩展、读取存储帐户密钥以便能够从 Azure 存储配置日志收集、创建和配置自动化帐户、添加功能以及配置对所有 Azure 资源的 Azure 诊断。 下表显示了授予角色的权限：

|**操作**  |**说明**  |
|---------|---------|
|*/read|读取除密码外的所有类型的资源。|
|Microsoft.Automation/automationAccounts/*|管理自动化帐户。|
|Microsoft.ClassicCompute/virtualMachines/extensions/*|创建和管理虚拟机扩展。|
|Microsoft.ClassicStorage/storageAccounts/listKeys/action|列出经典存储帐户密钥。|
|Microsoft.Compute/virtualMachines/extensions/*|创建和管理经典虚拟机扩展。|
|Microsoft.Insights/alertRules/*|读取/写入/删除警报规则。|
|Microsoft.Insights/diagnosticSettings/*|读取/写入/删除诊断设置。|
|Microsoft.OperationalInsights/*|管理 Azure Monitor 日志。|
|Microsoft.OperationsManagement/*|管理工作区中的 Azure 自动化功能。|
|Microsoft.Resources/deployments/*|创建和管理资源组部署。|
|Microsoft.Resources/subscriptions/resourcegroups/deployments/*|创建和管理资源组部署。|
|Microsoft.Storage/storageAccounts/listKeys/action|列出存储帐户密钥。|
|Microsoft.Support/*|创建和管理支持票证。|

### <a name="log-analytics-reader"></a>Log Analytics 读者

Log Analytics 读者可以查看和搜索所有监视数据并查看监视设置，其中包括查看所有 Azure 资源上的 Azure 诊断的配置。 下表显示了授予或拒绝角色的权限：

|**操作**  |**说明**  |
|---------|---------|
|*/read|读取除密码外的所有类型的资源。|
|Microsoft.OperationalInsights/workspaces/analytics/query/action|管理 Azure Monitor 日志中的查询。|
|Microsoft.OperationalInsights/workspaces/search/action|搜索 Azure Monitor 日志数据。|
|Microsoft.Support/*|创建和管理支持票证。|
|**无操作**| |
|Microsoft.OperationalInsights/workspaces/sharedKeys/read|无法读取共享访问密钥。|

### <a name="monitoring-contributor"></a>监视参与者

监视参与者可以读取所有监视数据并更新监视设置。 下表显示了授予角色的权限：

|**操作**  |**说明**  |
|---------|---------|
|*/read|读取除密码外的所有类型的资源。|
|Microsoft.AlertsManagement/alerts/*|管理警报。|
|Microsoft.AlertsManagement/alertsSummary/*|管理警报仪表板。|
|Microsoft.Insights/AlertRules/*|管理警报规则。|
|Microsoft.Insights/components/*|管理 Application Insights 组件。|
|Microsoft.Insights/DiagnosticSettings/*|管理诊断设置。|
|Microsoft.Insights/eventtypes/*|列出订阅中的活动日志事件（管理事件）。 此权限适用于以编程方式和通过门户访问活动日志。|
|Microsoft.Insights/LogDefinitions/*|此权限对于需要通过门户访问活动日志的用户是必需的。 列出活动日志中的日志类别。|
|Microsoft.Insights/MetricDefinitions/*|读取指标定义（资源的可用指标类型的列表）。|
|Microsoft.Insights/Metrics/*|读取资源的指标。|
|Microsoft.Insights/Register/Action|注册 Microsoft.Insights 提供程序。|
|Microsoft.Insights/webtests/*|管理 Application Insights Web 测试。|
|Microsoft.OperationalInsights/workspaces/intelligencepacks/*|管理 Azure Monitor 日志解决方案包。|
|Microsoft.OperationalInsights/workspaces/savedSearches/*|管理 Azure Monitor 日志保存的搜索。|
|Microsoft.OperationalInsights/workspaces/search/action|搜索 Log Analytics 工作区。|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|列出 Log Analytics 工作区的键。|
|Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*|管理 Azure Monitor 日志存储见解配置。|
|Microsoft.Support/*|创建和管理支持票证。|
|Microsoft.WorkloadMonitor/workloads/*|管理工作负荷。|

### <a name="monitoring-reader"></a>监视查阅者

监视读取者可以读取所有监视数据。 下表显示了授予角色的权限：

|**操作**  |**说明**  |
|---------|---------|
|*/read|读取除密码外的所有类型的资源。|
|Microsoft.OperationalInsights/workspaces/search/action|搜索 Log Analytics 工作区。|
|Microsoft.Support/*|创建和管理支持票证|

### <a name="user-access-administrator"></a>用户访问管理员

用户访问管理员可管理 Azure 资源的用户访问权限。 下表显示了授予角色的权限：

|**操作**  |**说明**  |
|---------|---------|
|*/read|读取所有资源。|
|Microsoft.Authorization/*|管理授权|
|Microsoft.Support/*|创建和管理支持票证|

## <a name="feature-setup-permissions"></a>功能设置权限

以下部分介绍了启用更新管理以及更改跟踪和库存功能所需的最低必需权限。

### <a name="permissions-for-enabling-update-management-and-change-tracking-and-inventory-from-a-vm"></a>从 VM 启用更新管理以及更改跟踪和库存的权限

|**Action**  |**权限**  |**最小范围**  |
|---------|---------|---------|
|写入新部署      | Microsoft.Resources/deployments/*          |订阅          |
|写入新资源组      | Microsoft.Resources/subscriptions/resourceGroups/write        | 订阅          |
|创建新的默认工作区      | Microsoft.OperationalInsights/workspaces/write         | 资源组         |
|创建新帐户      |  Microsoft.Automation/automationAccounts/write        |资源组         |
|链接工作区和帐户      |Microsoft.OperationalInsights/workspaces/write</br>Microsoft.Automation/automationAccounts/read|工作区</br>自动化帐户
|创建 MMA 扩展      | Microsoft.Compute/virtualMachines/write         | 虚拟机         |
|创建保存的搜索      | Microsoft.OperationalInsights/workspaces/write          | 工作区         |
|创建范围配置      | Microsoft.OperationalInsights/workspaces/write          | 工作区         |
|加入状态检查 - 读取工作区      | Microsoft.OperationalInsights/workspaces/read         | 工作区         |
|加入状态检查 - 读取帐户的链接工作区属性     | Microsoft.Automation/automationAccounts/read      | 自动化帐户        |
|加入状态检查 - 读取解决方案      | Microsoft.OperationalInsights/workspaces/intelligencepacks/read          | 解决方案         |
|加入状态检查 - 读取 VM      | Microsoft.Compute/virtualMachines/read         | 虚拟机         |
|加入状态检查 - 读取帐户      | Microsoft.Automation/automationAccounts/read  |  自动化帐户   |
| VM 的加入工作区检查<sup>1</sup>       | Microsoft.OperationalInsights/workspaces/read         | 订阅         |
| 注册 Log Analytics 提供程序 |Microsoft.Insights/register/action | 订阅|

<sup>1</sup> 通过 VM 门户体验启用功能需要此权限。

### <a name="permissions-for-enabling-update-management-and-change-tracking-and-inventory-from-an-automation-account"></a>从自动化帐户启用更新管理以及更改跟踪和库存的权限

|**Action**  |**权限** |**最小范围**  |
|---------|---------|---------|
|新建部署     | Microsoft.Resources/deployments/*        | 订阅         |
|新建资源组     | Microsoft.Resources/subscriptions/resourceGroups/write         | 订阅        |
|AutomationOnboarding 边栏选项卡 - 创建新工作区     |Microsoft.OperationalInsights/workspaces/write           | 资源组        |
|AutomationOnboarding 边栏选项卡 - 读取链接的工作区     | Microsoft.Automation/automationAccounts/read        | 自动化帐户       |
|AutomationOnboarding 边栏选项卡 - 读取解决方案     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read         | 解决方案        |
|AutomationOnboarding 边栏选项卡 - 读取工作区     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read        | 工作区        |
|创建工作区和帐户的链接     | Microsoft.OperationalInsights/workspaces/write        | 工作区        |
|写入 shoebox 帐户      | Microsoft.Automation/automationAccounts/write        | 帐户        |
|创建/编辑保存的搜索     | Microsoft.OperationalInsights/workspaces/write        | 工作区        |
|创建/编辑范围配置     | Microsoft.OperationalInsights/workspaces/write        | 工作区        |
| 注册 Log Analytics 提供程序 |Microsoft.Insights/register/action | 订阅|
|**步骤 2 - 启用多个 VM**     |         |         |
|VMOnboarding 边栏选项卡 - 创建 MMA 扩展     | Microsoft.Compute/virtualMachines/write           | 虚拟机        |
|创建/编辑保存的搜索     | Microsoft.OperationalInsights/workspaces/write           | 工作区        |
|创建/编辑范围配置  | Microsoft.OperationalInsights/workspaces/write   | 工作区|

## <a name="custom-azure-automation-contributor-role"></a>自定义 Azure 自动化参与者角色

Microsoft 打算从 Log Analytics 参与者角色中删除自动化帐户权限。 目前，上述内置 [Log Analytics 参与者](#log-analytics-contributor)角色可将特权提升至订阅[参与者](./../role-based-access-control/built-in-roles.md#contributor)角色。 由于最初为自动化帐户的运行方式帐户配置了订阅中的参与者权限，因此攻击者可能会利用该权限创建新的 Runbook，并以订阅中的参与者身份执行代码。

由于存在这种安全风险，我们建议不要使用 Log Analytics 参与者角色来执行自动化作业。 而是应该创建 Azure 自动化参与者自定义角色，并将其用于与自动化帐户相关的操作。 可执行以下步骤来创建此自定义角色。

### <a name="create-using-the-azure-portal"></a>使用 Azure 门户进行创建

在 Azure 门户中执行以下步骤，以创建 Azure 自动化自定义角色。 若要了解详细信息，请参阅 [Azure 自定义角色](./../role-based-access-control/custom-roles.md)。

1. 将以下 JSON 语法复制并粘贴到某个文件中。 将该文件保存在本地计算机或 Azure 存储帐户中。 在 JSON 文件中，请将 assignableScopes 属性的值替换为订阅 GUID。

   ```json
   {
    "properties": {
        "roleName": "Automation Account Contributor (Custom)",
        "description": "Allows access to manage Azure Automation and its resources",
        "assignableScopes": [
            "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXX"
        ],
        "permissions": [
            {
                "actions": [
                    "Microsoft.Authorization/*/read",
                    "Microsoft.Insights/alertRules/*",
                    "Microsoft.Insights/metrics/read",
                    "Microsoft.Insights/diagnosticSettings/*",
                    "Microsoft.Resources/deployments/*",
                    "Microsoft.Resources/subscriptions/resourceGroups/read",
                    "Microsoft.Automation/automationAccounts/*",
                    "Microsoft.Support/*"
                ],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ]
      }
   }
   ```

1. 按照[使用 Azure 门户创建或更新 Azure 自定义角色](../role-based-access-control/custom-roles-portal.md#start-from-json)中所述完成剩余步骤。 对于[步骤 3：基本信息](../role-based-access-control/custom-roles-portal.md#step-3-basics)，请注意以下几点：

    -  在“自定义角色名称”字段中，输入“自动化帐户参与者（自定义）”或者符合命名标准的名称 。
    - 对于“基线权限”，请选择“从 JSON 启动” 。 然后选择前面保存的自定义 JSON 文件。

1. 完成剩余步骤，然后查看并创建自定义角色。 自定义角色可能需要几分钟的时间才能显示在每个位置。

### <a name="create-using-powershell"></a>使用 PowerShell 创建

在 PowerShell 中执行以下步骤，以创建 Azure 自动化自定义角色。 若要了解详细信息，请参阅 [Azure 自定义角色](./../role-based-access-control/custom-roles.md)。

1. 将以下 JSON 语法复制并粘贴到某个文件中。 将该文件保存在本地计算机或 Azure 存储帐户中。 在 JSON 文件中，请将 AssignableScopes 属性的值替换为订阅 GUID。

    ```json
    { 
        "Name": "Automation account Contributor (custom)",
        "Id": "",
        "IsCustom": true,
        "Description": "Allows access to manage Azure Automation and its resources",
        "Actions": [
            "Microsoft.Authorization/*/read",
            "Microsoft.Insights/alertRules/*",
            "Microsoft.Insights/metrics/read",
            "Microsoft.Insights/diagnosticSettings/*",
            "Microsoft.Resources/deployments/*",
            "Microsoft.Resources/subscriptions/resourceGroups/read",
            "Microsoft.Automation/automationAccounts/*",
            "Microsoft.Support/*"
        ],
        "NotActions": [],
        "AssignableScopes": [
            "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXX"
        ] 
    } 
    ```

1. 按照[使用 Azure PowerShell 创建或更新 Azure 自定义角色](./../role-based-access-control/custom-roles-powershell.md#create-a-custom-role-with-json-template)中所述完成剩余步骤。 自定义角色可能需要几分钟的时间才能显示在每个位置。

## <a name="update-management-permissions"></a>更新管理权限

更新管理可用于评估和安排同一 Azure Active Directory (Azure AD) 租户中的或使用 Azure Lighthouse 跨租户的多个订阅中的计算机的更新部署。 下表列出了管理更新部署所需的权限。

|**资源** |**角色** |**范围** |
|---------|---------|---------|
|自动化帐户 |[自定义 Azure 自动化参与者角色](#custom-azure-automation-contributor-role) |自动化帐户 |
|自动化帐户 |虚拟机参与者  |帐户的资源组  |
|Log Analytics 工作区  Log Analytics 参与者|Log Analytics 工作区 |
|Log Analytics 工作区 |Log Analytics 读者|订阅|
|解决方案 |Log Analytics 参与者 |解决方案|
|虚拟机 |虚拟机参与者 |虚拟机 |
|虚拟机上的操作 | | |
|查看更新计划执行的历史记录（[软件更新配置计算机运行](/rest/api/automation/softwareupdateconfigurationmachineruns)） |读取器 |自动化帐户 |
|虚拟机上的操作 |**权限** | |
|创建更新计划（[软件更新配置](/rest/api/automation/softwareupdateconfigurations)） |Microsoft.Compute/virtualMachines/write |用于静态 VM 列表和资源组 |
|创建更新计划（[软件更新配置](/rest/api/automation/softwareupdateconfigurations)） |Microsoft.OperationalInsights/workspaces/analytics/query/action |用于使用非 Azure 动态列表时的工作区资源 ID。|

## <a name="configure-azure-rbac-for-your-automation-account"></a>为自动化帐户配置 Azure RBAC

以下部分演示如何通过 [Azure 门户](#configure-azure-rbac-using-the-azure-portal)和 [PowerShell](#configure-azure-rbac-using-powershell) 在自动化帐户上配置 Azure RBAC。

### <a name="configure-azure-rbac-using-the-azure-portal"></a>使用 Azure 门户配置 Azure RBAC

1. 登录到 [Azure 门户](https://portal.azure.com/)，然后从“自动化帐户”页打开自动化帐户。
2. 单击“访问控制(IAM)”打开“访问控制(IAM)”页。 可以使用此页添加新的用户、组和应用程序，以便管理自动化帐户并查看可以为自动化帐户配置的现有角色。
3. 单击“角色分配”选项卡。

   ![访问按钮](media/automation-role-based-access-control/automation-01-access-button.png)

#### <a name="add-a-new-user-and-assign-a-role"></a>添加新用户并分配角色

1. 在“访问控制(IAM)”页中，单击“+ 添加角色分配”。 此操作会打开“添加角色分配“页，可以在其中添加用户、组或应用程序，并分配相应的角色。

2. 从可用角色列表中选择一个角色。 可以选择自动化帐户所支持的任何可用的内置角色，或者定义的任何自定义角色。

3. 在“选择”字段中键入要对其授予权限的用户的名称。 从列表中选择用户，然后单击“保存”。

   ![添加用户](media/automation-role-based-access-control/automation-04-add-users.png)

   现在，应当会看到该用户已添加到“用户”页并且分配有所选角色。

   ![列出用户](media/automation-role-based-access-control/automation-05-list-users.png)

   也可以通过“角色”页向用户分配角色。

4. 单击“访问控制(IAM)”页中的“角色”打开“角色”页。 可以查看角色的名称以及分配给该角色的用户和组的数目。

    ![从用户页分配角色](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > 只能在自动化帐户范围内设置基于角色的访问控制，不能在自动化帐户下的任何资源上设置。

#### <a name="remove-a-user"></a>删除用户

可以删除不管理自动化帐户或不再为组织工作的用户的访问权限。 下面是删除用户的步骤：

1. 在“访问控制(IAM)”页中，选择要删除的用户，然后单击“删除”。
2. 单击“分配详细信息”页中的“删除”按钮。
3. 单击“是”以确认删除  。

   ![删除用户](media/automation-role-based-access-control/automation-08-remove-users.png)

### <a name="configure-azure-rbac-using-powershell"></a>使用 PowerShell 配置 Azure RBAC

还可以使用以下 [Azure PowerShell cmdlet](../role-based-access-control/role-assignments-powershell.md) 为自动化帐户配置基于角色的访问权限：

[Get-AzRoleDefinition](/powershell/module/Az.Resources/Get-AzRoleDefinition) 列出 Azure Active Directory 中提供的所有 Azure 角色。 可以将此 cmdlet 与 `Name` 参数一起使用来列出特定角色可以执行的所有操作。

```azurepowershell-interactive
Get-AzRoleDefinition -Name 'Automation Operator'
```

下面是示例输出：

```azurepowershell
Name             : Automation Operator
Id               : d3881f73-407a-4167-8283-e981cbba0404
IsCustom         : False
Description      : Automation Operators are able to start, stop, suspend, and resume jobs
Actions          : {Microsoft.Authorization/*/read, Microsoft.Automation/automationAccounts/jobs/read, Microsoft.Automation/automationAccounts/jobs/resume/action,
                   Microsoft.Automation/automationAccounts/jobs/stop/action...}
NotActions       : {}
AssignableScopes : {/}
```

[Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) 列出了指定范围内的 Azure 角色分配。 在没有任何参数的情况下，此 cmdlet 返回在订阅下进行的所有角色分配。 使用 `ExpandPrincipalGroups` 参数可列出针对指定用户和该用户所在组的访问权限分配。

**示例：** 使用以下 cmdlet 列出自动化帐户中的所有用户及其角色。

```azurepowershell-interactive
Get-AzRoleAssignment -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

下面是示例输出：

```powershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/cc594d39-ac10-46c4-9505-f182a355c41f
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : 15f26a47-812d-489a-8197-3d4853558347
ObjectType         : User
```

使用 [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) 为特定范围内的用户、组和应用程序分配访问权限。

**示例：** 使用以下命令为“自动化帐户”范围中的用户分配“自动化操作员”角色。

```azurepowershell-interactive
New-AzRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName 'Automation operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

下面是示例输出：

```azurepowershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/25377770-561e-4496-8b4f-7cba1d6fa346
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : f5ecbe87-1181-43d2-88d5-a8f5e9d8014e
ObjectType         : User
```

使用 [Remove-AzRoleAssignment](/powershell/module/Az.Resources/Remove-AzRoleAssignment) 从特定范围中删除指定用户、组或应用程序的访问权限。

**示例：** 使用以下命令从自动化帐户范围的自动化操作员角色中删除用户。

```azurepowershell-interactive
Remove-AzRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

在前面的示例中，将 `sign-in ID of a user you wish to remove`、`SubscriptionID`、`Resource Group Name` 和 `Automation account name` 替换为帐户详细信息。 出现提示时选择“是”以在继续删除用户角色分配前确认。

### <a name="user-experience-for-automation-operator-role---automation-account"></a>自动化操作员角色的用户体验 - 自动化帐户

在自动化帐户范围内分配了自动化操作员角色的用户，在查看分配到的自动化帐户时，只能查看在自动化帐户中创建的 Runbook、Runbook 作业和计划的列表。 此用户无法查看这些项的定义。 该用户可以启动、停止、暂停、恢复或计划 Runbook 作业。 但是，此用户无法访问其他自动化资源，例如配置、混合 Runbook 辅助角色组或 DSC 节点。

![对资源无访问权限](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

## <a name="configure-azure-rbac-for-runbooks"></a>为 runbook 配置 Azure RBAC

通过 Azure 自动化，可以将 Azure 角色分配给特定的 runbook。 若要执行此操作，运行以下脚本将用户添加到特定 Runbook。 自动化帐户管理员或租户管理员可以运行此脚本。

```azurepowershell-interactive
$rgName = "<Resource Group Name>" # Resource Group name for the Automation account
$automationAccountName ="<Automation account name>" # Name of the Automation account
$rbName = "<Name of Runbook>" # Name of the runbook
$userId = "<User ObjectId>" # Azure Active Directory (AAD) user's ObjectId from the directory

# Gets the Automation account resource
$aa = Get-AzResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts" -ResourceName $automationAccountName

# Get the Runbook resource
$rb = Get-AzResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts/runbooks" -ResourceName "$rbName"

# The Automation Job Operator role only needs to be run once per user.
New-AzRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Job Operator" -Scope $aa.ResourceId

# Adds the user to the Automation Runbook Operator role to the Runbook scope
New-AzRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Runbook Operator" -Scope $rb.ResourceId
```

运行脚本后，用户就可以登录到 Azure 门户并选择“所有资源”。 在列表中，用户可以看到以自动化 Runbook 操作员身份添加的 Runbook。

![门户中的 Runbook Azure RBAC](./media/automation-role-based-access-control/runbook-rbac.png)

### <a name="user-experience-for-automation-operator-role---runbook"></a>自动化操作员角色的用户体验 - Runbook

在 Runbook 范围内分配了自动化操作员角色的用户，在查看分配到的 Runbook 时，只能启动 Runbook 和查看 Runbook 作业。

![仅有权启动](media/automation-role-based-access-control/automation-only-start.png)

## <a name="next-steps"></a>后续步骤

* 若要详细了解使用 PowerShell 的 Azure RBAC，请参阅[使用 Azure PowerShell 添加或删除 Azure 角色分配](../role-based-access-control/role-assignments-powershell.md)。
* 有关 Runbook 类型的详细信息，请参阅 [Azure 自动化 Runbook 类型](automation-runbook-types.md)。
* 若要启动 Runbook，请参阅[在 Azure 自动化中启动 Runbook](start-runbooks.md)。
