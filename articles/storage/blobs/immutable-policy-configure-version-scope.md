---
title: 为 Blob 版本配置不可变性策略（预览版）
titleSuffix: Azure Storage
description: 了解如何配置范围限定为某个 Blob 版本的不可变性策略（预览版）。 不可变性策略通过以不可擦除、不可修改状态存储数据，为 Blob 存储提供 WORM（一次写入，多次读取）支持。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/31/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 04e05f67787b285dd1286e0c6b7a6b251262ed0f
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123272234"
---
# <a name="configure-immutability-policies-for-blob-versions-preview"></a>为 Blob 版本配置不可变性策略（预览版）

Azure Blob 存储的不可变存储可让用户以 WORM（一次写入，多次读取）状态存储业务关键型数据。 在 WORM 状态下，在用户指定的间隔内无法修改或删除数据。 为 Blob 数据配置不可变性策略可以防范数据被覆盖和删除。 不可变性策略包括基于时间的保留策略和法定保留。 有关 Blob 存储的不可变性策略的详细信息，请参阅[使用不可变存储来存储业务关键型 Blob 数据](immutable-storage-overview.md)。

不可变性策略的范围可以限定为单个 Blob 版本（预览版）或容器。 本文介绍如何配置版本级不可变性策略。 若要了解如何配置容器级不可变性策略，请参阅[为容器配置不可变性策略](immutable-policy-configure-container-scope.md)。

配置版本级不可变性策略的过程包括两个步骤：

