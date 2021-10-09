---
title: 删除资源组和资源
description: 介绍如何删除资源组和资源。 它介绍删除资源组时 Azure 资源管理器如何为资源的删除排序。 它描述响应代码以及资源管理器如何处理它们以确定是否成功删除。
ms.topic: conceptual
ms.date: 09/28/2021
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 7995539ededec882b0b69e5ba3d1c5ef42adbcdc
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129211226"
---
# <a name="azure-resource-manager-resource-group-and-resource-deletion"></a>Azure 资源管理器资源组和资源删除

本文介绍如何删除资源组和资源。 它介绍在删除资源组时 Azure 资源管理器如何为资源的删除排序。

## <a name="how-order-of-deletion-is-determined"></a>如何确定删除顺序

删除资源组时，资源管理器会确定删除资源的顺序。 采用以下顺序：

1. 删除所有子（嵌套）资源。

2. 接下来，删除管理其他资源的资源。 可以为资源设置 `managedBy` 属性，用于指示管理它的其他资源。 设置此属性后，先删除管理其他资源的资源，再删除受管理的这些资源。

3. 删除前两种类别后，便会删除剩余资源。

确定顺序后，资源管理器为每个资源发出 DELETE 操作。 它先等待所有依赖项均完成操作，再继续。

对于同步操作，预期的成功响应代码为：

* 200
* 204
* 404

对于异步操作，预期的成功响应代码为 202。 资源管理器会跟踪 location 标头或 azure-async operation 标头，以确定异步删除操作的状态。
  
### <a name="deletion-errors"></a>删除错误

如果删除操作返回错误，资源管理器会重试 DELETE 调用。 若状态代码为 5xx、429 和 408，便会重试。 默认情况下，重试的时间段为 15 分钟。

## <a name="after-deletion"></a>删除之后

资源管理器对其尝试删除的每个资源发出 GET 调用。 此 GET 调用的响应应为 404。 当资源管理器获得 404 时，便将删除操作视为成功完成。 资源管理器会从其缓存中删除资源。

但是，如果对资源的 GET 调用返回 200 或 201，资源管理器会重新创建资源。

如果 GET 操作返回错误，资源管理器会为以下错误代码重试 GET 调用：

* 小于 100
* 408
* 429
* 大于 500

对于其他错误代码，它会将删除操作视为失败。

> [!IMPORTANT]
> 资源组删除不可逆。

## <a name="delete-resource-group"></a>删除资源组

使用以下方法之一删除资源组。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name ExampleResourceGroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group delete --name ExampleResourceGroup
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 在[门户](https://portal.azure.com)中，选择要删除的资源组。

1. 选择“删除资源组”。

   ![删除资源组](./media/delete-resource-group/delete-group.png)

1. 若要确认删除，请键入资源组的名称

---

## <a name="delete-resource"></a>删除资源

使用以下方法之一删除资源。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResource `
  -ResourceGroupName ExampleResourceGroup `
  -ResourceName ExampleVM `
  -ResourceType Microsoft.Compute/virtualMachines
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az resource delete \
  --resource-group ExampleResourceGroup \
  --name ExampleVM \
  --resource-type "Microsoft.Compute/virtualMachines"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 在[门户](https://portal.azure.com)中，选择要删除的资源。

1. 选择“删除” 。 以下屏幕截图显示了虚拟机的管理选项。

   ![删除资源](./media/delete-resource-group/delete-resource.png)

1. 出现提示时，确认删除。

---

## <a name="required-access-and-deletion-failures"></a>所需的访问权限和删除失败

若要删除资源组，你需要访问“Microsoft.Resources/subscriptions/resourceGroups”资源的删除操作。 还需要资源组中所有资源的删除权限。

有关操作的列表，请参阅 [Azure 资源提供程序操作](../../role-based-access-control/resource-provider-operations.md)。 有关内置角色的列表，请参阅 [Azure 内置角色](../../role-based-access-control/built-in-roles.md)。

如果你具有所需的访问权限，但删除请求失败，则可能是因为[资源或资源组上存在锁定](lock-resources.md)。 即使未手动锁定资源组，它也可能已[由相关服务自动锁定](lock-resources.md#managed-applications-and-locks)。 或如果资源连接到其他未删除的资源组中的资源，则删除可能会失败。 例如，不能删除子网仍被某个虚拟机使用的虚拟网络。

## <a name="next-steps"></a>后续步骤

* 若要了解资源管理器概念，请参阅 [Azure 资源管理器概述](overview.md)。
* 有关删除命令，请参阅 [PowerShell](/powershell/module/az.resources/Remove-AzResourceGroup)、[Azure CLI](/cli/azure/group#az_group_delete) 和 [REST API](/rest/api/resources/resourcegroups/delete)。
