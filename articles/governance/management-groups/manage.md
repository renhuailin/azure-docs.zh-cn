---
title: 如何使用管理组 - Azure 治理
description: 了解如何查看、维护、更新和删除管理组层次结构。
ms.date: 06/11/2021
ms.topic: conceptual
ms.openlocfilehash: a42800bb40f0c94de9b852eb77b3b87b698bdb09
ms.sourcegitcommit: 942a1c6df387438acbeb6d8ca50a831847ecc6dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112020936"
---
# <a name="manage-your-resources-with-management-groups"></a>使用管理组管理资源

如果你的组织有多个订阅，则可能需要一种方法来高效地管理这些订阅的访问权限、策略和符合性。 Azure 管理组提供订阅上的作用域级别。 可将订阅组织到名为“管理组”的容器中，并将管理条件应用到管理组。 管理组中的所有订阅都将自动继承应用于管理组的条件。

不管使用什么类型的订阅，管理组都能提供大规模的企业级管理。 有关管理组的详细信息，请参阅[使用 Azure 管理组整理资源](./overview.md)。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

> [!IMPORTANT]
> Azure 资源管理器用户令牌和管理组缓存持续 30 分钟后才会被强制刷新。 执行任何操作（如移动管理组或订阅）后，最多可能需要 30 分钟才会显示该操作。 如果想更快看到更新，需要通过刷新浏览器、登录并注销来更新令牌，或者请求新令牌。

## <a name="change-the-name-of-a-management-group"></a>更改管理组的名称

可以使用门户、PowerShell 或 Azure CLI 更改管理组的名称。

### <a name="change-the-name-in-the-portal"></a>在门户中更改名称

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 选择“所有服务” > “管理组”。 

1. 选择要重命名的管理组。

1. 选择“详细信息”。

1. 选择页面顶部的“重命名组”选项。

   :::image type="content" source="./media/detail_action_small.png" alt-text="“管理组”页面上操作栏和“重命名组”按钮的屏幕截图。" border="false":::

1. 菜单打开后，请输入要显示的新名称。

   :::image type="content" source="./media/rename_context.png" alt-text="“重命名组”窗口和用于重命名管理组的选项的屏幕截图。" border="false":::

1. 选择“保存”。

### <a name="change-the-name-in-powershell"></a>在 PowerShell 中更改名称

若要更新显示名称，请使用 Update-AzManagementGroup。 例如，若要将管理组的显示名称从“Contoso IT”更改为“Contoso Group”，可运行以下命令：

```azurepowershell-interactive
Update-AzManagementGroup -GroupName 'ContosoIt' -DisplayName 'Contoso Group'
```

### <a name="change-the-name-in-azure-cli"></a>在 Azure CLI 中更改名称

在 Azure CLI 中使用 update 命令。

```azurecli-interactive
az account management-group update --name 'Contoso' --display-name 'Contoso Group'
```

## <a name="delete-a-management-group"></a>删除管理组

若要删除某个管理组，必须满足以下要求：

1. 该管理组下面没有任何子管理组或订阅。 若要将订阅或管理组移到另一个管理组，请参阅[在层次结构中移动管理组和订阅](#moving-management-groups-and-subscriptions)。

1. 你需要拥有对管理组的写入权限（“所有者”、“参与者”或“管理组参与者”）。 若要查看自己拥有哪些权限，请选择管理组，然后选择“IAM”。 若要详细了解 Azure 角色，请参阅 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md)。

### <a name="delete-in-the-portal"></a>在门户中删除

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 选择“所有服务” > “管理组”。 

1. 选择要删除的管理组。

1. 选择“详细信息”。

1. 选择“删除”

   :::image type="content" source="./media/delete.png" alt-text="突出显示了“删除”按钮的“管理组”页面的屏幕截图。" border="false":::

   > [!TIP]
   > 如果该图标已禁用，将鼠标指针悬停在该图标上可显示原因。

1. 此时会打开一个窗口，让你确认是否要删除该管理组。

   :::image type="content" source="./media/delete_confirm.png" alt-text="用于删除管理组的“删除组”确认对话框的屏幕截图。" border="false":::

1. 请选择“是”。

### <a name="delete-in-powershell"></a>在 PowerShell 中删除

在 PowerShell 中使用 Remove-AzManagementGroup 命令删除管理组。

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

### <a name="delete-in-azure-cli"></a>在 Azure CLI 中删除

在 Azure CLI 中，可以使用 az account management-group delete 命令。

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="view-management-groups"></a>查看管理组

可以查看你对其拥有直接管理角色或继承 Azure 角色的任何管理组。

### <a name="view-in-the-portal"></a>在门户中查看

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 选择“所有服务” > “管理组”。 

1. 将加载管理组层次结构页。 可以在此页面中浏览你有权访问的所有管理组和订阅。 选择组名会将你带到层次结构的较低级别。 导航的工作方式与文件资源管理器一样。

