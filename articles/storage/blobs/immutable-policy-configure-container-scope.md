---
title: 为容器配置不可变性策略
titleSuffix: Azure Storage
description: 了解如何配置范围限定为某个容器的不可变性策略。 不可变性策略通过以不可擦除、不可修改状态存储数据，为 Blob 存储提供 WORM（一次写入，多次读取）支持。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/16/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3a28699af7167bfbc6ffd9a00d64b4fbfd593693
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129389373"
---
# <a name="configure-immutability-policies-for-containers"></a>为容器配置不可变性策略

Azure Blob 存储的不可变存储可让用户以 WORM（一次写入，多次读取）状态存储业务关键型数据。 在 WORM 状态下，在用户指定的间隔内无法修改或删除数据。 为 Blob 数据配置不可变性策略可以防范数据被覆盖和删除。 不可变性策略包括基于时间的保留策略和法定保留。 有关 Blob 存储的不可变性策略的详细信息，请参阅[使用不可变存储来存储业务关键型 Blob 数据](immutable-storage-overview.md)。

不可变性策略的范围可以限定为单个 blob 版本（预览版）或容器。 本文介绍如何配置容器级不可变性策略。 若要了解如何配置版本级不可变性策略，请参阅[为 blob 版本配置不可变性策略（预览版）](immutable-policy-configure-version-scope.md)。

## <a name="configure-a-retention-policy-on-a-container"></a>在容器上配置保留策略

若要在容器上配置基于时间的保留策略，请使用 Azure 门户、PowerShell 或 Azure CLI。 可以将容器级别的保留策略配置为介于 1 到 146000 天之间。

### <a name="portal"></a>[Portal](#tab/azure-portal)

若要使用 Azure 门户在容器上配置基于时间的保留策略，请执行以下步骤：

