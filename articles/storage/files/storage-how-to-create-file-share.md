---
title: 创建 Azure 文件共享
titleSuffix: Azure Files
description: 如何使用 Azure 门户、PowerShell 或 Azure CLI 创建 Azure 文件共享。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/05/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, references_regions, devx-track-azurepowershell
ms.openlocfilehash: 0100bd0e0eb0ee6dbd802ad1cf5df002a706c12c
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110676156"
---
# <a name="create-an-azure-file-share"></a>创建 Azure 文件共享
若要创建 Azure 文件共享，需要回答有关你将如何使用它的三个问题：

- **Azure 文件共享的性能要求是什么？**  
    Azure 文件存储提供在基于硬盘（基于 HDD）的硬件上托管的标准文件共享，以及在基于固态磁盘（基于 SSD）的硬件上托管的高级文件共享。

- **Azure 文件共享在冗余方面的要求是什么？**  
    标准文件共享提供本地冗余存储 (LRS)、区域冗余存储 (ZRS)、异地冗余存储 (GRS) 或异地区域冗余存储 (GZRS)，但是，只有本地冗余和区域冗余文件共享才支持大型文件共享功能。 高级文件共享不支持任何形式的异地冗余。

    高级文件共享在区域子集中提供本地冗余和区域冗余。 若要确定高级文件共享目前是否可在你的区域中使用，请参阅 Azure 的[产品的上市区域](https://azure.microsoft.com/global-infrastructure/services/?products=storage)页。 有关支持 ZRS 的区域的信息，请参阅 [Azure 存储冗余](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

- **需要哪种大小的文件共享？**  
    在本地和区域冗余存储帐户中，Azure 文件共享最多可以实现 100 TiB 的跨越，但在异地冗余和异地区域冗余存储帐户上，Azure 文件共享最多只能实现 5 TiB 的跨越。 

有关这三个选项的详细信息，请参阅[规划 Azure 文件存储部署](storage-files-planning.md)。

## <a name="prerequisites"></a>先决条件
- 本文假设已创建一个 Azure 订阅。 如果还没有订阅，则请在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 如果你打算使用 Azure PowerShell，请[安装最新版本](/powershell/azure/install-az-ps)。
- 如果你打算使用 Azure CLI，请[安装最新版本](/cli/azure/install-azure-cli)。

## <a name="create-a-storage-account"></a>创建存储帐户
Azure 文件共享将部署到存储帐户。存储帐户是代表存储共享池的顶级对象。 此存储池可用于部署多个文件共享。 

对于客户的不同存储方案，Azure 支持多种类型的存储帐户，但对于 Azure 文件存储，有两个主要类型的存储帐户。 需要创建的存储帐户类型取决于你是要创建标准文件共享还是要创建高级文件共享： 

- **常规用途版本 2 (GPv2) 存储帐户**：使用 GPv2 存储帐户可以在标准的/基于硬盘（基于 HDD）的硬件上部署 Azure 文件共享。 除了存储 Azure 文件共享以外，GPv2 存储帐户还可以存储其他存储资源，例如 Blob 容器、队列或表。 文件共享可部署到事务优化层（默认）、热层或冷层中。

- **FileStorage 存储帐户**：使用 FileStorage 存储帐户可以在高级/基于固态磁盘（基于 SSD）的硬件上部署 Azure 文件共享。 FileStorage 帐户只能用于存储 Azure 文件共享；其他存储资源（Blob 容器、队列、表等）都不能部署在 FileStorage 帐户中。

# <a name="portal"></a>[Portal](#tab/azure-portal)
若要通过 Azure 门户创建存储帐户，请在仪表板中选择“+ 创建资源”。 在出现的 Azure 市场搜索窗口中搜索“存储帐户”，并选择生成的搜索结果。 此时会显示存储帐户的概述页；选择“创建”继续执行存储帐户创建向导中的步骤。

![浏览器中存储帐户快速创建选项的屏幕截图](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="basics"></a>基础
创建存储帐户所要完成的第一部分是标有“基本信息”的部分。 此部分包含创建存储帐户所需填写的所有字段。 若要创建 GPv2 存储帐户，请确保将“性能”单选按钮设置为“标准”，并在“帐户类型”下拉列表中选择“StorageV2 (常规用途 v2)”。

:::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-performance-standard.png" alt-text="屏幕截图：“性能”单选按钮已选择为“标准”、“帐户类型”已选择为“StorageV2”。":::

若要创建 FileStorage 存储帐户，请确保将“性能”单选按钮设置为“高级”，并在“高级帐户类型”下拉列表中选择“Fileshares”。

:::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-performance-premium.png" alt-text="屏幕截图：“性能”单选按钮已选择为“高级”、“帐户类型”已选择为“FileStorage”。":::

其他基本信息字段与存储帐户的选择无关：
- **存储帐户名称**：要创建的存储帐户资源的名称。 此名称必须全局唯一，但可以是所需的任何名称。 通过 SMB 装载 Azure 文件共享时，存储帐户名称将用作服务器名称。
- **位置**：要部署到的存储帐户所在的区域。 此区域可以是与资源组关联的区域，也可以是任何其他可用区域。
- **复制**：尽管此字段的标签为“复制”，但它实际上指的是“冗余”；这是所需的冗余级别：本地冗余 (LRS)、区域冗余存储 (ZRS)、异地冗余存储 (GRS) 或异地区域冗余存储 (GZRS)。 此下拉列表还包含读取访问权限异地冗余存储 (RA-GRS) 和读取访问权限异地区域冗余存储 (GZRS)，它不适用于 Azure 文件共享；在选择这些选项的情况下在存储帐户中创建的任何文件共享实际上是异地冗余或异地区域冗余存储的（分别）。 

#### <a name="networking"></a>网络
在“网络”部分可以配置网络选项。 创建存储帐户时，这些设置是可选的，以后可根据需要对其进行配置。 有关这些选项的详细信息，请参阅 [Azure 文件存储的网络注意事项](storage-files-networking-overview.md)。

#### <a name="data-protection"></a>数据保护
可通过“数据保护”部分为存储帐户中的 Azure 文件共享配置软删除策略。 其他与 Blob、容器的软删除、容器的时间点还原、版本控制以及更改源相关的设置仅适用于 Azure Blob 存储。

#### <a name="advanced"></a>高级
“高级”部分包含 Azure 文件共享的几项重要设置：

- **需要安全传输**：此字段指示存储帐户是否需要对与存储帐户的通信进行传输中加密。 如果需要 SMB 2.1 支持，则必须禁用此功能。

    :::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-secure-transfer.png" alt-text="屏幕截图：在存储帐户的高级设置中启用安全传输。":::

- **大型文件共享**：此字段为最多涵盖 100 TiB 空间的文件共享启用存储帐户。 启用此功能会将存储帐户限制为只能使用本地冗余和区域冗余存储选项。 为大型文件共享启用 GPv2 存储帐户后，无法禁用大型文件共享功能。 FileStorage 存储帐户（高级文件共享的存储帐户）没有此选项，因为所有高级文件共享最多可以扩展到 100 TiB。 

    :::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-large-file-shares.png" alt-text="屏幕截图：存储帐户的“高级”边栏选项卡中的“大文件共享”设置。":::

“高级”选项卡中提供的其他设置（Azure Data Lake Storage Gen 2 的分层命名空间、默认 blob 层、blob 存储的 NFSv3 等）不适用于 Azure 文件存储。

> [!Important]  
> 选择 Blob 访问层不会影响文件共享的层。

#### <a name="tags"></a>Tags
标记是名称/值对，可让你通过将相同的标记应用到多个资源和资源组，对资源进行分类并查看合并的账单。 这些设置是可选的，可以在创建存储帐户后应用。

#### <a name="review--create"></a>查看 + 创建
创建存储帐户的最后一步是选中“查看 + 创建”选项卡上的“创建”按钮。 如果未填写存储帐户的所有必填字段，则此按钮将不可用。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
我们将在 PowerShell 中使用 `New-AzStorageAccount` cmdlet 创建存储帐户。 此 cmdlet 有许多选项；此处只显示了必需的选项。 若要详细了解高级选项，请参阅 [`New-AzStorageAccount` cmdlet 文档](/powershell/module/az.storage/new-azstorageaccount)。

为了简化存储帐户和后续文件共享的创建，我们将在变量中存储多个参数。 可将变量内容替换为所需的任何值，但请注意，存储帐户名称必须全局唯一。

```powershell
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

我们将使用以下命令创建能够存储标准 Azure 文件共享的存储帐户。 `-SkuName` 参数与所需的冗余类型相关；如果需要异地冗余或异地区域冗余存储帐户，则还必须删除 `-EnableLargeFileShare` 参数。

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

我们将使用以下命令创建能够存储高级 Azure 文件共享的存储帐户。 请注意，`-SkuName` 参数已更改为包含 `Premium` 和所需的冗余级别：本地冗余 (`LRS`)。 `-Kind` 参数是 `FileStorage` 而不是 `StorageV2`，因为必须在 FileStorage 存储帐户（而不是 GPv2 存储帐户）中创建高级文件共享。

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $region `
    -Kind FileStorage 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
我们将在 Azure CLI 中使用 az storage account create 命令创建存储帐户。 此命令有许多选项；此处只显示了必需的选项。 若要详细了解高级选项，请参阅 [`az storage account create` 命令文档](/cli/azure/storage/account)。

为了简化存储帐户和后续文件共享的创建，我们将在变量中存储多个参数。 可将变量内容替换为所需的任何值，但请注意，存储帐户名称必须全局唯一。

```azurecli
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

我们将使用以下命令创建能够存储标准 Azure 文件共享的存储帐户。 `--sku` 参数与所需的冗余类型相关；如果需要异地冗余或异地区域冗余存储帐户，则还必须删除 `--enable-large-file-share` 参数。

```azurecli
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

我们将使用以下命令创建能够存储高级 Azure 文件共享的存储帐户。 请注意，`--sku` 参数已更改为包含 `Premium` 和所需的冗余级别：本地冗余 (`LRS`)。 `--kind` 参数是 `FileStorage` 而不是 `StorageV2`，因为必须在 FileStorage 存储帐户（而不是 GPv2 存储帐户）中创建高级文件共享。

```azurecli
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

## <a name="create-a-file-share"></a>创建文件共享
创建存储帐户后，剩下的操作就是创建文件共享。 无论使用的是高级文件共享还是标准文件共享，此过程大部分都是相同的。 应考虑以下差异。

标准文件共享可以部署到任一标准层：事务优化（默认）层、热层或冷层。 这是一个不受存储帐户的 blob 访问层影响的每文件共享层（此属性仅与 Azure Blob 存储相关 - 与 Azure 文件存储完全不相关）。 部署共享后，可随时更改共享的层。 高级文件共享不能直接转换到任何标准层。

> [!Important]  
> 可以在 GPv2 存储帐户类型中的各层（事务优化、热和冷）之间移动文件共享。 共享在层间移动会产生事务：从较热层移动到较冷层会导致对共享中每个文件收取冷层的写入事务费用，而从较冷层移动到较热层会导致对共享中每个文件收取冷层的读取事务费用。

“quota”属性表示高级和标准文件共享之间略有不同：

- 标准文件共享是 Azure 文件共享的上限，最终用户不能超越此上限。 如果未指定配额，则标准文件共享最多可以涵盖 100 TiB 的空间（如果未为存储帐户设置大型文件共享属性，则最多可以涵盖 5 TiB）。

- 对于高级文件共享，配额表示预配的大小。 预配的大小是将要对你计费的数量，与实际使用情况无关。 有关如何规划高级文件共享的详细信息，请参阅[预配高级文件共享](understanding-billing.md#provisioned-model)。

# <a name="portal"></a>[Portal](#tab/azure-portal)
如果你刚刚创建了存储帐户，可以在部署屏幕中选择“转到资源”导航到该存储帐户。 在存储帐户中，在存储帐户的目录中选择“文件共享”。

在文件共享列表中，应会看到以前在此存储帐户中创建的任何文件共享；如果尚未创建任何文件共享，则会显示一个空表。 选择“+ 文件共享”创建新的文件共享。

此时屏幕上应会显示“新建文件共享”边栏选项卡。 填写“新建文件共享”边栏选项卡中的字段以创建文件共享：

- **名称**：要创建的文件共享的名称。
- **配额**：标准文件共享的文件共享配额；高级文件共享的文件共享预配大小。
- **层**：为文件共享选择的层。 此字段仅适用于常规用途 (GPv2) 存储帐户。 可以选择事务优化层、热层或冷层。 可以随时更改共享层。 建议在迁移过程中选择可选的最热层，以最大程度减少事务费用，然后在迁移完成后切换到较低的层（如果需要）。

选择“创建”以完成新共享的创建。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
可以使用 [`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare) cmdlet 创建 Azure 文件共享。 以下 PowerShell 命令假设已根据前面有关使用 Azure PowerShell 创建存储帐户的部分中的定义，设置了变量 `$resourceGroupName` 和 `$storageAccountName`。 

以下示例演示如何使用 `-AccessTier` 参数创建具有显式层的文件共享。 这需要使用预览版 Az.Storage 模块，如示例中所示。 如果未指定层，原因可能是使用的是正式版的 Az.Storage 模块，或由于未包含此命令，标准文件共享的默认层是事务优化层。

> [!Important]  
> 对于高级文件共享，`-QuotaGiB` 参数指的是文件共享的预配大小。 文件共享的预配大小是将要对你计费的数量，与使用情况无关。 标准文件共享按使用情况计费，而不是按预配大小计费。

```powershell
# Assuming $resourceGroupName and $storageAccountName from earlier in this document have already
# been populated. The access tier parameter may be TransactionOptimized, Hot, or Cool for GPv2 
# storage accounts. Standard tiers are only available in standard storage accounts. 
$shareName = "myshare"

New-AzRmStorageShare `
        -ResourceGroupName $resourceGroupName `
        -StorageAccountName $storageAccountName `
        -Name $shareName `
        -AccessTier TransactionOptimized `
        -QuotaGiB 1024 | `
    Out-Null
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
可以使用 [`az storage share-rm create`](/cli/azure/storage/share-rm#az_storage_share_rm_create) 命令创建 Azure 文件共享。 以下 Azure CLI 命令假设你已根据前面“使用 Azure CLI 创建存储帐户”部分中的定义，设置了变量 `$resourceGroupName` 和 `$storageAccountName`。

> [!Important]  
> 对于高级文件共享，`--quota` 参数指的是文件共享的预配大小。 文件共享的预配大小是将要对你计费的数量，与使用情况无关。 标准文件共享按使用情况计费，而不是按预配大小计费。

```azurecli
shareName="myshare"

az storage share-rm create \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "TransactionOptimized" \
    --quota 1024 \
    --output none
```

---

> [!Note]  
> 文件共享的名称必须是全部小写。 有关为文件共享和文件命名的完整详细信息，请参阅 [命名和引用共享、目录、文件和元数据](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)。

### <a name="change-the-tier-of-an-azure-file-share"></a>更改 Azure 文件共享的层
常规用途 v2 (GPv2) 存储帐户中部署的文件共享可位于事务优化层、热层或冷层中。 可以随时更改 Azure 文件共享的层，同时需要支付上述相应事务费用。

# <a name="portal"></a>[Portal](#tab/azure-portal)
在主存储帐户页上，选择“文件共享”。选择标记为“文件共享”的磁贴（还可以通过存储帐户的目录导航到“文件共享”）  。

:::image type="content" source="media/storage-files-quick-create-use-windows/click-files.png" alt-text="屏幕截图：已选中存储帐户边栏选项卡中的“文件共享”。":::

在文件共享的表列表中，选择要为其更改层的文件共享。 在“文件共享概述”页上，在菜单中选择“更改层”。

![“文件共享概述”页的屏幕截图，其中突出显示了“更改层”按钮](media/storage-how-to-create-file-share/change-tier-0.png)

在生成的对话框中，选择所需的层：事务优化层、热层或冷层。

![“更改层”对话框的屏幕截图](media/storage-how-to-create-file-share/change-tier-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
以下 PowerShell cmdlet 假设你已按本文档前面部分所述设置了 `$resourceGroupName`、`$storageAccountName`、`$shareName` 变量。

```PowerShell
Update-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -AccessTier Cool
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
以下 Azure CLI 命令假设你已按本文档前面部分所述设置了 `$resourceGroupName`、`$storageAccountName` 和 `$shareName` 变量。

```azurecli
az storage share-rm update \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "Cool"
```

---

## <a name="next-steps"></a>后续步骤
- [规划 Azure 文件存储的部署](storage-files-planning.md)或[规划 Azure 文件同步的部署](../file-sync/file-sync-planning.md)。 
- [网络概述](storage-files-networking-overview.md)。
- 在 [Windows](storage-how-to-use-files-windows.md)、[macOS](storage-how-to-use-files-mac.md) 与 [Linux](storage-how-to-use-files-linux.md) 上连接和装载文件共享。
