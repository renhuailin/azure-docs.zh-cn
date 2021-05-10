---
title: 启用和创建大型文件共享 - Azure 文件存储
description: 本文介绍如何启用和创建大型文件共享。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/20/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: a53f964020583c41e2400d97ad244bacd33813bc
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818253"
---
# <a name="enable-and-create-large-file-shares"></a>启用和创建大型文件共享
在存储帐户上启用大型文件共享后，Azure 文件共享最多可以纵向扩展到 100 TiB。 启用大型文件共享时，还可能增加文件共享的 IOPS 和吞吐量限制。 你也可以在现有存储帐户中为现有的和新的文件共享实现此扩展。 有关性能差异的详细信息，请参阅[文件共享和文件缩放目标](storage-files-scale-targets.md#azure-files-scale-targets)。

## <a name="prerequisites"></a>先决条件
- 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
- 如果你打算使用 Azure CLI，请[安装最新版本](/cli/azure/install-azure-cli)。
- 如果你打算使用 Azure PowerShell 模块，请[安装最新版本](/powershell/azure/install-az-ps)。

## <a name="restrictions"></a>限制
目前，只能在启用了大型文件共享的存储帐户中使用本地冗余存储 (LRS) 或区域冗余存储 (ZRS)。 不能使用异地区域冗余存储 (GZRS)、异地冗余存储 (GRS)、读取访问异地冗余存储 (RA-GRS) 或读取访问异地区域冗余存储 (RA-GZRS)。

在帐户中启用大型文件共享是一个不可逆的过程。 启用后，无法将帐户转换为 GZRS、GRS、RA-GRS 或 RA-GZRS。

## <a name="create-a-new-storage-account"></a>新建存储帐户

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在 Azure 门户中，选择“所有服务”。 
1. 在资源列表中输入“存储帐户”。 键入时，列表会根据输入的内容进行筛选。 选择“存储帐户”。
1. 在显示的“存储帐户”边栏选项卡中选择“+ 新建”。 
1. 在“基本信息”边栏选项卡中填写所需的选择。
1. 确保将“性能”设置为“标准” 。
1. 将“冗余”设置为“本地冗余存储”或“区域冗余存储”  。
1. 选择“高级”边栏选项卡，然后选择“大型文件共享”右侧的“启用”选项按钮。  
1. 选择“查看+创建”可查看存储帐户设置并创建帐户。
1. 选择“创建”  。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
若要创建启用了大文件共享的存储帐户，请使用以下命令。 请将 `<yourStorageAccountName>`、`<yourResourceGroup>` 和 `<yourDesiredRegion>` 替换为自己的信息。

```powershell
# This command creates a large file share–enabled account. It will not support changing the 
# redundancy to GRS, GZRS, RA-GRS, or RA-GZRS.
New-AzStorageAccount `
    -ResourceGroupName <yourResourceGroup> `
    -Name <yourStorageAccountName> `
    -Location <yourDesiredRegion> `
    -SkuName Standard_LRS `
    -EnableLargeFileShare
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
若要创建启用了大文件共享的存储帐户，请使用以下命令。 请将 `<yourStorageAccountName>`、`<yourResourceGroup>` 和 `<yourDesiredRegion>` 替换为自己的信息。

```azurecli-interactive
# This command creates a large file share–enabled account. It will not support changing the 
# redundancy to GRS, GZRS, RA-GRS, or RA-GZRS.
az storage account create \
    --name <yourStorageAccountName> \
    -g <yourResourceGroup> \
    -l <yourDesiredRegion> \
    --sku Standard_LRS \
    --kind StorageV2 \
    --enable-large-file-share
```

---

创建启用了大型文件共享的存储帐户后，可以创建利用大容量和规模限制的文件共享。 有关如何创建存储帐户和 Azure 文件共享的详细信息，请参阅[创建 Azure 文件共享](storage-how-to-create-file-share.md)。

## <a name="enable-large-files-shares-on-an-existing-account"></a>在现有帐户中启用大型文件共享
也可以在现有 LRS 和 ZRS 存储帐户中启用大型文件共享。 如果具有 GRS、GZRS、RA-GRS 或 RA-GZRS 帐户，则需要将其转换为 LRS 帐户，然后再继续。

# <a name="portal"></a>[门户](#tab/azure-portal)
1. 打开 [Azure 门户](https://portal.azure.com)，并导航到要在其中启用大型文件共享的存储帐户。
1. 打开存储帐户，然后选择“文件共享”。
1. 选择“大型文件共享”对应的“已启用”，然后选择“保存”。  
1. 依次选择“概述”、“刷新”。 
1.   选择“共享容量”，然后选择“100 TiB”和“保存”。

    :::image type="content" source="media/storage-files-how-to-create-large-file-share/files-enable-large-file-share-existing-account.png" alt-text="Azure 存储帐户和突出显示了 100 TiB 共享的“文件共享”边栏选项卡的屏幕截图。":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
若要在现有帐户中启用大型文件共享，请使用以下命令。 请将 `<yourStorageAccountName>` 和 `<yourResourceGroup>` 替换为自己的信息。

```powershell
Set-AzStorageAccount `
    -ResourceGroupName <yourResourceGroup> `
    -Name <yourStorageAccountName> `
    -EnableLargeFileShare
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
若要在现有帐户中启用大型文件共享，请使用以下命令。 请将 `<yourStorageAccountName>` 和 `<yourResourceGroup>` 替换为自己的信息。

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

---

现已在存储帐户中启用大型文件共享。 接下来，必须[更新现有共享的配额](#expand-existing-file-shares)才能利用提高的容量和规模。 

> [!Important]  
> 除非更改了配额，否则现有文件共享不会纵向扩展到大型文件共享的播发限制。

## <a name="expand-existing-file-shares"></a>扩展现有的文件共享
在存储帐户上启用了大型文件共享后，必须扩展该存储帐户中的现有文件共享，才能利用增加的容量和规模。 

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. 在存储帐户中选择“文件共享”。
1. 右键单击文件共享并选择“配额”。
1. 输入所需的新大小，然后选择“确定”。

![显示了现有文件共享配额的 Azure 门户 UI](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
若要将配额设置为最大大小，请使用以下命令。 请将 `<YourResourceGroupName>`、`<YourStorageAccountName>` 和 `<YourStorageAccountFileShareName>` 替换为自己的信息。

```powershell
$resourceGroupName = "<YourResourceGroupName>"
$storageAccountName = "<YourStorageAccountName>"
$shareName="<YourStorageAccountFileShareName>"

# update quota
Set-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -QuotaGiB 102400
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
若要将配额设置为最大大小，请使用以下命令。 请将 `<yourResourceGroupName>`、`<yourStorageAccountName>` 和 `<yourFileShareName>` 替换为自己的信息。

```azurecli-interactive
az storage share-rm update \
    --resource-group <yourResourceGroupName> \
    --storage-account <yourStorageAccountName> \
    --name <yourFileShareName> \
    --quota 102400
```

---

## <a name="next-steps"></a>后续步骤
* [在 Windows 上连接并装载文件共享](storage-how-to-use-files-windows.md)
* [在 Linux 上连接并装载文件共享](storage-how-to-use-files-linux.md)
* [在 macOS 上连接并装载文件共享](storage-how-to-use-files-mac.md)