1. 首先，对新容器或现有容器启用版本级不可变性支持。 有关详细信息，请参阅[对容器启用版本级不可变性支持](#enable-support-for-version-level-immutability-on-a-container)。
1. 接下来，配置应用于该容器中一个或多个 Blob 版本的基于时间的保留策略或法定保留。

> [!IMPORTANT]
> 版本级不可变性策略目前为预览版。 有关 beta 版本、预览版或尚未正式发布的版本的 Azure 功能所适用的法律条款，请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

若要配置版本级基于时间的保留策略，必须为存储帐户启用 Blob 版本控制。 若要了解如何启用 blob 版本控制，请参阅[启用和管理 blob 版本控制](versioning-enable.md)。

有关版本级不可变性策略支持的存储帐户配置的信息，请参阅[支持的帐户配置](immutable-storage-overview.md#supported-account-configurations)。

## <a name="enable-support-for-version-level-immutability-on-a-container"></a>对容器启用版本级不可变性支持

在将基于时间的保留策略应用于 Blob 版本之前，必须启用版本级不可变性支持。 新容器和现有容器都可配置为支持版本级不可变性。 但是，现有容器必须经历迁移过程才能为其启用此项支持。

请记住，为某个容器启用版本级不可变性支持并不会使该容器中的数据不可变。 此外，必须为容器配置默认的不可变性策略，或者对特定的 Blob 版本配置不可变性策略。

### <a name="enable-version-level-immutability-for-a-new-container"></a>为新容器启用版本级不可变性

若要使用版本级不可变性策略，首先必须显式对容器启用版本级 WORM 支持。 可以在创建容器时或者在向现有容器添加版本级不可变性策略时启用版本级 WORM 支持。

#### <a name="portal"></a>[Portal](#tab/azure-portal)

若要在 Azure 门户中创建支持版本级不可变性的容器，请执行以下步骤：

1. 在 Azure 门户中导航到你的存储帐户的“容器”页，然后选择“添加” 。
1. 在“新建容器”对话框中提供容器的名称，然后展开“高级”部分 。
1. 选择“启用版本级不可变性支持”以便为容器启用版本级不可变性。

    :::image type="content" source="media/immutable-policy-configure-version-scope/create-container-version-level-immutability.png" alt-text="该屏幕截图显示如何创建启用了版本级不可变性的容器":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

要使用 PowerShell 创建支持版本级不可变性的容器，请先安装 [Az.Storage 模块](https://www.powershellgallery.com/packages/Az.Storage/3.10.1-preview) 预览版 3.10.1。

接下来，使用 `-EnableImmutableStorageWithVersioning` 参数调用 New-AzRmStorageContainer 命令，如以下示例所示。 请记得将尖括号中的占位符替换为你自己的值：

```azurepowershell
# Create a container with version-level immutability support.
$container = New-AzRmStorageContainer -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -Name <container> `
    -EnableImmutableStorageWithVersioning

# Verify that version-level immutability support is enabled for the container
$container.ImmutableStorageWithVersioning
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 创建支持版本级不可变性的容器，请先安装 Azure CLI 2.27 或更高版本。 有关安装 Azure CLI 的详细信息，请参阅[如何安装 Azure CLI](/cli/azure/install-azure-cli)。

接下来，调用 [az storage container-rm create](/cli/azure/storage/container-rm#az_storage_container_rm_create) 命令，指定 `--enable-vlw` 参数。 请记得将尖括号中的占位符替换为你自己的值：

```azurecli
# Create a container with version-level immutability support.
az storage container-rm create \
    --name <container> \
    --storage-account <storage-account> \
    --resource-group <resource-group> \
    --enable-vlw

# Verify that version-level immutability support is enabled for the container
az storage container-rm show \
    --storage-account <storage-account> \
    --name <container> \
    --query '[immutableStorageWithVersioning.enabled]' \
    --output tsv
```

---

### <a name="migrate-an-existing-container-to-support-version-level-immutability"></a>迁移现有容器以支持版本级不可变性

若要为现有容器配置版本级不可变性策略，必须迁移该容器以支持版本级不可变存储。 容器迁移可能需要一段时间，此操作不可逆。

若要迁移现有容器以支持版本级不可变性策略，容器必须配置容器级基于时间的保留策略。 除非容器具有现有策略，否则迁移将失败。 容器级策略的保留期间隔与容器上默认版本级策略的保留期间隔保持一致。

如果容器具有现有的容器级法定保留，则只有在删除法定保留之后才能迁移该容器。

#### <a name="portal"></a>[Portal](#tab/azure-portal)

若要在 Azure 门户中迁移容器以支持版本级不可变存储，请执行以下步骤：

1. 导航到所需的容器。
1. 选择右侧的“更多”按钮，然后选择“访问策略” 。
1. 在“不可变 Blob 存储”下，选择“添加策略” 。
1. 对于“策略类型”字段，请选择“基于时间的保留”并指定保留间隔。
1. 选择“启用版本级不可变性”。
1. 选择“确定”以创建具有指定保留期间隔的容器级策略，然后开始迁移到版本级不可变性支持。

    :::image type="content" source="media/immutable-policy-configure-version-scope/migrate-existing-container.png" alt-text="该屏幕截图显示如何迁移现有容器以支持版本级不可变性":::

执行迁移操作时，容器上的策略范围显示为容器。

:::image type="content" source="media/immutable-policy-configure-version-scope/container-migration-in-process.png" alt-text="该屏幕截图显示正在迁移容器":::

迁移完成后，容器上的策略范围显示为版本。 显示的策略是容器上的默认策略，该策略自动应用于随后在容器中创建的所有 blob 版本。 通过为该版本指定自定义策略，可以在任何版本上替代默认策略。

:::image type="content" source="media/immutable-policy-configure-version-scope/container-migration-complete.png" alt-text="该屏幕截图显示已完成容器迁移":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

要使用 PowerShell 迁移容器以支持版本级不可变存储，首先要确保该容器存在容器级基于时间的保留策略。 若要创建一个策略，请调用 [Set-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/set-azrmstoragecontainerimmutabilitypolicy)。

```azurepowershell
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
   -StorageAccountName <storage-account> `
   -ContainerName <container> `
   -ImmutabilityPeriod <retention-interval-in-days>
```

接下来，调用 Invoke-AzRmStorageContainerImmutableStorageWithVersioningMigration 命令以迁移容器。 添加 `-AsJob` 参数以异步运行该命令。 建议异步运行该操作，因为迁移可能需要一些时间才能完成。

```azurepowershell
$migrationOperation = Invoke-AzRmStorageContainerImmutableStorageWithVersioningMigration `
    -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -Name <container> `
    -AsJob
```

若要检查长时间运行的操作的状态，请阅读该操作的 JobStateInfo.State 属性。

```azurepowershell
$migrationOperation.JobStateInfo.State
```

在尝试迁移到版本级不可变性时，如果容器尚无基于时间的保留策略，则操作将失败。 如果由于不存在容器级策略而导致操作失败，以下示例将检查 JobStateInfo.State 属性的值并显示错误消息。

```azurepowershell
if ($migrationOperation.JobStateInfo.State -eq "Failed") {
Write-Host $migrationOperation.Error
}
The container <container-name> must have an immutability policy set as a default policy 
before initiating container migration to support object level immutability with versioning.
```

迁移完成后，检查操作的 Output 属性，看看是否启用了版本级不可变性支持。

```azurepowershell
$migrationOperation.Output
```

有关 PowerShell 作业的详细信息，请参阅[在 PowerShell 作业中运行 Azure PowerShell cmdlet](/powershell/azure/using-psjobs)。

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

要使用 Azure CLI 迁移容器以支持版本级不可变存储，首先要确保该容器存在容器级基于时间的保留策略。 若要创建一个策略，请调用 [az storage container immutability-policy create](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_create)。

```azurecli
az storage container immutability-policy create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --container-name <container> \
    --period <retention-interval-in-days>
```

接下来，调用 [az storage container-rm migrate-vlw](/cli/azure/storage/container-rm#az_storage_container_rm_migrate_vlw) 命令以迁移容器。 添加 `--no-wait` 参数以异步运行该命令。 建议异步运行该操作，因为迁移可能需要一些时间才能完成。

```azurecli
az storage container-rm migrate-vlw \
    --resource-group <resource-group> \
    --storage-account <storage-account> \
    --name <container> \
    --no-wait
```

若要检查长时间运行的操作的状态，请读取 migrationState 属性的值。

```azurecli
az storage container-rm show \
    --storage-account <storage-account> \
    --name <container> \
    --query '[immutableStorageWithVersioning.migrationState]' \
    --output tsv
```

---

## <a name="configure-a-time-based-retention-policy-on-a-container"></a>针对容器配置基于时间的保留策略

容器启用版本级不可变性后，可以为容器指定默认的版本级基于时间的保留策略。 为容器指定默认策略后，该策略默认应用于容器中创建的所有新 Blob 版本。 可以替代容器中任何单个 blob 版本的默认策略。

默认策略不会自动应用于配置默认策略之前存在的 blob 版本。

如果迁移了现有容器以支持版本级不变性，则迁移前生效的容器级策略会迁移到容器的默认版本级策略。

### <a name="configure-a-default-time-based-retention-policy-on-a-container"></a>针对容器配置默认的基于时间的保留策略

若要为容器配置默认的版本级不可变性策略，请使用 Azure 门户、PowerShell、Azure CLI 或任一 Azure 存储 SDK。 确保已为容器启用版本级不可变性支持，如[在容器上启用版本级不可变性支持](#enable-support-for-version-level-immutability-on-a-container)中所述。

#### <a name="portal"></a>[Portal](#tab/azure-portal)

若要为 Azure 门户中的容器配置默认的版本级不可变性策略，请执行以下步骤：

1. 在 Azure 门户中导航到“容器”页，找到要对其应用该策略的容器。
1. 选择容器名称右侧的“更多”按钮，然后选择“访问策略” 。
1. 在“访问策略”对话框中的“不可变 Blob 存储”部分下，选择“添加策略”  。
1. 选择“基于时间的保留策略”并指定保留间隔。
1. 如果需要，请选择“允许其他受保护的追加”以便能够写入到不可变性策略保护的追加 Blob。 有关详细信息，请参阅[允许受保护的追加 Blob 写入](immutable-time-based-retention-policy-overview.md#allow-protected-append-blobs-writes)。
1. 选择“确定”以将默认策略应用于容器。

    :::image type="content" source="media/immutable-policy-configure-version-scope/configure-default-retention-policy-container.png" alt-text="该屏幕截图显示如何为容器配置默认的版本级保留策略":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 PowerShell 为容器配置默认的版本级不可变性策略，请调用 [Set-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/set-azrmstoragecontainerimmutabilitypolicy) 命令。

```azurepowershell
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
   -StorageAccountName <storage-account> `
   -ContainerName <container> `
   -ImmutabilityPeriod <retention-interval-in-days> `
   -AllowProtectedAppendWrite $true
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 为容器配置默认的版本级不可变性策略，请调用 [az storage container immutability-policy create](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_create) 命令。

```azurecli
az storage container immutability-policy create \
    --account-name <storage-account> \
    --container-name <container> \
    --period <retention-interval-in-days> \
    --allow-protected-append-writes true
```

---

### <a name="determine-the-scope-of-a-retention-policy-on-a-container"></a>确定针对容器的保留策略的范围

若要在 Azure 门户中确定基于时间的保留策略的范围，请执行以下步骤：

1. 导航到所需的容器。
1. 选择右侧的“更多”按钮，然后选择“访问策略” 。
1. 在“不可变 Blob 存储”下，找到“范围”字段 。 如果为容器配置了默认的版本级保留策略，则范围将设置为“版本”，如下图所示：

    :::image type="content" source="media/immutable-policy-configure-version-scope/version-scoped-retention-policy.png" alt-text="该屏幕截图显示针对容器配置的默认版本级保留策略":::

1. 如果为容器配置了容器级保留策略，则范围将设置为“容器”，如下图所示：

    :::image type="content" source="media/immutable-policy-configure-version-scope/container-scoped-retention-policy.png" alt-text="该屏幕截图显示针对容器配置的容器级保留策略":::

## <a name="configure-a-time-based-retention-policy-on-an-existing-version"></a>针对现有版本配置基于时间的保留策略

基于时间的保留策略在指定的间隔内将 Blob 数据保持为 WORM 状态。 有关基于时间的保留策略的详细信息，请参阅[不可变 Blob 数据的基于时间的保留策略](immutable-time-based-retention-policy-overview.md)。

可使用三个选项为 Blob 版本配置基于时间的保留策略：

- 选项 1：可以配置一个范围限定为容器并默认应用于该容器中所有对象的默认策略。 除非你通过针对单个 Blob 版本配置策略来显式替代默认策略，否则容器中的对象将继承默认策略。 有关更多详细信息，请参阅[针对容器配置默认的基于时间的保留策略](#configure-a-default-time-based-retention-policy-on-a-container)。
- 选项 2：可以针对 Blob 的当前版本配置策略。 此策略可以替代针对容器配置的默认策略（如果默认策略存在且已解锁）。 默认情况下，在配置策略后创建的所有先前版本将继承针对当前 Blob 版本的策略。 有关更多详细信息，请参阅[针对当前 Blob 版本配置保留策略](#configure-a-retention-policy-on-the-current-version-of-a-blob)。
- 选项 3：可以针对先前的 Blob 版本配置策略。 此策略可以替代针对当前版本配置的默认策略（如果默认策略存在且已解锁）。 有关更多详细信息，请参阅[针对先前的 Blob 版本配置保留策略](#configure-a-retention-policy-on-a-previous-version-of-a-blob)。

有关 Blob 版本控制的详细信息，请参阅 [Blob 版本控制](versioning-overview.md)。

### <a name="portal"></a>[Portal](#tab/azure-portal)

导航到某个容器时，Azure 门户会显示 Blob 列表。 显示的每个 Blob 表示当前 Blob 版本。 通过选择 blob 的“更多”按钮并选择“查看以前的版本”，可访问以前版本的列表 。  

### <a name="configure-a-retention-policy-on-the-current-version-of-a-blob"></a>针对当前 Blob 版本配置保留策略

若要针对当前 Blob 版本配置基于时间的保留策略，请执行以下步骤：

1. 导航到包含目标 Blob 的容器。
1. 选择 Blob 名称右侧的“更多”按钮，然后选择“访问策略” 。 如果已为先前版本配置了基于时间的保留策略，该策略会出现在“访问策略”对话框中。
1. 在“访问策略”对话框中的“不可变 Blob 版本”部分下，选择“添加策略”  。
1. 选择“基于时间的保留策略”并指定保留间隔。
1. 选择“确定”以将策略应用于当前 Blob 版本。

    :::image type="content" source="media/immutable-policy-configure-version-scope/configure-retention-policy-version.png" alt-text="该屏幕截图显示如何为当前 Blob 版本配置保留策略":::

可以查看 Blob 的属性，以确定是否对当前版本启用了某个策略。 选择该 Blob，然后导航到“概述”选项卡并找到“版本级不可变性策略”属性 。 如果启用了某个策略，“保留期”属性将显示该策略的过期日期和时间。 请记住，可为当前版本配置策略，也可以从 Blob 的父容器继承策略（如果默认策略已生效）。

:::image type="content" source="media/immutable-policy-configure-version-scope/view-version-level-retention-policy-portal.png" alt-text="该屏幕截图显示 Azure 门户中 Blob 版本的不可变性策略属性":::

### <a name="configure-a-retention-policy-on-a-previous-version-of-a-blob"></a>针对先前的 Blob 版本配置保留策略

还可以针对先前的 blob 版本配置基于时间的保留策略。 先前的版本始终是不可变的，因为无法修改它。 不过，可以删除先前的版本。 基于时间的保留策略在生效的情况下可以防止被删除。

若要针对先前的 Blob 版本配置基于时间的保留策略，请执行以下步骤：

1. 导航到包含目标 Blob 的容器。
1. 选择该 Blob，然后导航到“版本”选项卡。
1. 找到目标版本，然后依次选择“更多”按钮和“访问策略” 。 如果已为先前版本配置了基于时间的保留策略，该策略会出现在“访问策略”对话框中。
1. 在“访问策略”对话框中的“不可变 Blob 版本”部分下，选择“添加策略”  。
1. 选择“基于时间的保留策略”并指定保留间隔。
1. 选择“确定”以将策略应用于当前 Blob 版本。

    :::image type="content" source="media/immutable-policy-configure-version-scope/configure-retention-policy-previous-version.png" alt-text="该屏幕截图显示如何在 Azure 门户中为先前的 Blob 版本配置保留策略":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 PowerShell 在 blob 版本上配置基于时间的保留策略，请调用 Set-AzStorageBlobImmutabilityPolicy 命令。

```azurepowershell
# Get the storage account context
$ctx = (Get-AzStorageAccount `
        -ResourceGroupName <resource-group> `
        -Name <storage-account>).Context

Set-AzStorageBlobImmutabilityPolicy -Container <container> `
    -Blob <blob-version> `
    -Context $ctx `
    -ExpiresOn "2021-09-01T12:00:00Z" `
    -PolicyMode Unlocked
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

空值

---

## <a name="configure-a-time-based-retention-policy-when-uploading-a-blob"></a>上传 Blob 时配置基于时间的保留策略

使用 Azure 门户将 Blob 上传到支持版本级不可变性的容器时，可通过多个选项为新 Blob 配置基于时间的保留策略：

- 选项 1：如果为容器配置了默认保留策略，则可以上传具有容器策略的 Blob。 如果存在针对容器的保留策略，则默认会选择此选项。
- 选项 2：如果为容器配置了默认保留策略，则可以选择替代默认策略，方法是为新 Blob 定义自定义保留策略，或上传没有策略的 Blob。
- 选项 3：如果未为容器配置默认策略，则可以上传具有自定义策略或没有任何策略的 Blob。

若要在上传 Blob 时配置基于时间的保留策略，请执行以下步骤：

1. 导航到所需的容器，选择“上传”。
1. 在“上传 Blob”对话框中，展开“高级”部分 。
1. 在“保留策略”字段中，为新 Blob 配置基于时间的保留策略。 如果为容器配置了默认策略，则默认会选择该策略。 还可为 Blob 指定自定义策略。

    :::image type="content" source="media/immutable-policy-configure-version-scope/configure-retention-policy-blob-upload.png" alt-text="该屏幕截图显示用于在 Azure 门户中上传 Blob 时配置保留策略的选项":::

## <a name="modify-or-delete-an-unlocked-retention-policy"></a>修改或删除未锁定的保留策略

可以修改已解锁的基于时间的保留策略以缩短或延长保留间隔。 还可以删除已解锁的策略。 编辑或删除针对某个 Blob 版本的已解锁基于时间的保留策略不影响针对任何其他版本生效的策略。 如果存在针对容器生效的默认基于时间的保留策略，则使用已修改或已删除策略的 Blob 版本将不再从容器继承。

### <a name="portal"></a>[Portal](#tab/azure-portal)

若要在 Azure 门户中修改已解锁的基于时间的保留策略，请执行以下步骤：

1. 查找目标容器或版本。 依次选择“更多”按钮和“访问策略” 。
1. 查找现有的未锁定不可变性策略。 选择“更多”按钮，然后从菜单中选择“编辑” 。

    :::image type="content" source="media/immutable-policy-configure-version-scope/edit-existing-version-policy.png" alt-text="该屏幕截图显示如何在 Azure 门户中编辑现有版本级基于时间的保留策略":::

1. 提供新的策略过期日期和时间。

若要删除未锁定策略，请从“更多”菜单中选择“删除” 。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 PowerShell 修改未锁定的基于时间的保留策略，请使用策略到期的新日期和时间对 blob 版本调用 Set-AzStorageBlobImmutabilityPolicy 命令。

```azurepowershell
$containerName = "<container>"
$blobName = "<blob>"

# Get the previous blob version.
$blobVersion = Get-AzStorageBlob -Container $containerName `
    -Blob $blobName `
    -VersionId "2021-08-31T00:26:41.2273852Z" `
    -Context $ctx

# Extend the retention interval by five days.
$blobVersion = $blobVersion | 
    Set-AzStorageBlobImmutabilityPolicy -ExpiresOn (Get-Date).AddDays(5) `

# View the new policy parameters.
$blobVersion.BlobProperties.ImmutabilityPolicy
```

若要删除未锁定的保留策略，请调用 Remove-AzStorageBlobImmutabilityPolicy 命令。

```azurepowershell
$blobVersion = $blobVersion | Remove-AzStorageBlobImmutabilityPolicy
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

空值

---

## <a name="lock-a-time-based-retention-policy"></a>锁定基于时间的保留策略

测试完基于时间的保留策略后，可以锁定该策略。 已锁定的策略符合 SEC 17a-4(f) 和其他法规标准。 最多可将已锁定策略的保留间隔延长五倍，但不能缩短保留间隔。

锁定策略后无法删除该策略。 但是，可以在保留间隔过期后删除 blob。

### <a name="portal"></a>[Portal](#tab/azure-portal)

若要在 Azure 门户中锁定策略，请执行以下步骤：

1. 查找目标容器或版本。 依次选择“更多”按钮和“访问策略” 。
1. 在“不可变 Blob 版本”部分下，找到现有的已解锁策略。 选择“更多”按钮，然后从菜单中选择“锁定策略” 。
1. 确认要锁定该策略。

    :::image type="content" source="media/immutable-policy-configure-version-scope/lock-policy-portal.png" alt-text="该屏幕截图显示如何在 Azure 门户中锁定基于时间的保留策略":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 PowerShell 锁定策略，请调用 Set-AzStorageBlobImmutabilityPolicy 命令并将 PolicyMode 参数设置为“Locked” 。

以下示例显示如何通过指定对未锁定策略有效的相同保留期间隔来锁定策略。 也可在锁定策略时更改到期时间。

```azurepowershell
# Get the previous blob version.
$blobVersion = Get-AzStorageBlob -Container $containerName `
    -Blob $blobName `
    -VersionId "2021-08-31T00:26:41.2273852Z" `
    -Context $ctx

$blobVersion = $blobVersion | 
    Set-AzStorageBlobImmutabilityPolicy `
        -ExpiresOn $blobVersion.BlobProperties.ImmutabilityPolicy.ExpiresOn `
        -PolicyMode Locked
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

空值

---

## <a name="configure-or-clear-a-legal-hold"></a>配置或清除法定保留

在显式清除法定保留之前，该法定保留会一直存储不可变数据。 若要详细了解法定保留策略，请参阅[不可变 Blob 数据的法定保留](immutable-legal-hold-overview.md)。

#### <a name="portal"></a>[Portal](#tab/azure-portal)

若要使用 Azure 门户配置 blob 版本上的法定保留，请执行以下步骤：

1. 找到目标版本，这可能是 Blob 的当前版本或先前版本。 依次选择“更多”按钮和“访问策略” 。
1. 在“不可变 Blob 版本”部分下，选择“添加策略” 。
1. 选择“法定保留”作为策略类型，然后选择“确定”以应用法定保留 。

下图显示了一个当前 Blob 版本，为其配置了基于时间的保留策略和法定保留。

:::image type="content" source="media/immutable-policy-configure-version-scope/configure-legal-hold-blob-version.png" alt-text="该屏幕截图显示针对 Blob 版本配置的法定保留":::

若要清除法定保留，请导航到“访问策略”对话框，选择“更多”按钮，然后选择“删除”  。

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

要使用 PowerShell 配置或清除 blob 版本上的法定保留，请调用 Set-AzStorageBlobLegalHold 命令。

```azurepowershell
# Set a legal hold
Set-AzStorageBlobLegalHold -Container <container> `
    -Blob <blob-version> `
    -Context $ctx `
    -EnableLegalHold

# Clear a legal hold
Set-AzStorageBlobLegalHold -Container <container> `
    -Blob <blob-version> `
    -Context $ctx `
    -DisableLegalHold
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

空值

---

## <a name="next-steps"></a>后续步骤

- [使用不可变的存储来存储业务关键型 Blob 数据](immutable-storage-overview.md)
- [不可变 Blob 数据的基于时间的保留策略](immutable-time-based-retention-policy-overview.md)
- [不可变 Blob 数据的法定保留](immutable-legal-hold-overview.md)
