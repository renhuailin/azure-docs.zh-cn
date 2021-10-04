---
title: 监视管理租户中的委托更改
description: 了解如何监视所有针对管理租户的 Azure Lighthouse 委托活动。
ms.date: 09/08/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7eb38ac8ac7f86fd179663fe7bfb3aa1fb4e8830
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124819595"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>监视管理租户中的委托更改

作为服务提供商，你可能想要知道客户订阅或资源组何时已通过 [Azure Lighthouse](../overview.md) 委托到你的租户，或者以前何时删除了委托的资源。

在管理租户中，[Azure 活动日志](../../azure-monitor/essentials/platform-logs-overview.md)将会跟踪租户级别的委托活动。 记录的此活动包括任何在客户租户中添加或删除的委托。

本主题介绍监视（所有客户）对你的租户进行的委托活动所需的权限。 其中还包含一个示例脚本，用于演示一种查询和报告此数据的方法。

> [!IMPORTANT]
> 所有这些步骤必须在管理租户中执行，而不能在任何客户租户中执行。
>
> 尽管本主题中只是提到了服务提供商和客户，但[管理多个租户的企业](../concepts/enterprise.md)也可以使用相同的过程。

## <a name="enable-access-to-tenant-level-data"></a>启用对租户级数据的访问

若要访问租户级别的活动日志数据，必须为某个帐户分配根范围 (/) 的[监视读取者](../../role-based-access-control/built-in-roles.md#monitoring-reader) Azure 内置角色。 这种分配必须由具有“全局管理员”角色（带有其他提升的访问权限）的用户执行。

### <a name="elevate-access-for-a-global-administrator-account"></a>提升全局管理员帐户的访问权限

若要在根范围 (/) 分配角色，需要具有“全局管理员”角色，并且该角色带有提升的访问权限。 仅当需要进行角色分配时才应添加此提升的访问权限，完成分配后应删除此访问权限。

有关添加和删除提升的访问权限的详细说明，请参阅[提升访问权限以管理所有 Azure 订阅和管理组](../../role-based-access-control/elevate-access-global-admin.md)。

提升访问权限后，你的帐户将在 Azure 中具有根范围的“用户访问管理员”角色。 此角色分配可让你查看所有资源，在目录中的任何订阅或管理组内分配访问权限，以及在根范围进行角色分配。

### <a name="assign-the-monitoring-reader-role-at-root-scope"></a>在根范围分配“监视读取者”角色

提升自己的访问权限后，可以向帐户分配适当的权限，使它可以查询租户级别的活动日志数据。 需要在管理租户的根范围为此帐户分配[监视读取者](../../role-based-access-control/built-in-roles.md#monitoring-reader) Azure 内置角色。

> [!IMPORTANT]
> 在根范围授予角色分配意味着相同的权限将应用于租户中的每个资源。 由于此访问级别的范围较广泛，因此建议[将此角色分配给服务主体帐户，并使用该帐户来查询数据](#use-a-service-principal-account-to-query-the-activity-log)。
> 
> 还可以向单个用户或用户组分配根范围的“监视读取者”角色，使他们可以[直接在 Azure 门户中查看委托信息](#view-delegation-changes-in-the-azure-portal)。 如果这样做，请注意，此访问级别的范围较广泛，应限制为分配给尽量少的用户。

使用以下方法之一来进行根范围的分配。

#### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

New-AzRoleAssignment -SignInName <yourLoginName> -Scope "/" -RoleDefinitionName "Monitoring Reader"  -ObjectId <objectId> 
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az role assignment create --assignee 00000000-0000-0000-0000-000000000000 --role "Monitoring Reader" --scope "/"
```

### <a name="remove-elevated-access-for-the-global-administrator-account"></a>删除全局管理员帐户的提升访问权限

将根范围的“监视读取者”角色分配给所需的帐户后，请务必将全局管理员帐户的[提升访问权限删除](../../role-based-access-control/elevate-access-global-admin.md#remove-elevated-access)，因为不再需要此访问级别。

## <a name="view-delegation-changes-in-the-azure-portal"></a>在 Azure 门户中查看委托更改

分配有根范围的“监视读取者”角色的用户可以直接在 Azure 门户中查看委托更改。

1. 导航到“我的客户”页，然后从左侧导航菜单中选择“活动日志”。 
1. 确保在屏幕顶部附近的筛选器中选择“目录活动”。

此时会显示委托更改列表。 可以选择“编辑列”来显示或隐藏“状态”、“事件类别”、“时间”、“时间戳”、“订阅”、“事件发起者”、“资源组”、“资源类型”和“资源”值。         

:::image type="content" source="../media/delegation-activity-portal.jpg" alt-text="Azure 门户中的委托更改的屏幕截图。":::

## <a name="use-a-service-principal-account-to-query-the-activity-log"></a>使用服务主体帐户查询活动日志

由于根范围的“监视读取者”角色的访问级别范围比较广泛，因此建议将该角色分配给服务主体帐户，并使用该帐户通过以下脚本查询数据。

> [!IMPORTANT]
> 目前，具有大量委托活动的租户在查询此数据时可能会遇到错误。

使用服务主体帐户查询活动日志时，建议采用以下最佳做法：

- [创建一个新的服务主体帐户](../../active-directory/develop/howto-create-service-principal-portal.md)专门用于此功能，而不是将此角色分配给用于其他自动化功能的现有服务主体。
- 确保此服务主体无权访问任何委托的客户资源。
- [使用证书进行身份验证](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options)并[将证书安全存储在 Azure 密钥保管库中](../../key-vault/general/security-features.md)。
- 限制有权代表服务主体执行操作的用户。

创建对管理租户的根范围拥有“监视读取者”访问权限的新服务主体帐户后，可以使用该帐户来查询和报告租户中的委托活动。

[此 Azure PowerShell 脚本](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes)可用于查询过去 1 天的活动，并报告任何已添加或删除的委托（或不成功的委托尝试）。 它会查询[租户活动日志](/rest/api/monitor/TenantActivityLogs/List)数据，然后构造以下值来报告已添加或删除的委托：

- **DelegatedResourceId**：委托的订阅或资源组的 ID
- **CustomerTenantId**：客户租户 ID
- **CustomerSubscriptionId**：委托的订阅 ID，或包含委托的资源组的订阅 ID
- **CustomerDelegationStatus**：委托的资源的状态更改（成功或失败）
- **EventTimeStamp**：记录委托更改的日期和时间

查询此数据时，请记住：

- 如果在单个部署中委托了多个资源组，将为每个资源组返回与之对应的条目。
- 对前一委托所做的更改（例如更新权限结构）将记录为一个已添加的委托。
- 如前所述，帐户必须具有根范围 (/) 的“监视读取者”Azure 内置角色才能访问此租户级别的数据。
- 你可以在自己的工作流和报告中使用此数据。 例如，可以使用 [HTTP 数据收集器 API（公共预览版）](../../azure-monitor/logs/data-collector-api.md)将数据从 REST API 客户端记录到 Azure Monitor，然后使用[操作组](../../azure-monitor/alerts/action-groups.md)创建通知或警报。

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Azure Lighthouse: Query Tenant Activity Log for registered/unregistered delegations for the last 1 day

$GetDate = (Get-Date).AddDays((-1))

$dateFormatForQuery = $GetDate.ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ssZ")

# Getting Azure context for the API call
$currentContext = Get-AzContext

# Fetching new token
$azureRmProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = [Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient]::new($azureRmProfile)
$token = $profileClient.AcquireAccessToken($currentContext.Tenant.Id)

$listOperations = @{
    Uri     = "https://management.azure.com/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&`$filter=eventTimestamp ge '$($dateFormatForQuery)'"
    Headers = @{
        Authorization  = "Bearer $($token.AccessToken)"
        'Content-Type' = 'application/json'
    }
    Method  = 'GET'
}
$list = Invoke-RestMethod @listOperations

# First link can be empty - and point to a next link (or potentially multiple pages)
# While you get more data - continue fetching and add result
while($list.nextLink){
    $list2 = Invoke-RestMethod $list.nextLink -Headers $listOperations.Headers -Method Get
    $data+=$list2.value;
    $list.nextLink = $list2.nextlink;
}

$showOperations = $data;

if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/register/action") {
    $registerOutputs = $showOperations | Where-Object -FilterScript { $_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/register/action" }
    foreach ($registerOutput in $registerOutputs) {
        $eventDescription = $registerOutput.description | ConvertFrom-Json;
    $registerOutputdata = [pscustomobject]@{
        Event                    = "An Azure customer has registered delegated resources to your Azure tenant";
        DelegatedResourceId      = $eventDescription.delegationResourceId; 
        CustomerTenantId         = $eventDescription.subscriptionTenantId;
        CustomerSubscriptionId   = $eventDescription.subscriptionId;
        CustomerDelegationStatus = $registerOutput.status.value;
        EventTimeStamp           = $registerOutput.eventTimestamp;
        }
        $registerOutputdata | Format-List
    }
}
if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/unregister/action") {
    $unregisterOutputs = $showOperations | Where-Object -FilterScript { $_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/unregister/action" }
    foreach ($unregisterOutput in $unregisterOutputs) {
        $eventDescription = $registerOutput.description | ConvertFrom-Json;
    $unregisterOutputdata = [pscustomobject]@{
        Event                    = "An Azure customer has unregistered delegated resources from your Azure tenant";
        DelegatedResourceId      = $eventDescription.delegationResourceId;
        CustomerTenantId         = $eventDescription.subscriptionTenantId;
        CustomerSubscriptionId   = $eventDescription.subscriptionId;
        CustomerDelegationStatus = $unregisterOutput.status.value;
        EventTimeStamp           = $unregisterOutput.eventTimestamp;
        }
        $unregisterOutputdata | Format-List
    }
}
else {
    Write-Output "No new delegation events for tenant: $($currentContext.Tenant.TenantId)"
}
```

## <a name="next-steps"></a>后续步骤

- 了解如何[将客户加入到 Azure Lighthouse](onboard-customer.md)。
- 了解 [Azure Monitor](../../azure-monitor/index.yml) 和 [Azure 活动日志](../../azure-monitor/essentials/platform-logs-overview.md)。
- 查看[按域提供的活动日志](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain)示例工作簿，了解如何使用一个用于按域名筛选日志的选项显示各个订阅的 Azure 活动日志。
