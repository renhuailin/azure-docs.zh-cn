---
title: 将 Azure Policy 大规模部署到委托订阅
description: Azure Lighthouse 如何便于跨多个租户部署策略定义和策略分配。
ms.date: 06/13/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e45feb45da482aa960516abec8c339d61016c758
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128673571"
---
# <a name="deploy-azure-policy-to-delegated-subscriptions-at-scale"></a>将 Azure Policy 大规模部署到委托订阅

作为服务提供商，你可能已经将多个客户租户载入了 [Azure Lighthouse](../overview.md)。 Azure Lighthouse 允许服务提供商同时在多个租户之间大规模执行操作，从而提高管理任务的效率。

本主题说明如何按照 [Azure Policy](../../governance/policy/index.yml) 使用 PowerShell 命令在多个租户中部署策略定义和策略分配。 在此示例中，策略定义确保通过允许仅 HTTPS 流量来保护存储帐户。

> [!TIP]
> 尽管本主题中只是提到了服务提供商和客户，但[管理多个租户的企业](../concepts/enterprise.md)也可以使用相同的过程。

## <a name="use-azure-resource-graph-to-query-across-customer-tenants"></a>使用 Azure Resource Graph 在客户租户之间执行查询

你可以使用 [Azure Resource Graph](../../governance/resource-graph/overview.md) 在所管理的客户租户的所有订阅中执行查询。 在此示例中，我们将在这些订阅中识别当前不需要 HTTPS 流量的所有存储帐户。  

```powershell
$MspTenant = "insert your managing tenantId here"

$subs = Get-AzSubscription

$ManagedSubscriptions = Search-AzGraph -Query "ResourceContainers | where type == 'microsoft.resources/subscriptions' | where tenantId != '$($mspTenant)' | project name, subscriptionId, tenantId" -subscription $subs.subscriptionId

Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | project name, location, subscriptionId, tenantId, properties.supportsHttpsTrafficOnly" -subscription $ManagedSubscriptions.subscriptionId | convertto-json
```

## <a name="deploy-a-policy-across-multiple-customer-tenants"></a>跨多个客户租户部署策略

以下示例演示如何使用 [Azure 资源管理器模板](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json)跨多个客户租户中的委托订阅部署策略定义和策略分配。 此策略定义要求所有存储帐户都使用 HTTPS 流量，防止创建任何不符合要求的新存储帐户，并标记现有存储帐户，而不设置为不合规。

```powershell
Write-Output "In total, there are $($ManagedSubscriptions.Count) delegated customer subscriptions to be managed"

foreach ($ManagedSub in $ManagedSubscriptions)
{
    Select-AzSubscription -SubscriptionId $ManagedSub.subscriptionId

    New-AzSubscriptionDeployment -Name mgmt `
                     -Location eastus `
                     -TemplateUri "https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json" `
                     -AsJob
}
```

> [!NOTE]
> 虽然可以跨多个租户部署策略，但目前无法[查看这些租户中不合规资源的合规性详细信息](../../governance/policy/how-to/determine-non-compliance.md#compliance-details)。

## <a name="validate-the-policy-deployment"></a>验证策略部署

在部署了 Azure 资源管理器模板之后，可以通过尝试在其中一个委托订阅中创建 EnableHttpsTrafficOnly 设置为 false 的存储帐户，确认策略定义是否已成功应用。 由于策略分配，你应该无法创建此存储帐户。  

```powershell
New-AzStorageAccount -ResourceGroupName (New-AzResourceGroup -name policy-test -Location eastus -Force).ResourceGroupName `
                     -Name (get-random) `
                     -Location eastus `
                     -EnableHttpsTrafficOnly $false `
                     -SkuName Standard_LRS `
                     -Verbose                  
```

## <a name="clean-up-resources"></a>清理资源

完成后，删除部署所创建的策略定义和分配。

```powershell
foreach ($ManagedSub in $ManagedSubscriptions)
{
    select-azsubscription -subscriptionId $ManagedSub.subscriptionId

    Remove-AzSubscriptionDeployment -Name mgmt -AsJob

    $Assignment = Get-AzPolicyAssignment | where-object {$_.Name -like "enforce-https-storage-assignment"}

    if ([string]::IsNullOrEmpty($Assignment))
    {
        Write-Output "Nothing to clean up - we're done"
    }
    else
    {

    Remove-AzPolicyAssignment -Name 'enforce-https-storage-assignment' -Scope "/subscriptions/$($ManagedSub.subscriptionId)" -Verbose

    Write-Output "Deployment has been deleted - we're done"
    }
}
```

## <a name="next-steps"></a>后续步骤

- 了解 [Azure Policy](../../governance/policy/index.yml)。
- 了解[跨租户管理体验](../concepts/cross-tenant-management-experience.md)。
- 了解如何在委托订阅中[部署可以修正的策略](deploy-policy-remediation.md)。
