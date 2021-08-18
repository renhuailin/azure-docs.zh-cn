---
title: 将资源移动到新的订阅或资源组
description: 使用 Azure Resource Manager 将资源移到新的资源组或订阅。
ms.topic: conceptual
ms.date: 06/03/2021
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: ec23b4306f088328bfb72f3cf9071a70f8eb2307
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2021
ms.locfileid: "113586772"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>将资源移到新的资源组或订阅

本文说明了如何将 Azure 资源移动到另一 Azure 订阅，或移动到同一订阅下的另一资源组。 可以使用 Azure 门户、Azure PowerShell、Azure CLI 或 REST API 移动资源。

在移动操作过程中，源组和目标组都会锁定。 在完成移动之前，将阻止对资源组执行写入和删除操作。 此锁意味着将无法添加、更新或删除资源组中的资源。 这并不意味着资源已被冻结。 例如，如果将 Azure SQL 逻辑服务器及其数据库移到新的资源组或订阅，则使用这些数据库的应用程序不会受停机影响。 它们仍可读取和写入数据库。 锁定时间最长可达四小时，但大多数移动所需的完成时间要少得多。

移动资源只会将其移到新的资源组或订阅。 但不会更改该资源的位置。

## <a name="changed-resource-id"></a>更改的资源 ID

移动资源时，会更改其资源 ID。 资源 ID 的标准格式为 `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}`。 将资源移到新的资源组或订阅时，会更改该路径中的一个或多个值。

如果在任意位置使用资源 ID，则需要更改该值。 例如，如果门户中有引用资源 ID 的[自定义仪表板](../../azure-portal/quickstart-portal-dashboard-azure-cli.md)，则需要更新该值。 查找需要针对新资源 ID 进行更新的任何脚本或模板。

## <a name="checklist-before-moving-resources"></a>移动资源前需查看的清单

移动资源之前需执行的一些重要步骤。 验证这些条件可以避免错误。

1. 要移动的资源必须支持移动操作。 有关支持移动的资源列表，请参阅[资源的移动操作支持](move-support-resources.md)。

