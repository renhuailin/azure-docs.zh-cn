---
title: 创建 NFS 共享 - Azure 文件存储（预览）
description: 了解如何创建可使用网络文件系统协议装载的 Azure 文件共享。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/22/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: b085b9991175d8cd43e2dac0db80c5af4e703c34
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521231"
---
# <a name="how-to-create-an-nfs-share"></a>如何创建 NFS 共享
Azure 文件共享是位于云中的完全托管文件共享。 本文介绍如何创建可使用 NFS 协议的文件共享。 有关这两种协议的详细信息，请参阅 [Azure 文件共享协议](storage-files-compare-protocols.md)。

## <a name="limitations"></a>限制
[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>区域可用性
[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>先决条件
- 只能从受信任的网络访问 NFS 共享。 与 NFS 共享的连接必须来自以下源之一：
    - [创建专用终结点](storage-files-networking-endpoints.md#create-a-private-endpoint)（建议）或[限制对公共终结点的访问](storage-files-networking-endpoints.md#restrict-public-endpoint-access)。
    - [在 Linux 上配置点到站点 (P2S) VPN 以与 Azure 文件存储一起使用](storage-files-configure-p2s-vpn-linux.md)。
    - [配置站点到站点 VPN 以与 Azure 文件存储一起使用](storage-files-configure-s2s-vpn.md)。
    - 配置 [ExpressRoute](../../expressroute/expressroute-introduction.md)。

- 如果你打算使用 Azure CLI，请[安装最新版本](/cli/azure/install-azure-cli)。

## <a name="register-the-nfs-41-protocol"></a>注册 NFS 4.1 协议
如果你使用的是 Azure PowerShell 模块或 Azure CLI，请使用以下命令注册功能：

# <a name="portal"></a>[Portal](#tab/azure-portal)
使用 Azure PowerShell 或 Azure CLI 为 Azure 文件存储注册 NFS 4.1 功能。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
# Connect your PowerShell session to your Azure account, if you have not already done so.
Connect-AzAccount

# Set the actively selected subscription, if you have not already done so.
$subscriptionId = "<yourSubscriptionIDHere>"
$context = Get-AzSubscription -SubscriptionId $subscriptionId
Set-AzContext $context

# Register the NFS 4.1 feature with Azure Files to enable the preview.
Register-AzProviderFeature `
    -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowNfsFileShares 
    
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
# Connect your Azure CLI to your Azure account, if you have not already done so.
az login

# Provide the subscription ID for the subscription where you would like to 
# register the feature
subscriptionId="<yourSubscriptionIDHere>"

az feature register \
    --name AllowNfsFileShares \
    --namespace Microsoft.Storage \
    --subscription $subscriptionId

az provider register \
    --namespace Microsoft.Storage
```

---

注册审批可能需要一个小时。 若要验证注册是否完成，请使用以下命令：

# <a name="portal"></a>[Portal](#tab/azure-portal)
使用 Azure PowerShell 或 Azure CLI 检查 Azure 文件存储的 NFS 4.1 功能注册情况。 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
Get-AzProviderFeature `
    -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowNfsFileShares
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az feature show \
    --name AllowNfsFileShares \
    --namespace Microsoft.Storage \
    --subscription $subscriptionId
```

---

## <a name="create-a-filestorage-storage-account"></a>创建 FileStorage 存储帐户
目前，NFS 4.1 共享仅可用作高级文件共享。 若要部署支持 NFS 4.1 协议的高级文件共享，必须先创建一个 FileStorage 存储帐户。 存储帐户是 Azure 中的顶级对象，表示可用于部署多个 Azure 文件共享的共享存储池。

# <a name="portal"></a>[Portal](#tab/azure-portal)
要创建 FileStorage 存储帐户，请导航到 Azure 门户。

1. 在 Azure 门户中，选择左侧菜单中的“存储帐户”。

    ![Azure 门户主页 - 选择存储帐户](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

2. 在显示的“存储帐户”窗口中，选择“添加”。 
3. 选择要在其中创建存储帐户的订阅。
4. 选择要在其中创建存储帐户的资源组

5. 然后，输入存储帐户的名称。 所选名称在 Azure 中必须唯一。 该名称还必须为 3 到 24 个字符，并且只能包含数字和小写字母。
6. 选择存储帐户的位置或使用默认位置。
7. 对于“性能”，请选择“高级”。 

    必须选择“高级”，“FileStorage”才会成为“帐户类型”下拉列表中的可用选项。

8. 依次选择“帐户类型”、“FileStorage”。 
9. 将“复制”保留设置为默认值“本地冗余存储(LRS)”。 

    ![如何创建高级文件共享的存储帐户](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

10. 选择“查看+创建”可查看存储帐户设置并创建帐户。
11. 选择“创建” 。

创建存储帐户资源后，请导航到该资源。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
若要创建 FileStorage 存储帐户，请打开 PowerShell 提示符并执行以下命令，记住将 `<resource-group>` 和 `<storage-account>` 替换为你的环境的相应值。

```powershell
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"
$location = "westus2"

$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $location `
    -Kind FileStorage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
若要创建 FileStorage 存储帐户，请打开终端并执行以下命令，记住将 `<resource-group>` 和 `<storage-account>` 替换为你的环境的相应值。

```azurecli-interactive
resourceGroup="<resource-group>"
storageAccount="<storage-account>"
location="westus2"

az storage account create \
    --resource-group $resourceGroup \
    --name $storageAccount \
    --location $location \
    --sku Premium_LRS \
    --kind FileStorage
```
---

## <a name="create-an-nfs-share"></a>创建 NFS 共享

# <a name="portal"></a>[Portal](#tab/azure-portal)

现在，你已创建了一个 FileStorage 帐户并配置了网络，接下来可以创建一个 NFS 文件共享。 此过程类似于创建 SMB 共享，在创建共享时选择 NFS 而不是 SMB。

1. 导航到存储帐户，然后选择“文件共享”。
1. 选择“+ 文件共享”创建新的文件共享。
1. 为文件共享命名，选择预配的容量。
1. 对于“协议”，请选择“NFS (预览)”。
1. 对于“根 Squash”，在以下选项中进行选择。

    - 根 Squash (默认) - 远程超级用户（根）的访问映射到 UID (65534) 和 GID (65534)。
    - 无根 Squash - 远程超级用户（根）以 root 身份接收访问。
    - 所有 Squash - 所有用户访问映射到 UID (65534) 和 GID (65534)。
    
1. 选择“创建”。

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/create-nfs-file-share.png" alt-text="文件共享创建边栏选项卡的屏幕截图":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 确保已安装 .NET Framework。 请参阅[下载 .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework)。
 
1. 使用以下命令验证安装的 PowerShell 版本是否为 `5.1` 或以上。    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   若要升级 PowerShell 版本，请参阅[升级现有的 Windows PowerShell](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell)
    
1. 安装最新版本的 PowershellGet 模块。

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

1. 关闭 PowerShell 控制台，然后重新将其打开。

1. 安装 Az.Storage 预览模块版本 2.5.2-preview。

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   有关如何安装 PowerShell 模块的详细信息，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)
   
1. 若要使用 Azure PowerShell 模块创建高级文件共享，请使用 [New-AzRmStorageShare](/powershell/module/az.storage/new-azrmstorageshare) cmdlet。

    > [!NOTE]
    > 高级文件共享采用预配模型计费。 共享的预配大小由下方的 `QuotaGiB` 指定。 有关详细信息，请参阅[了解预配模型](understanding-billing.md#provisioned-model)和 [Azure 文件存储定价页](https://azure.microsoft.com/pricing/details/storage/files/)。

    ```powershell
    New-AzRmStorageShare `
        -StorageAccount $storageAccount `
        -Name myshare `
        -EnabledProtocol NFS `
        -RootSquash RootSquash `
        -QuotaGiB 1024
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
若要使用 Azure CLI 创建高级文件共享，请使用 [az storage share create](/cli/azure/storage/share-rm) 命令。

> [!NOTE]
> 高级文件共享采用预配模型计费。 共享的预配大小由下方的 `quota` 指定。 有关详细信息，请参阅[了解预配模型](understanding-billing.md#provisioned-model)和 [Azure 文件存储定价页](https://azure.microsoft.com/pricing/details/storage/files/)。

```azurecli-interactive
az storage share-rm create \
    --resource-group $resourceGroup \
    --storage-account $storageAccount \
    --name "myshare" \
    --enabled-protocol NFS \
    --root-squash RootSquash \
    --quota 1024
```
---

## <a name="next-steps"></a>后续步骤
现在，你已创建了一个 NFS 共享，要使用它，必须将它装载到 Linux 客户端上。 有关详细信息，请参阅[如何装载 NFS 共享](storage-files-how-to-mount-nfs-shares.md)。

如果遇到任何问题，请参阅[解决 Azure NFS 文件共享问题](storage-troubleshooting-files-nfs.md)。