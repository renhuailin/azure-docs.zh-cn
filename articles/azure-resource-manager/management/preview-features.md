---
title: 在 Azure 订阅中设置预览功能
description: 介绍如何在 Azure 订阅中列出、注册或注销资源提供程序的预览功能。
ms.topic: how-to
ms.date: 08/18/2021
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 9d7b705e4db7c6556eea4b9fd3cbe1916ec257a4
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429840"
---
# <a name="set-up-preview-features-in-azure-subscription"></a>在 Azure 订阅中设置预览功能

本文演示如何管理 Azure 订阅中的预览功能。 借助预览功能，你可以在发布前便选择使用新功能。 有意向选择加入的任何用户都可以使用部分预览功能。 其他预览功能则需要获得产品团队的批准。

可通过 [Microsoft.Features](/rest/api/resources/features) 命名空间获取 Azure 功能公开控件 (AFEC)。 预览功能的资源 ID 格式如下：

`Microsoft.Features/providers/{resourceProviderNamespace}/features/{featureName}`

## <a name="list-preview-features"></a>列出预览功能

可以列出 Azure 订阅的所有预览功能及其注册状态。

# <a name="portal"></a>[Portal](#tab/azure-portal)

只有提供该项功能的服务明确选择参与预览功能管理体验时，门户才会显示预览功能。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 在搜索框中，输入“订阅” _，_ 然后选择“订阅”。

    :::image type="content" source="./media/preview-features/search.png" alt-text="Azure 门户搜索。":::

1. 选择订阅名称的链接。

    :::image type="content" source="./media/preview-features/subscriptions.png" alt-text="选择 Azure 订阅。":::

1. 从左侧菜单的“设置”下，选择“预览功能” 。

    :::image type="content" source="./media/preview-features/preview-features-menu.png" alt-text="Azure 预览功能菜单。":::

1. 你会看到包含可用预览功能和当前注册状态的列表。

    :::image type="content" source="./media/preview-features/preview-features-list.png" alt-text="Azure 门户预览功能列表。":::

