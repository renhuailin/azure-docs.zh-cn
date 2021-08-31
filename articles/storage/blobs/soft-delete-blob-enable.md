---
title: 为 blob 启用软删除
titleSuffix: Azure Storage
description: 为 blob 启用软删除，以防止意外删除或覆盖 blob 数据。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/29/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 790afdb013d2721bc90238cfe0caf7aa4534c632
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114289497"
---
# <a name="enable-soft-delete-for-blobs"></a>为 blob 启用软删除

Blob 软删除会在系统中将已删除的数据保留指定的一段时间，可以在意外删除或覆盖单个 Blob 及其版本、快照和元数据时提供保护。 在保留期内，可以将 Blob 还原到它在删除时的状态。 在指定的保留期已过后，Blob 将永久被删除。 有关 Blob 软删除的详细信息，请参阅 [Blob 的软删除](soft-delete-blob-overview.md)。

Blob 软删除是针对 Blob 数据的综合性数据保护策略的一部分。 若要详细了解 Microsoft 的数据保护建议，请参阅[数据保护概述](data-protection-overview.md)。

> [!NOTE]
> Blob 软删除还可以保护已启用分层命名空间功能的帐户中的 blob 和目录。 在已启用分层命名空间功能的帐户中进行的 blob 软删除目前为公共预览版，并且在全球所有 Azure 区域内均可用。 

新存储帐户默认禁用 blob 软删除。 你可以使用 Azure 门户、PowerShell 或 Azure CLI 随时启用或禁用存储帐户的软删除。

## <a name="enable-blob-soft-delete"></a>启用 blob 软删除

### <a name="portal"></a>[Portal](#tab/azure-portal)

若要使用 Azure 门户为存储帐户启用 blob 软删除，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中导航到存储帐户。
1. 在“Blob 服务”下，找到“数据保护”选项 。
1. 在“恢复”部分，选择“启用 blob 软删除” 。
1. 指定 1 到 365 天的保持期。 Microsoft 建议使用 7 天的最短保持期。
1. 保存所做更改。

:::image type="content" source="media/soft-delete-blob-enable/blob-soft-delete-configuration-portal.png" alt-text="显示如何在 Azure 门户中启用软删除的屏幕截图":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 PowerShell 启用 blob 软删除，请调用 [Enable-AzStorageBlobDeleteRetentionPolicy](/powershell/module/az.storage/enable-azstorageblobdeleteretentionpolicy) 命令，并指定保持期（天）。

以下示例启用 blob 软删除并将保持期设置为七天。 请记得将括号中的占位符值替换为你自己的值：

```azurepowershell
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -RetentionDays 7
```

若要检查 blob 软删除的当前设置，请调用 [Get-AzStorageBlobServiceProperty](/powershell/module/az.storage/get-azstorageblobserviceproperty) 命令：

```azurepowershell
$properties = Get-AzStorageBlobServiceProperty -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$properties.DeleteRetentionPolicy.Enabled
$properties.DeleteRetentionPolicy.Days
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-CLI)

若要使用 Azure CLI 启用 blob 软删除，请调用 [az storage account blob-service-properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) 命令，并指定保持期（天）。

以下示例启用 blob 软删除并将保持期设置为七天。 请记得将括号中的占位符值替换为你自己的值：

```azurecli-interactive
az storage account blob-service-properties update --account-name <storage-account> \
    --resource-group <resource-group> \
    --enable-delete-retention true \
    --delete-retention-days 7
```

若要检查 blob 软删除的当前设置，请调用 [az storage account blob-service-properties show](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_show) 命令：

```azurecli-interactive
az storage account blob-service-properties show --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="enable-blob-soft-delete-hierarchical-namespace"></a>启用 blob 软删除（分层命名空间）

blob 软删除还可以保护已启用分层命名空间功能的帐户中的 blob 和目录。 