1. 导航到所需的容器。
1. 选择右侧的“更多”按钮，然后选择“访问策略” 。
1. 在“不可变 blob 存储”部分，选择“添加策略” 。
1. 对于“策略类型”字段，请选择“基于时间的保留”并指定保留天数 。
1. 若要创建容器范围的策略，请勿选中“启用版本级不可变性”复选框。
1. 如果需要，请选择“允许其他受保护的追加”以便能够写入到受不可变性策略保护的追加 blob。 有关详细信息，请参阅[允许受保护的追加 blob 写入](immutable-time-based-retention-policy-overview.md#allow-protected-append-blobs-writes)。

    :::image type="content" source="media/immutable-policy-configure-container-scope/configure-retention-policy-container-scope.png" alt-text="显示如何配置容器范围的不可变性策略的屏幕截图":::

配置不可变性策略后，你会看到它的范围限定为容器：

:::image type="content" source="media/immutable-policy-configure-container-scope/view-retention-policy-container-scope.png" alt-text="显示范围限定为容器的不可变性策略的屏幕截图":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 PowerShell 在容器上配置基于时间的保留策略，请调用 [Set-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/set-azrmstoragecontainerimmutabilitypolicy) 命令，并提供保留间隔（天）。 请务必将尖括号中的占位符值替换为你自己的值：

```azurepowershell
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -ContainerName <container> `
    -ImmutabilityPeriod 10
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 在容器上配置基于时间的保留策略，请调用 [az storage container immutability-policy create](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_create) 命令，并提供保留间隔（天）。 请务必将尖括号中的占位符值替换为你自己的值：

```azurecli
az storage container immutability-policy create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --container-name <container> \
    --period 10
```

---

## <a name="modify-an-unlocked-retention-policy"></a>修改已解锁的保留策略

你可以修改已解锁的基于时间的保留策略，以缩短或延长保留间隔，并允许在容器中额外写入追加 blob。 还可删除已解锁的策略。

### <a name="portal"></a>[Portal](#tab/azure-portal)

若要在 Azure 门户中修改已解锁的基于时间的保留策略，请执行以下步骤：

1. 导航到所需的容器。
1. 依次选择“更多”按钮和“访问策略” 。
1. 在“不可变 Blob 版本”部分下，找到现有的已解锁策略。 选择“更多”按钮，然后从菜单中选择“编辑” 。
1. 为策略提供新的保留间隔。 还可选择“允许其他受保护的追加”，以允许写入受保护的追加 blob。

    :::image type="content" source="media/immutable-policy-configure-container-scope/modify-retention-policy-container-scope.png" alt-text="显示如何修改已解锁的基于时间的保留策略的屏幕截图":::

若要删除已解锁的策略，请选择“更多”按钮，然后选择“删除” 。

> [!NOTE]
> 可通过选中“启用版本级不可变性”复选框，启用版本级不可变性策略（预览版）。 若要了解如何配置版本级不可变性保留策略，请参阅[为 blob 版本配置不可变性策略（预览版）](immutable-policy-configure-version-scope.md)。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要修改已解锁的策略，请先通过调用 [AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/get-azrmstoragecontainerimmutabilitypolicy) 命令以检索策略。 接下来，调用 [AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/set-azrmstoragecontainerimmutabilitypolicy) 命令以更新策略。 包括新的保留间隔（天）和 `-ExtendPolicy` 参数。 请务必将尖括号中的占位符值替换为你自己的值：

```azurepowershell
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
    -AccountName <storage-account> `
    -ContainerName <container>

Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -ContainerName <container> `
    -ImmutabilityPeriod 21 `
    -AllowProtectedAppendWrite true `
    -Etag $policy.Etag `
    -ExtendPolicy
```

若要删除已解锁的策略，请调用 [AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/remove-azrmstoragecontainerimmutabilitypolicy) 命令。

```azurepowershell
Remove-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
    -AccountName <storage-account> `
    -ContainerName <container>
    -Etag $policy.Etag
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 修改已解锁的基于时间的保留策略，请调用 [az storage container immutability-policy extend](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_extend) 命令，并提供新的保留间隔（天）。 请务必将尖括号中的占位符值替换为你自己的值：

```azurecli
$etag=$(az storage container immutability-policy show \
        --account-name <storage-account> \
        --container-name <container> \
        --query etag \
        --output tsv)

az storage container immutability-policy extend \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --container-name <container> \
    --period 21 \
    --if-match $etag \
    --allow-protected-append-writes true
```

若要删除已解锁的策略，请调用 [az storage container immutability-policy delete](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_delete) 命令。

---

## <a name="lock-a-time-based-retention-policy"></a>锁定基于时间的保留策略

测试完基于时间的保留策略后，可以锁定该策略。 已锁定的策略符合 SEC 17a-4(f) 和其他法规标准。 最多可将已锁定策略的保留间隔延长五倍，但不能缩短保留间隔。

锁定策略后无法删除该策略。 但是，可以在保留间隔过期后删除 blob。

### <a name="portal"></a>[Portal](#tab/azure-portal)

若要使用 Azure 门户锁定策略，请执行以下步骤：

1. 导航到具有已解锁策略的容器。
1. 在“不可变 blob 版本”部分下，找到现有的已解锁策略。 选择“更多”按钮，然后从菜单中选择“锁定策略” 。
1. 确认要锁定该策略。

:::image type="content" source="media/immutable-policy-configure-container-scope/lock-retention-policy.png" alt-text="显示如何在 Azure 门户中锁定基于时间的保留策略的屏幕截图":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 PowerShell 锁定策略，请先调用 [Get-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/get-azrmstoragecontainerimmutabilitypolicy) 命令以检索策略的 ETag。 接下来，调用 [AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/lock-azrmstoragecontainerimmutabilitypolicy) 命令并传入 ETag 值以锁定策略。 请务必将尖括号中的占位符值替换为你自己的值：

```azurepowershell
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
    -AccountName <storage-account> `
    -ContainerName <container>

Lock-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
    -AccountName <storage-account> `
    -ContainerName <container> `
    -Etag $policy.Etag
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 锁定策略，请先调用 [az storage container immutability-policy show](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_show) 命令以检索策略的 ETag。 接下来，调用 [az storage container immutability-policy lock](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_lock) 命令并传入 ETag 值以锁定策略。 请务必将尖括号中的占位符值替换为你自己的值：

```azurecli
$etag=$(az storage container immutability-policy show \
        --account-name <storage-account> \
        --container-name <container> \
        --query etag \
        --output tsv)

az storage container immutability-policy lock \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --container-name <container> \
    --if-match $etag
```

---

## <a name="configure-or-clear-a-legal-hold"></a>配置或清除法定保留

在显式清除法定保留之前，该法定保留会一直存储不可变数据。 若要详细了解法定保留策略，请参阅[不可变 Blob 数据的法定保留](immutable-legal-hold-overview.md)。

### <a name="portal"></a>[Portal](#tab/azure-portal)

若要使用 Azure 门户配置容器上的法定保留，请执行以下步骤：

1. 导航到所需的容器。
1. 依次选择“更多”按钮和“访问策略” 。
1. 在“不可变 Blob 版本”部分下，选择“添加策略” 。
1. 选择“法定保留”作为策略类型，然后选择“确定”以应用法定保留 。

下图显示了一个配置了基于时间的保留策略和法定保留的容器。

:::image type="content" source="media/immutable-policy-configure-container-scope/retention-policy-legal-hold-container-scope.png" alt-text="显示配置了基于时间的保留策略和法定保留的容器的屏幕截图":::

若要清除法定保留，请导航到“访问策略”对话，选择“更多”按钮，然后选择“删除”  。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 PowerShell 配置容器上的法定保留，请调用 [AzRmStorageContainerLegalHold](/powershell/module/az.storage/add-azrmstoragecontainerlegalhold) 命令。 请务必将尖括号中的占位符值替换为你自己的值：

```azurepowershell
Add-AzRmStorageContainerLegalHold -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -Name <container> `
    -Tag <tag1>,<tag2>,...
```

若要清除法定保留，请调用 [Remove-AzRmStorageContainerLegalHold](/powershell/module/az.storage/remove-azrmstoragecontainerlegalhold) 命令：

```azurepowershell
Remove-AzRmStorageContainerLegalHold -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -Name <container> `
    -Tag <tag1>,<tag2>,...
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 PowerShell 配置容器上的法定保留，请调用 [az storage container legal-hold set](/cli/azure/storage/container/legal-hold#az_storage_container_legal_hold_set) 命令。 请务必将尖括号中的占位符值替换为你自己的值：

```azurecli
az storage container legal-hold set \
    --tags tag1 tag2 \
    --container-name <container> \
    --account-name <storage-account> \
    --resource-group <resource-group>
```

若要清除合法保留，请调用 [az storage container legal-hold clear](/cli/azure/storage/container/legal-hold#az_storage_container_legal_hold_clear) 命令：

```azurecli
az storage container legal-hold clear \
    --tags tag1 tag2 \
    --container-name <container> \
    --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>后续步骤

- [使用不可变的存储来存储业务关键型 Blob 数据](immutable-storage-overview.md)
- [不可变 Blob 数据的基于时间的保留策略](immutable-time-based-retention-policy-overview.md)
- [不可变 Blob 数据的法定保留](immutable-legal-hold-overview.md)
- [为 Blob 版本配置不可变性策略（预览版）](immutable-policy-configure-version-scope.md)