1. 若要查看管理组的详细信息，请选择管理组标题旁边的“(详细信息)”链接。 如果此链接不可用，则表示你无权查看该管理组。

   :::image type="content" source="./media/main.png" alt-text="显示子管理组和订阅的“管理组”页面的屏幕截图。" border="false":::

### <a name="view-in-powershell"></a>在 PowerShell 中查看

使用 Get-AzManagementGroup 命令可检索所有组。 请参阅 [Az.Resources](/powershell/module/az.resources/Get-AzManagementGroup) 模块，查看管理组 GET PowerShell 命令的完整列表。

```azurepowershell-interactive
Get-AzManagementGroup
```

若要查看单个管理组的信息，请使用 -GroupName 参数

```azurepowershell-interactive
Get-AzManagementGroup -GroupName 'Contoso'
```

若要返回特定管理组及其下层次结构的所有级别，请使用 -Expand 和 -Recurse 参数。 

```azurepowershell-interactive
PS C:\> $response = Get-AzManagementGroup -GroupName TestGroupParent -Expand -Recurse
PS C:\> $response

Id                : /providers/Microsoft.Management/managementGroups/TestGroupParent
Type              : /providers/Microsoft.Management/managementGroups
Name              : TestGroupParent
TenantId          : 00000000-0000-0000-0000-000000000000
DisplayName       : TestGroupParent
UpdatedTime       : 2/1/2018 11:15:46 AM
UpdatedBy         : 00000000-0000-0000-0000-000000000000
ParentId          : /providers/Microsoft.Management/managementGroups/00000000-0000-0000-0000-000000000000
ParentName        : 00000000-0000-0000-0000-000000000000
ParentDisplayName : 00000000-0000-0000-0000-000000000000
Children          : {TestGroup1DisplayName, TestGroup2DisplayName}

PS C:\> $response.Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestGroup1
Name        : TestGroup1
DisplayName : TestGroup1DisplayName
Children    : {TestRecurseChild}

PS C:\> $response.Children[0].Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestRecurseChild
Name        : TestRecurseChild
DisplayName : TestRecurseChild
Children    :
```

### <a name="view-in-azure-cli"></a>在 Azure CLI 中查看

使用 list 命令可以检索所有组。

```azurecli-interactive
az account management-group list
```

若要查看单个管理组的信息，请使用 show 命令

```azurecli-interactive
az account management-group show --name 'Contoso'
```

若要返回特定管理组及其下层次结构的所有级别，请使用 -Expand 和 -Recurse 参数。 

```azurecli-interactive
az account management-group show --name 'Contoso' -e -r
```

## <a name="moving-management-groups-and-subscriptions"></a>移动管理组和订阅

创建管理组的原因之一是将订阅捆绑在一起。 只能将管理组和订阅设置为另一个管理组的子级。 移到管理组的订阅从父管理组继承所有用户访问权限和策略

将管理组或订阅移动为另一个管理组的子项时，三项规则的计算结果都需要为 true。

如果要执行移动操作，则需要对以下各层具有权限：

- 子订阅/管理组
  - `Microsoft.management/managementgroups/write`
  - `Microsoft.management/managementgroups/subscription/write`（仅适用于订阅）
  - `Microsoft.Authorization/roleassignment/write`
- 目标父管理组
  - `Microsoft.management/managementgroups/write`
- 当前父管理组
  - `Microsoft.management/managementgroups/write`

**例外**：如果目标或现有父管理组不是根管理组，则权限要求不适用。 由于根管理组是所有新管理组和订阅的默认登陆点，因此不需在其上具有相关权限即可移动某个项。

如果订阅上的“所有者”角色继承自当前管理组，你的移动目标会受限。 只能将订阅移到你在其中拥有“所有者”角色的另一管理组。 不能将订阅移到你在其中仅是参与者的管理组，因为你会失去订阅的所有权。 如果你已被直接分配了订阅的“所有者”角色，则可将它移到你在其中是参与者的任何管理组。

若要查看自己在 Azure 门户中拥有哪些权限，请选择管理组，然后选择“IAM”。 若要详细了解 Azure 角色，请参阅 [Azure 基于角色的访问控制 (Azure RBAC)](../../role-based-access-control/overview.md)。

## <a name="move-subscriptions"></a>移动订阅

### <a name="add-an-existing-subscription-to-a-management-group-in-the-portal"></a>在门户中将现有订阅添加到管理组

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 选择“所有服务” > “管理组”。 

1. 选择要设为父级的管理组。

1. 在页面顶部，选择“添加订阅”。

1. 在列表中选择具有正确 ID 的订阅。

   :::image type="content" source="./media/add_context_sub.png" alt-text="用于选择要添加到管理组的现有订阅的“添加订阅”选项的屏幕截图。" border="false":::

1. 选择“保存”。

### <a name="remove-a-subscription-from-a-management-group-in-the-portal"></a>在门户中从管理组删除订阅

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 选择“所有服务” > “管理组”。 

1. 选择要设为当前父级的管理组。

1. 在列表中，选择要移动的订阅所在行末尾的椭圆。

   :::image type="content" source="./media/move_small.png" alt-text="用于选择“移动”选项的订阅的备用菜单的屏幕截图。" border="false":::