> [!IMPORTANT]
> 在已启用分层命名空间功能的帐户中进行的软删除目前为预览版，并且在全球所有 Azure 区域内均可用。
> 有关 beta 版本、预览版或尚未正式发布的版本的 Azure 功能所适用的法律条款，请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>
>
> 若要注册预览版，请参阅[此表单](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4mEEwKhLjlBjU3ziDwLH-pUOVRVOUpDRUtHVUtDUUtMVTZUR0tUMjZWNy4u)。

<a id="enable-blob-soft-delete-hierarchical-namespace"></a>

### <a name="portal"></a>[Portal](#tab/azure-portal)

若要使用 Azure 门户为存储帐户启用 blob 软删除，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中导航到存储帐户。
1. 在“数据管理”下找到“数据保护”选项。
1. 在“恢复”部分中，选择“启用 blob 软删除”。
1. 指定 1 到 365 天的保持期。 Microsoft 建议使用 7 天的最短保持期。
1. 保存所做更改。

> [!div class="mx-imgBorder"]
> ![显示如何在 Azure 门户中对具有分层命名空间的帐户启用软删除的屏幕截图](./media/soft-delete-blob-enable/blob-soft-delete-configuration-portal-hierarchical-namespace.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 安装最新的 **PowershellGet** 模块。 然后关闭 PowerShell 控制台，接着重新将其打开。

    ```powershell
    install-Module PowerShellGet –Repository PSGallery –Force 
    ```

2.  安装 **Az.Storage** 预览版模块。

    ```powershell
    Install-Module Az.Storage -Repository PsGallery -RequiredVersion 3.7.1-preview -AllowClobber -AllowPrerelease -Force
    ```
    有关如何安装 PowerShell 模块的详细信息，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)

3. 使用存储帐户密钥、连接字符串或 Azure Active Directory (Azure AD) 获取存储帐户授权。 请参阅[连接到帐户](data-lake-storage-directory-file-acl-powershell.md#connect-to-the-account)。

   以下示例使用存储帐户密钥获取授权。

   ```powershell
   $ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -StorageAccountKey '<storage-account-key>'
   ```

4. 若要使用 PowerShell 启用 blob 软删除，请使用 [Enable-AzStorageDeleteRetentionPolicy](/powershell/module/az.storage/enable-azstoragedeleteretentionpolicy) 命令，并指定保持期（以天为单位）。

   下面的示例启用了帐户的软删除功能，并将保持期设置为 4 天。 

   ```powershell
   Enable-AzStorageDeleteRetentionPolicy -RetentionDays 4  -Context $ctx
   ```
5. 若要检查 blob 软删除的当前设置，请使用 `Get-AzStorageServiceProperty` 命令：

   ```powershell
    Get-AzStorageServiceProperty -ServiceType Blob -Context $ctx
   ```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-CLI)

1. 打开 [Azure Cloud Shell](../../cloud-shell/overview.md)，或者，如果已在本地[安装](/cli/azure/install-azure-cli) Azure CLI，请打开命令控制台应用程序，如 Windows PowerShell。

2. 安装 `storage-preview` 扩展。

   ```azurecli
   az extension add -n storage-preview
   ```
3. 连接到你的存储帐户。 请参阅[连接到帐户](data-lake-storage-directory-file-acl-cli.md#connect-to-the-account)。

   > [!NOTE]
   > 本文中提供的示例演示 Azure Active Directory (Azure AD) 授权操作。 若要详细了解身份验证方法，请参阅[使用 Azure CLI 授权访问 blob 或队列数据](./authorize-data-operations-cli.md)。
 
4. 若要在 Azure CLI 中启用软删除，请调用 `az storage fs service-properties update` 命令，并指定保持期（以天为单位）。

   下面的示例启用了 blob 和目录软删除，并将保持期设置为 5 天。 

   ```azurecli
   az storage fs service-properties update --delete-retention --delete-retention-period 5 --auth-mode login
   ```

5. 若要检查 blob 软删除的当前设置，请调用 `az storage fs service-properties update` 命令：

   ```azurecli
   az storage fs service-properties update --delete-retention false --connection-string $con
   ```

---

## <a name="next-steps"></a>后续步骤

- [blob 的软删除](soft-delete-blob-overview.md)
- [管理和还原软删除的 Blob](soft-delete-blob-manage.md)