1. 在“预览功能”中，可以按“名称”、“状态”或“类型”筛选列表   ：

    - 按名称筛选：必须包含预览功能名称而不是显示名称中的文本。
    - 状态：选择下拉菜单并选择状态。 门户不会按“已注销”进行筛选。
    - 类型：选择下拉菜单并选择类型。

    :::image type="content" source="./media/preview-features/filter.png" alt-text="Azure 门户筛选器预览功能。":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要列出订阅的所有预览功能，请使用 [az feature list](/cli/azure/feature#az_feature_list) 命令。

Azure CLI 的默认输出为 JSON。 有关其他输出格式的详细信息，请参阅 [Azure CLI 命令的输出格式](/cli/azure/format-output-azure-cli)。

```azurecli-interactive
az feature list
```

```json
{
  "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Features/providers/
    Microsoft.Compute/features/InGuestPatchVMPreview",
  "name": "Microsoft.Compute/InGuestPatchVMPreview",
  "properties": {
    "state": "NotRegistered"
  },
  "type": "Microsoft.Features/providers/features"
}
```

若要筛选特定资源提供程序的输出，请使用 `namespace` 参数。 此示例中，`output` 参数将指定表格式。

```azurecli-interactive
az feature list --namespace Microsoft.Compute --output table
```

```Output
Name                                                RegistrationState
-------------------------------------------------   -------------------
Microsoft.Compute/AHUB                              Unregistered
Microsoft.Compute/AllowManagedDisksReplaceOSDisk    Registered
Microsoft.Compute/AllowPreReleaseRegions            Pending
Microsoft.Compute/InGuestPatchVMPreview             NotRegistered
```

若要筛选特定预览功能的输出，请使用 [az feature show](/cli/azure/feature#az_feature_show) 命令。

```azurecli-interactive
az feature show --name InGuestPatchVMPreview --namespace Microsoft.Compute --output table
```

```Output
Name                                     RegistrationState
---------------------------------------  -------------------
Microsoft.Compute/InGuestPatchVMPreview  NotRegistered
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要列出订阅的所有预览功能，请使用 [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) 命令。

```azurepowershell-interactive
Get-AzProviderFeature -ListAvailable
```

```Output
FeatureName      ProviderName     RegistrationState
-----------      ------------     -----------------
betaAccess       Microsoft.AAD    NotRegistered
previewAccess    Microsoft.AAD    Registered
tipAccess        Microsoft.AAD    Pending
testAccess       Microsoft.AAD    Unregistered
```

若要筛选特定资源提供程序的输出，请使用 `ProviderNamespace` 参数。 默认输出仅会显示已注册的功能。 若要显示资源提供程序的所有预览功能，请配合使用 `ListAvailable` 参数与 `ProviderNamespace` 参数。

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -ListAvailable
```

```Output
FeatureName                          ProviderName        RegistrationState
-----------                          ------------        -----------------
AHUB                                 Microsoft.Compute   Unregistered
AllowManagedDisksReplaceOSDisk       Microsoft.Compute   Registered
AllowPreReleaseRegions               Microsoft.Compute   Pending
InGuestPatchVMPreview                Microsoft.Compute   NotRegistered
```

可以使用 `FeatureName` 参数筛选特定预览功能的输出。

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName "InGuestPatchVMPreview" -ProviderNamespace "Microsoft.Compute"
```

```Output
FeatureName             ProviderName        RegistrationState
-----------             ------------        -----------------
InGuestPatchVMPreview   Microsoft.Compute   NotRegistered
```

---

## <a name="register-preview-feature"></a>注册预览功能

在 Azure 订阅中注册预览功能，以公开资源提供程序的更多功能。 部分预览功能需要审批。

在订阅中注册预览功能后，你将看到以下两种状态之一：已注册或待处理 。

- 对于无需审批的预览功能，状态则为“已注册”。
- 如果预览功能需要审批，则注册状态为“待处理”。
  - 若要请求审批，请提交 [Azure 支持请求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。
  - 注册获得批准后，预览功能的状态将更改为“已注册”。

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 在搜索框中，输入“订阅” _，_ 然后选择“订阅”。
1. 选择订阅名称的链接。
1. 从左侧菜单的“设置”下，选择“预览功能” 。
1. 选择要注册的预览功能的链接。
1. 选择“注册”。

    :::image type="content" source="./media/preview-features/register.png" alt-text="Azure 门户注册预览功能。":::

1. 选择“确定”。

预览功能屏幕将刷新，预览功能的状态也会显示。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要注册预览功能，请使用 [az feature register](/cli/azure/feature#az_feature_register) 命令。

```azurecli-interactive
az feature register --name InGuestPatchVMPreview --namespace Microsoft.Compute
```

```json
{
  "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Features/providers/
    Microsoft.Compute/features/InGuestPatchVMPreview",
  "name": "Microsoft.Compute/InGuestPatchVMPreview",
  "properties": {
    "state": "Registering"
  },
  "type": "Microsoft.Features/providers/features"
}
```

若要查看注册状态，请使用 `az feature show` 命令。

```azurecli-interactive
az feature show --name InGuestPatchVMPreview --namespace Microsoft.Compute --output table
```

```Output
Name                                     RegistrationState
---------------------------------------  -------------------
Microsoft.Compute/InGuestPatchVMPreview  Registered
```

> [!NOTE]
> 当注册命令运行时，你会看到一则消息，其中指出需在注册该功能后，运行 `az provider register --namespace <provider-name>` 以传播更改。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要注册预览功能，请使用 [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) cmdlet。

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName "InGuestPatchVMPreview" -ProviderNamespace "Microsoft.Compute"
```

```Output
FeatureName             ProviderName        RegistrationState
-----------             ------------        -----------------
InGuestPatchVMPreview   Microsoft.Compute   Registering
```

若要查看注册状态，请使用 `Get-AzProviderFeature` cmdlet。

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName "InGuestPatchVMPreview" -ProviderNamespace "Microsoft.Compute"
```

```Output
FeatureName             ProviderName        RegistrationState
-----------             ------------        -----------------
InGuestPatchVMPreview   Microsoft.Compute   Registered
```

---

## <a name="unregister-preview-feature"></a>注销预览功能

使用完预览功能后，请从 Azure 订阅中注销该功能。 注销预览功能后，你可能会注意到两种不同的状态。 如果通过门户注销，状态将设为“未注册”。 如果通过 Azure CLI、PowerShell 或 REST API 注销，则状态将设为“已注销”。 状态存在差异的原因在于，门户会删除功能注册，但命令则会取消注册该功能。 在这两种情况下，该功能在订阅中均不再可用， 但可以通过重新注册功能来再次选择使用该功能。

# <a name="portal"></a>[Portal](#tab/azure-portal)

可以从“预览功能”中注销预览功能。 “状态”将更改为“未注册”。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 在搜索框中，输入“订阅” _，_ 然后选择“订阅”。
1. 选择订阅名称的链接。
1. 从左侧菜单的“设置”下，选择“预览功能” 。
1. 选择要注销的预览功能链接。
1. 选择“注销”。

    :::image type="content" source="./media/preview-features/unregister.png" alt-text="Azure 门户注销预览功能。":::

1. 选择“确定”。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要注销预览功能，请使用 [az feature register](/cli/azure/feature#az_feature_unregister) 命令。 `RegistrationState` 状态将更改为“已注销”。

```azurecli-interactive
az feature unregister --name InGuestPatchVMPreview --namespace Microsoft.Compute
```

```json
{
  "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Features/providers/
    Microsoft.Compute/features/InGuestPatchVMPreview",
  "name": "Microsoft.Compute/InGuestPatchVMPreview",
  "properties": {
    "state": "Unregistering"
  },
  "type": "Microsoft.Features/providers/features"
}
```

若要查看注销状态，请使用 `az feature show` 命令。

```azurecli-interactive
az feature show --name InGuestPatchVMPreview --namespace Microsoft.Compute --output table
```

```Output
Name                                     RegistrationState
---------------------------------------  -------------------
Microsoft.Compute/InGuestPatchVMPreview  Unregistered
```

> [!NOTE]
> 当注销命令运行时，你会看到一则消息，其中指出需在注销该功能后，运行 `az provider register --namespace <provider-name>` 以传播更改。

若要查找已注销的预览功能，请使用以下命令。 将 `<ResourceProvider.Name>` 替换为提供程序名称，例如 `Microsoft.Compute`。

以下示例显示 `Microsoft.Compute` 资源提供程序的“已注销”预览功能。

```azurecli-interactive
az feature list --namespace <ResourceProvider.Name> --query "[?properties.state=='Unregistered'].{Name:name, RegistrationState:properties.state}" --output table
```

```Output
Name                                     RegistrationState
---------------------------------------  -------------------
Microsoft.Compute/InGuestPatchVMPreview  Unregistered
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要注销预览功能，请使用 [Unregister-AzProviderFeature](/powershell/module/az.resources/unregister-azproviderfeature) cmdlet。 `RegistrationState` 状态将更改为“已注销”。

```azurepowershell-interactive
Unregister-AzProviderFeature -FeatureName "InGuestPatchVMPreview" -ProviderNamespace "Microsoft.Compute"
```

```Output
FeatureName             ProviderName        RegistrationState
-----------             ------------        -----------------
InGuestPatchVMPreview   Microsoft.Compute   Unregistering
```

若要查看注销状态，请使用 `Get-AzProviderFeature` cmdlet。

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName "InGuestPatchVMPreview" -ProviderNamespace "Microsoft.Compute"
```

```Output
FeatureName             ProviderName        RegistrationState
-----------             ------------        -----------------
InGuestPatchVMPreview   Microsoft.Compute   Unregistered
```

以下示例显示 `Microsoft.Compute` 资源提供程序的“已注销”预览功能。

```azurepowershell-interactive
Get-AzProviderFeature  -ProviderNamespace "Microsoft.Compute" -ListAvailable | Where-Object { $_.RegistrationState -eq "Unregistered" }
```

```Output
FeatureName             ProviderName        RegistrationState
-----------             ------------        -----------------
InGuestPatchVMPreview   Microsoft.Compute   Unregistered
```

---

## <a name="next-steps"></a>后续步骤

- 若要使用 REST API 调用并列出、注册或注销预览功能，请参阅[功能文档](/rest/api/resources/features)。
- 有关如何注册资源提供程序的详细信息，请参阅 [Azure 资源提供程序和类型](resource-providers-and-types.md)。
- 有关将资源提供程序映射到 Azure 服务的列表，请参阅 [Azure 服务的资源提供程序](azure-services-resource-providers.md)。