1. 选择“移动”。

1. 在打开的菜单中，选择“父管理组”。

   :::image type="content" source="./media/move_small_context.png" alt-text="“移动”窗口和用于将订阅移到其他管理组的选项的屏幕截图。" border="false":::

1. 选择“保存”。

### <a name="move-subscriptions-in-powershell"></a>在 PowerShell 中移动订阅

若要在 PowerShell 中移动订阅，请使用 New-AzManagementGroupSubscription 命令。

```azurepowershell-interactive
New-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

若要删除订阅与管理组之间的链接，请使用 Remove-AzManagementGroupSubscription 命令。

```azurepowershell-interactive
Remove-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-azure-cli"></a>在 Azure CLI 中移动订阅

若要在 CLI 中移动订阅，请使用 add 命令。

```azurecli-interactive
az account management-group subscription add --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

若要从管理组中删除订阅，请使用 subscription remove 命令。

```azurecli-interactive
az account management-group subscription remove --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-arm-template"></a>在 ARM 模板中移动订阅

若要在 Azure 资源管理器模板（ARM 模板）中移动订阅，请使用以下模板并将其部署在[租户级别](../../azure-resource-manager/templates/deploy-to-tenant.md)上。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "targetMgId": {
            "type": "string",
            "metadata": {
                "description": "Provide the ID of the management group that you want to move the subscription to."
            }
        },
        "subscriptionId": {
            "type": "string",
            "metadata": {
                "description": "Provide the ID of the existing subscription to move."
            }
        }
    },
    "resources": [
        {
            "scope": "/",
            "type": "Microsoft.Management/managementGroups/subscriptions",
            "apiVersion": "2020-05-01",
            "name": "[concat(parameters('targetMgId'), '/', parameters('subscriptionId'))]",
            "properties": {
            }
        }
    ],
    "outputs": {}
}
```

## <a name="move-management-groups"></a>移动管理组

### <a name="move-management-groups-in-the-portal"></a>在门户中移动管理组

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 选择“所有服务” > “管理组”。 

1. 选择要设为父级的管理组。

1. 在页面顶部，选择“添加管理组”。

1. 在打开的菜单中，选择要使用新管理组或现有管理组。

   - 选择新管理组将创建一个新管理组。
   - 选择现有管理组将显示所有管理组的下拉列表，这些管理组可移动到此管理组。

   :::image type="content" source="./media/add_context_MG.png" alt-text="用于创建新管理组的“添加管理组”选项的屏幕截图。" border="false":::

1. 选择“保存”。

### <a name="move-management-groups-in-powershell"></a>在 PowerShell 中移动管理组

在 PowerShell 中使用 Update-AzManagementGroup 命令将管理组移到不同的组下面。

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName ContosoIT
Update-AzManagementGroup -GroupName 'Contoso' -ParentId $parentGroup.id
```

### <a name="move-management-groups-in-azure-cli"></a>在 Azure CLI 中移动管理组

在 Azure CLI 中使用 update 命令移动管理组。

```azurecli-interactive
az account management-group update --name 'Contoso' --parent ContosoIT
```

## <a name="audit-management-groups-using-activity-logs"></a>使用活动日志审核管理组

[Azure 活动日志](../../azure-monitor/essentials/platform-logs-overview.md)支持管理组。 可查询发生在与其他 Azure 资源位于相同中心位置的管理组上的所有事件。 例如，可以看到对特定管理组所做的所有角色分配或策略分配更改。

:::image type="content" source="./media/al-mg.png" alt-text="与所选管理组相关的活动日志和操作的屏幕截图。" border="false":::

如果要在 Azure 门户外针对管理组进行查询，管理组的目标范围将如下所示： **"/providers/Microsoft.Management/managementGroups/{yourMgID}"** 。

## <a name="referencing-management-groups-from-other-resource-providers"></a>从其他资源提供程序引用管理组

从其他资源提供程序的操作引用管理组时，请使用以下路径作为作用域。 使用 PowerShell、Azure CLI 和 REST API 时，将使用此路径。

`/providers/Microsoft.Management/managementGroups/{yourMgID}`

在 PowerShell 中向管理组分配新的角色分配时使用此路径的示例：

```azurepowershell-interactive
New-AzRoleAssignment -Scope "/providers/Microsoft.Management/managementGroups/Contoso"
```

在管理组处检索策略定义时使用相同的范围路径。

```http
GET https://management.azure.com/providers/Microsoft.Management/managementgroups/MyManagementGroup/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming?api-version=2019-09-01
```

## <a name="next-steps"></a>后续步骤

若要了解有关管理组的详细信息，请参阅：

- [创建管理组来组织 Azure 资源](./create-management-group-portal.md)
- [如何更改、删除或管理管理组](./manage.md)
- [在 Azure PowerShell 资源模块中查看管理组](/powershell/module/az.resources#resources)
- [在 REST API 中查看管理组](/rest/api/managementgroups/managementgroups)
- [在 Azure CLI 中查看管理组](/cli/azure/account/management-group)