1. 某些服务在移动资源时有特定的限制或要求。 如果要移动以下任何服务，请在移动之前查看该指南。

   * 如果使用 Azure Stack Hub，则无法在组之间移动资源。
   * [应用程序服务移动指南](./move-limitations/app-service-move-limitations.md)
   * [Azure DevOps Services 移动指南](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [经典部署模型移动指南](./move-limitations/classic-model-move-limitations.md) - 经典计算、经典存储、经典虚拟网络和云服务
   * [网络移动指南](./move-limitations/networking-move-limitations.md)
   * [恢复服务移动指南](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [虚拟机移动指南](./move-limitations/virtual-machines-move-limitations.md)
   * 若要将 Azure 订阅移到新管理组，请参阅[移动订阅](../../governance/management-groups/manage.md#move-subscriptions)。

1. 如果移动的资源具有直接分配给该资源（或子资源）的 Azure 角色，则该角色分配不会移动，将会变成孤立状态。 移动后必须重新创建角色分配。 最终，孤立角色分配会被自动删除，但我们建议你在进行移动之前先删除角色分配。

    若要了解如何管理角色分配，请参阅[列出 Azure 角色分配](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope)和[分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。

1. 源订阅和目标订阅必须处于活动状态。 如果在启用已禁用的帐户时遇到问题，请[创建 Azure 支持请求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。 选择“订阅管理”  作为问题类型。

1. 源订阅与目标订阅必须在同一个 [Azure Active Directory 租户](../../active-directory/develop/quickstart-create-new-tenant.md)中。 若要检查这两个订阅是否具有相同的租户 ID，请使用 Azure PowerShell 或 Azure CLI。

   对于 Azure PowerShell，请使用：

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   对于 Azure CLI，请使用：

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   如果源订阅和目标订阅的租户 ID 不相同，可使用以下方法协调租户 ID：

   * [将 Azure 订阅所有权转让给其他帐户](../../cost-management-billing/manage/billing-subscription-transfer.md)
   * [如何将 Azure 订阅关联或添加到 Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. 必须针对要移动的资源的资源提供程序注册目标订阅。 否则，会收到错误，指明 **未针对资源类型注册订阅**。 将资源移到新的订阅时，可能会看到此错误，但该订阅从未配合该资源类型使用。

   对于 PowerShell，请使用以下命令来获取注册状态：

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   若要注册资源提供程序，请使用：

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   对于 Azure CLI，请使用以下命令来获取注册状态：

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   若要注册资源提供程序，请使用：

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. 移动资源的帐户至少需要具备下列权限：

   * 源资源组上的 Microsoft.Resources/subscriptions/resourceGroups/moveResources/action  权限。
   * 目标资源组上的 Microsoft.Resources/subscriptions/resourceGroups/write  权限。

1. 在移动资源之前，请检查要将资源移动到的订阅的订阅配额。 如果移动资源意味着订阅将超出其限制，则需要检查是否可以请求增加配额。 有关限制的列表及如何请求增加配额的信息，请参阅 [Azure 订阅和服务限制、配额与约束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。

1. **若要跨订阅移动，则资源及其依赖资源必须位于同一资源组中，并且必须一起移动。** 例如，如果 VM 带有托管磁盘，则 VM 和托管磁盘以及其他依赖资源必须一起移动。

   如果要将某个资源移到新的订阅，请进行检查，看看该资源是否有任何依赖资源，以及它们是否位于同一资源组中。 如果这些资源不在同一资源组中，请看看能否将这些资源组合到同一资源组中。 如果可以，请跨资源组使用一个移动操作，将所有这些资源并入同一资源组。

   有关详细信息，请参阅[跨订阅移动方案](#scenario-for-move-across-subscriptions)。

## <a name="scenario-for-move-across-subscriptions"></a>跨订阅移动方案

将资源从一个订阅移到另一个订阅分为三步：

![跨订阅移动方案](./media/move-resource-group-and-subscription/cross-subscription-move-scenario.png)

为了演示方便，我们只有一个依赖资源。

* 步骤 1：如果依赖资源分布在不同的资源组中，请先将它们移到一个资源组。
* 步骤 2：将资源和依赖资源一起从源订阅移到目标订阅。
* 步骤 3：也可将依赖资源重新分布到目标订阅中的不同资源组。

## <a name="use-the-portal"></a>使用门户

若要移动资源，请选择包含那些资源的资源组。

选择要移动的资源。 若要移动所有资源，请选中列表顶部的复选框。 或者，分别选择各个资源。

:::image type="content" source="./media/move-resource-group-and-subscription/select-resources-to-move.png" alt-text="选择资源":::

选择“移动”按钮

:::image type="content" source="./media/move-resource-group-and-subscription/select-move.png" alt-text="移动选项":::

此按钮提供了三个选项：

* 移动到新的资源组。
* 移动到新的订阅。
* 移动到新的区域。 若要更改区域，请参阅[在区域之间移动资源（从资源组）](../../resource-mover/move-region-within-resource-group.md?toc=/azure/azure-resource-manager/management/toc.json)。

选择是要将资源移到新资源组还是新订阅。

将自动设置源资源组。 指定目标资源组。 如果要移动到新订阅，还请指定订阅。 选择“**下一页**”。

:::image type="content" source="./media/move-resource-group-and-subscription/select-destination-group.png" alt-text="选择目标资源组":::

门户验证资源是否可移动。 等待验证完成。

:::image type="content" source="./media/move-resource-group-and-subscription/validation.png" alt-text="移动验证":::

验证成功完成后，选择“下一步”。

确认需要更新这些资源的工具和脚本。 若要开始移动资源，请选择“移动”。

:::image type="content" source="./media/move-resource-group-and-subscription/acknowledge-change.png" alt-text="选择目标":::

移动完成后，你会获得结果通知。

:::image type="content" source="./media/move-resource-group-and-subscription/view-notification.png" alt-text="查看移动结果":::

## <a name="use-azure-powershell"></a>使用 Azure PowerShell

### <a name="validate"></a>验证

若要在不实际移动资源的情况下测试移动方案，请使用 [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) 命令。 仅在需要预先确定结果时，才使用此命令。 若要运行此操作，需要：

* 源资源组的资源 ID
* 目标资源组的资源 ID
* 要移动的每个资源的资源 ID

```azurepowershell
Invoke-AzResourceAction -Action validateMoveResources `
-ResourceId "/subscriptions/{subscription-id}/resourceGroups/{source-rg}" `
-Parameters @{ resources= @("/subscriptions/{subscription-id}/resourceGroups/{source-rg}/providers/{resource-provider}/{resource-type}/{resource-name}", "/subscriptions/{subscription-id}/resourceGroups/{source-rg}/providers/{resource-provider}/{resource-type}/{resource-name}", "/subscriptions/{subscription-id}/resourceGroups/{source-rg}/providers/{resource-provider}/{resource-type}/{resource-name}");targetResourceGroup = '/subscriptions/{subscription-id}/resourceGroups/{destination-rg}' }  
```

如果验证通过，则看不到任何输出。

如果验证失败，则会看到一条错误消息，说明无法移动资源的原因。

### <a name="move"></a>移动

要将现有资源移到另一个资源组或订阅，请使用 [Move-AzResource](/powershell/module/az.resources/move-azresource) 命令。 下面的示例演示了如何将多个资源移动到新的资源组。

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

若要移到新订阅，请包含 `DestinationSubscriptionId` 参数的值。

## <a name="use-azure-cli"></a>使用 Azure CLI

### <a name="validate"></a>验证

若要在不实际移动资源的情况下测试移动方案，请使用 [az resource invoke-action](/cli/azure/resource#az_resource_invoke_action) 命令。 仅在需要预先确定结果时，才使用此命令。 若要运行此操作，需要：

* 源资源组的资源 ID
* 目标资源组的资源 ID
* 要移动的每个资源的资源 ID

在请求正文中，使用 `\"` 转义双引号。

```azurecli
az resource invoke-action --action validateMoveResources \
  --ids "/subscriptions/{subscription-id}/resourceGroups/{source-rg}" \
  --request-body "{  \"resources\": [\"/subscriptions/{subscription-id}/resourceGroups/{source-rg}/providers/{resource-provider}/{resource-type}/{resource-name}\", \"/subscriptions/{subscription-id}/resourceGroups/{source-rg}/providers/{resource-provider}/{resource-type}/{resource-name}\", \"/subscriptions/{subscription-id}/resourceGroups/{source-rg}/providers/{resource-provider}/{resource-type}/{resource-name}\"],\"targetResourceGroup\":\"/subscriptions/{subscription-id}/resourceGroups/{destination-rg}\" }" 
```

如果验证通过，则会看到：

```azurecli
{} Finished .. 
```

如果验证失败，则会看到一条错误消息，说明无法移动资源的原因。

### <a name="move"></a>移动

若要将现有资源移动到另一个资源组或订阅，请使用 [az resource move](/cli/azure/resource#az_resource_move) 命令。 提供要移动的资源的资源 ID。 下面的示例演示了如何将多个资源移动到新的资源组。 在 `--ids` 参数中，提供要移动的资源 ID 的空格分隔列表。

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

若要移到新订阅，请提供 `--destination-subscription-id` 参数。

## <a name="use-rest-api"></a>使用 REST API

### <a name="validate"></a>验证

[验证移动操作](/rest/api/resources/resources/validate-move-resources)可以测试你的移动方案而无需实际移动资源。 使用此操作检查移动是否会成功。 发送移动请求时会自动调用验证。 仅当需要预先确定结果时才使用此操作。 若要运行此操作，需要：

* 源资源组的名称
* 目标资源组的资源 ID
* 要移动的每个资源的资源 ID
* 你的帐户的[访问令牌](/rest/api/azure/#acquire-an-access-token)

发送以下请求：

```HTTP
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2019-05-10
Authorization: Bearer <access-token>
Content-type: application/json
```

包含请求正文：

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

如果请求格式正确，则操作将返回：

```HTTP
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

202 状态代码指示已接受验证请求，但尚未确定移动操作是否会成功。 `location` 值包含用于检查长时间运行操作的状态的 URL。

若要检查状态，请发送以下请求：

```HTTP
GET <location-url>
Authorization: Bearer <access-token>
```

操作仍在运行时，会继续收到 202 状态代码。 请等待 `retry-after` 值中所示的秒数，然后重试。 如果移动操作验证成功，则会收到 204 状态代码。 如果移动验证失败，则会收到错误消息，例如：

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

### <a name="move"></a>移动

要将现有资源移到另一个资源组或订阅，请使用[移动资源](/rest/api/resources/resources/moveresources)操作。

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

在请求正文中，指定目标资源组和要移动的资源。

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

## <a name="frequently-asked-questions"></a>常见问题

**问：我的资源移动操作通常只需几分钟的时间，但这次已运行了几乎一个小时。出现了错误？**

移动资源是一项复杂的操作，包含不同的阶段。 它不仅仅涉及你尝试移动的资源的资源提供程序。 由于资源提供程序之间的依赖关系，Azure 资源管理器允许在 4 小时内完成操作。 此时间段为资源提供程序提供了从暂时性问题中恢复的机会。 如果你的移动请求还在四小时期间内，则操作会继续尝试完成，并且仍可能会成功。 源和目标资源组在这段时间内被锁定，以避免出现一致性问题。

**问：为什么我的资源组在资源移动期间锁定了四个小时？**

最多允许移动请求花费四个小时来完成。 为了防止对正在移动的资源进行修改，在资源移动期间，源资源组和目标资源组都会被锁定。

移动请求中有两个阶段。 在第一阶段中，将移动资源。 在第二阶段中，将向依赖于被移动资源的其他资源提供程序发送通知。 如果某个资源提供程序在任一阶段失败，则资源组可能会被锁定整整四个小时。 在允许的时间内，资源管理器将重试失败的步骤。

如果资源无法在四小时内移动，则资源管理器会解除对这两个资源组的锁定。 已成功移动的资源位于目标资源组中。 未能移动的资源留在源资源组中。

**问：资源移动过程中锁定的源和目标资源组有哪些影响？**

锁定会阻止你删除任一资源组，阻止在任一资源组中创建新资源或删除移动中涉及的任何资源。

下图显示了当用户尝试删除正在移动的资源组时来自 Azure 门户的错误消息。

![移动错误消息“正在尝试删除”](./media/move-resource-group-and-subscription/move-error-delete.png)

**问：错误代码“MissingMoveDependentResources”的含义是什么？**

移动资源时，依赖的资源必须存在于目标资源组或订阅中，或者包含在移动请求中。 当依赖的资源不满足此要求时，你将收到 MissingMoveDependentResources 错误代码。 错误消息包含有关需要在移动请求中包括的依赖资源的详细信息。

例如，移动某个虚拟机可能需要移动具有三个不同资源提供程序的七个资源类型。 这些资源提供程序和类型为：

* Microsoft.Compute

  * virtualMachines
  * disks
* Microsoft.Network
  * networkInterfaces
  * publicIPAddresses
  * networkSecurityGroups
  * virtualNetworks
* Microsoft.Storage
  * storageAccounts

另一个常见示例涉及移动虚拟网络。 可能必须移动与该虚拟网络关联的多个其他资源。 移动请求可能要求移动公共 IP 地址、路由表、虚拟网络网关、网络安全组和其他项。

问：错误代码“RequestDisallowedByPolicy”的含义是什么？

资源管理器在尝试移动之前验证移动请求。 此验证包括检查在移动所涉及的资源上定义的策略。 例如，如果尝试移动密钥保管库，但组织有一个策略拒绝在目标资源组中创建密钥保管库，则验证会失败并阻止移动。 返回的错误代码为 RequestDisallowedByPolicy。 

有关策略的详细信息，请参阅[什么是 Azure Policy？](../../governance/policy/overview.md)。

**问：为何无法移动 Azure 中的某个资源？**

目前，并非 Azure 中的所有资源都支持移动。 有关支持移动的资源列表，请参阅[资源的移动操作支持](move-support-resources.md)。

**问：可以在单个操作中移动多少资源？**

在可能的情况下，将大型移动分为单独的移动操作。 在一次操作中有 800 多项资源时，资源管理器会立即返回错误。 但是，移动 800 项以下的资源也可能因超时而失败。

**问：“资源未处于成功状态”此错误的意思是什么？**

如果收到一条错误消息，指出由于资源未处于成功状态而无法移动资源，则它可能实际上是阻止移动的依赖资源。 通常，错误代码为 **MoveCannotProceedWithResourcesNotInSucceededState**。

如果源或目标资源组包含虚拟网络，则会在移动过程中检查虚拟网络的所有依赖资源的状态。 该检查包括直接和间接依赖于虚拟网络的这些资源。 如果这些资源中有任何资源处于故障状态，则会阻止移动。 例如，如果某个使用虚拟网络的虚拟机故障，则会阻止移动。 即使该虚拟机不是要移动的资源之一，也不在要移动的资源组之一中，系统也会阻止移动。

收到此错误时，你有两个选择。 将资源移到没有虚拟网络的资源组，或[联系支持人员](../../azure-portal/supportability/how-to-create-azure-support-request.md)。

## <a name="next-steps"></a>后续步骤

有关支持移动的资源列表，请参阅[资源的移动操作支持](move-support-resources.md)。
