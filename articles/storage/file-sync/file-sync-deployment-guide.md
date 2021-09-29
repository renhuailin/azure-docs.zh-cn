---
title: 部署 Azure 文件同步 | Microsoft Docs
description: 了解如何使用 Azure 门户、PowerShell 或 Azure CLI 从头到尾部署 Azure 文件同步。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 75e8b7482e9d810caf0afb0818d53df141908708
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128609263"
---
# <a name="deploy-azure-file-sync"></a>部署 Azure 文件同步

使用 Azure 文件同步，即可将组织的文件共享集中在 Azure 文件中，同时又不失本地文件服务器的灵活性、性能和兼容性。 Azure 文件同步可将 Windows Server 转换为 Azure 文件共享的快速缓存。 可以使用 Windows Server 上可用的任意协议本地访问数据，包括 SMB、NFS 和 FTPS。 并且可以根据需要在世界各地具有多个缓存。

强烈建议先阅读[规划 Azure 文件部署](../files/storage-files-planning.md)和[规划 Azure 文件同步部署](file-sync-planning.md)，再按照本文中的步骤进行操作。

## <a name="prerequisites"></a>先决条件

# <a name="portal"></a>[门户](#tab/azure-portal)

1. 要部署 Azure 文件同步的同一区域中的 Azure 文件共享。有关详细信息，请参阅：
    - Azure 文件同步的[适用地区](file-sync-planning.md#azure-file-sync-region-availability)。
    - [创建文件共享](../files/storage-how-to-create-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)，了解创建文件共享的分步说明。
1. 至少有一个受支持的 Windows Server 或 Windows Server 群集实例与 Azure 文件同步来同步。有关受支持的 Windows Server 版本和建议系统资源的详细信息，请参阅 [Windows 文件服务器注意事项](file-sync-planning.md#windows-file-server-considerations)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 要部署 Azure 文件同步的同一区域中的 Azure 文件共享。有关详细信息，请参阅：
    - Azure 文件同步的[适用地区](file-sync-planning.md#azure-file-sync-region-availability)。
    - [创建文件共享](../files/storage-how-to-create-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)，了解创建文件共享的分步说明。
1. 至少有一个受支持的 Windows Server 或 Windows Server 群集实例与 Azure 文件同步来同步。有关受支持的 Windows Server 版本和建议系统资源的详细信息，请参阅 [Windows 文件服务器注意事项](file-sync-planning.md#windows-file-server-considerations)。

1. Az PowerShell 模块可与 PowerShell 5.1 或 PowerShell 6 及以上版本配合使用。 可以在任何受支持的系统（包括非 Windows 系统）上使用 Azure 文件同步的 Az PowerShell 模块，但服务器注册 cmdlet 必须始终在要注册的 Windows 服务器实例上运行（这可以直接完成或通过 PowerShell 远程完成）。 在 Windows Server 2012 R2 上，可通过查看 $PSVersionTable 对象的“PSVersion”属性值来验证是否至少正在运行 PowerShell 5.1.\* ：

    ```powershell
    $PSVersionTable.PSVersion
    ```

    与 Windows Server 2012 R2 的全新安装一样，如果 PSVersion 值低于 5.1.，可通过下载并安装 [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616) 轻松升级\*。 需下载和安装的 Windows Server 2012 R2 的相应包为 Win8.1AndW2K12R2-KB\*\*\*\*\*\*\*-x64.msu。

    PowerShell 6 及以上版本可用于任何受支持的系统，并且可以通过其 [GitHub 页面](https://github.com/PowerShell/PowerShell#get-powershell)下载。

    > [!IMPORTANT]
    > 如果计划使用服务器注册 UI，而不是直接从 PowerShell 注册，则必须使用 PowerShell 5.1。

1. 如果已选择使用 PowerShell 5.1，请确保至少安装了 .NET 4.7.2。 详细了解系统上的 [.NET Framework 版本和依赖项](/dotnet/framework/migration-guide/versions-and-dependencies)。

    > [!IMPORTANT]
    > 如果在 Windows Server Core 上安装 .NET 4.7.2 及以上版本，则必须用 `quiet` 和 `norestart` 标志安装，否则安装将失败。 例如，如果安装 .NET 4.8，则命令将如下所示：
    > ```PowerShell
    > Start-Process -FilePath "ndp48-x86-x64-allos-enu.exe" -ArgumentList "/q /norestart" -Wait
    > ```

1. Az PowerShell 模块可以按照此处的说明进行安装：[安装和配置 Azure PowerShell](/powershell/azure/install-Az-ps)。

    > [!NOTE]
    > 现在，安装 Az PowerShell 模块时将自动安装 Az.StorageSync 模块。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 要部署 Azure 文件同步的同一区域中的 Azure 文件共享。有关详细信息，请参阅：
    - Azure 文件同步的[适用地区](file-sync-planning.md#azure-file-sync-region-availability)。
    - [创建文件共享](../files/storage-how-to-create-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)，了解创建文件共享的分步说明。
1. 至少有一个受支持的 Windows Server 或 Windows Server 群集实例与 Azure 文件同步来同步。有关受支持的 Windows Server 版本和建议系统资源的详细信息，请参阅 [Windows 文件服务器注意事项](file-sync-planning.md#windows-file-server-considerations)。

1. [安装 Azure CLI](/cli/azure/install-azure-cli)

   如果愿意，还可以使用 Azure Cloud Shell 来完成本教程中的步骤。  Azure Cloud Shell 是一种可以通过浏览器使用的交互式 shell 环境。  使用下列方法之一开始使用 Cloud Shell：

   - 选择代码块右上角的“试用”。 “试用”将打开 Azure Cloud Shell，但不会自动将代码复制到 Cloud Shell。

   - 通过转到 [https://shell.azure.com](https://shell.azure.com) 打开 Cloud Shell

   - 选择 [Azure 门户](https://portal.azure.com)右上角菜单栏上的 Cloud Shell 按钮

1. 登录。

   如果使用的是 CLI 的本地安装，请使用 [az login](/cli/azure/reference-index#az_login) 命令登录。

   ```azurecli
   az login
   ```

    遵循终端中显示的步骤完成身份验证过程。

1. 安装 [az filesync](/cli/azure/storagesync) Azure CLI 扩展。

   ```azurecli
   az extension add --name storagesync
   ```

   安装 storagesync 扩展引用后，你将收到以下警告。

   ```output
   The installed extension 'storagesync' is experimental and not covered by customer support. Please use with discretion.
   ```

---

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>准备 Windows Server，用于 Azure 文件同步

对于要与 Azure 文件同步配合使用的每个服务器（包括故障转移群集中的服务器节点），请禁用“Internet Explorer 增强的安全性配置”。 只需在最初注册服务器时禁用。 可在注册服务器后重新启用。

# <a name="portal"></a>[门户](#tab/azure-portal)

> [!NOTE]
> 如果要在 Windows Server Core 上部署 Azure 文件同步，则可以跳过此步骤。

1. 打开服务器管理器。
2. 单击“本地服务器”：  
    ![服务器管理器 UI 左侧的“本地服务器”](media/storage-sync-files-deployment-guide/prepare-server-disable-ieesc-part-1.png)
3. 在“属性”子窗格上，选择“IE 增强的安全性配置”的链接。  
    ![服务器管理器 UI 中的“IE 增强的安全性配置”窗格](media/storage-sync-files-deployment-guide/prepare-server-disable-ieesc-part-2.png)
4. 对于“Internet Explorer 增强的安全性配置”对话框中的“管理员”和“用户”，都选择“关”   ：  
    ![选定“关”的“Internet Explorer 增强的安全性配置”弹出窗口](media/storage-sync-files-deployment-guide/prepare-server-disable-ieesc-part-3.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要禁用“Internet Explorer 增强的安全性配置”，请在权限提升的 PowerShell 会话中执行以下命令：

```powershell
$installType = (Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\").InstallationType

# This step is not required for Server Core
if ($installType -ne "Server Core") {
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Administrators
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force

    # Disable Internet Explorer Enhanced Security Configuration 
    # for Users
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A8-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force

    # Force Internet Explorer closed, if open. This is required to fully apply the setting.
    # Save any work you have open in the IE browser. This will not affect other browsers,
    # including Microsoft Edge.
    Stop-Process -Name iexplore -ErrorAction SilentlyContinue
}
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

按照 Azure 门户或 PowerShell 的说明进行操作。

---

## <a name="deploy-the-storage-sync-service"></a>部署存储同步服务

Azure 文件同步的部署过程首先会将一个“存储同步服务”资源放入所选订阅的资源组中。 我们建议尽量少预配这些资源。 将在服务器与此资源之间创建信任关系，一个服务器只能注册到一个存储同步服务。 因此，我们建议根据需要部署尽量多的存储同步服务，以隔离服务器组。 请记住，不同存储同步服务中的服务器不能彼此同步。

> [!NOTE]
> 存储同步服务已从其部署到的订阅和资源组继承访问权限。 我们建议仔细检查谁有权访问该服务。 具有写访问权限的实体可以开始从已注册到此存储同步服务的服务器同步新的文件集，使数据流向这些实体可以访问的 Azure 存储。

# <a name="portal"></a>[门户](#tab/azure-portal)

若要部署存储同步服务，请转到 [Azure 门户](https://portal.azure.com/)，单击“创建资源”，然后搜索 Azure 文件同步。在搜索结果中，选择“Azure 文件同步”，然后选择“创建”，打开“部署存储同步”选项卡  。

在打开的窗格中，输入以下信息：

- 名称：存储同步服务的唯一名称（按区域）。
- 订阅：需要在其中创建存储同步服务的订阅。 根据组织的配置策略，可能有权访问一个或多个订阅。 Azure 订阅是对每项云服务（如 Azure 文件）计费的最基本容器。
- 资源组：资源组是 Azure 资源（如存储帐户或存储同步服务）的逻辑组。 可为 Azure 文件同步创建新的资源组，也可对其使用现有资源组。（建议使用资源组作为用于从逻辑上隔离组织资源的容器，例如对 HR 资源或特定项目的资源进行分组。）
- **位置**：要在其中部署 Azure 文件同步的区域。此列表中仅提供受支持的区域。

完成后，选择“创建”部署存储同步服务。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用自己的值替换 `<Az_Region>`、`<RG_Name>` 和 `<my_storage_sync_service>`，然后使用以下命令创建并部署存储同步服务：

```powershell
$hostType = (Get-Host).Name

if ($installType -eq "Server Core" -or $hostType -eq "ServerRemoteHost") {
    Connect-AzAccount -UseDeviceAuthentication
}
else {
    Connect-AzAccount
}

# this variable holds the Azure region you want to deploy 
# Azure File Sync into
$region = '<Az_Region>'

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = @()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the selected Azure Region or the region is mistyped.")
}

# the resource group to deploy the Storage Sync Service into
$resourceGroup = '<RG_Name>'

# Check to ensure resource group exists and create it if doesn't
$resourceGroups = @()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    New-AzResourceGroup -Name $resourceGroup -Location $region
}

$storageSyncName = "<my_storage_sync_service>"
$storageSync = New-AzStorageSyncService -ResourceGroupName $resourceGroup -Name $storageSyncName -Location $region
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

按照 Azure 门户或 PowerShell 的说明进行操作。

---

## <a name="install-the-azure-file-sync-agent"></a>安装 Azure 文件同步代理

Azure 文件同步代理是一个可下载包，可实现 Windows 服务器与 Azure 文件共享的同步。

# <a name="portal"></a>[门户](#tab/azure-portal)

可从 [Microsoft 下载中心](https://go.microsoft.com/fwlink/?linkid=858257)下载代理。 下载完成后，双击 MSI 包，开始安装 Azure 文件同步代理。

> [!IMPORTANT]
> 如果要对故障转移群集使用 Azure 文件同步，则 必须在群集中的每个节点上安装 Azure 文件同步代理。 必须注册群集中的每个节点才能使用 Azure 文件同步。

建议执行以下操作：
- 保留默认安装路径(C:\Program Files\Azure\StorageSyncAgent)，以简化故障排除和服务器维护。
- 启用 Microsoft 更新，使 Azure 文件同步保持最新。 Azure 文件同步代理的所有更新（包括功能更新和修补程序）都可从 Microsoft 更新进行。 建议将最新更新安装到 Azure 文件同步。有关详细信息，请参阅 [Azure 文件同步更新策略](file-sync-planning.md#azure-file-sync-agent-update-policy)。

Azure 文件同步代理安装完成后，服务器注册 UI 自动打开。 在注册之前，必须创建存储同步服务；请参阅下一部分了解如何创建存储同步服务。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

执行以下 PowerShell 代码，以下载适用于所用 OS 的 Azure 文件同步代理版本，并将其安装在系统上。

> [!IMPORTANT]
> 如果要对故障转移群集使用 Azure 文件同步，则 必须在群集中的每个节点上安装 Azure 文件同步代理。 必须注册群集中的每个节点才能使用 Azure 文件同步。

```powershell
# Gather the OS version
$osver = [System.Environment]::OSVersion.Version

# Download the appropriate version of the Azure File Sync agent for your OS.
if ($osver.Equals([System.Version]::new(10, 0, 17763, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2019 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(10, 0, 14393, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2016 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(6, 3, 9600, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2012R2 `
        -OutFile "StorageSyncAgent.msi" 
} else {
    throw [System.PlatformNotSupportedException]::new("Azure File Sync is only supported on Windows Server 2012 R2, Windows Server 2016, and Windows Server 2019")
}

# Install the MSI. Start-Process is used to PowerShell blocks until the operation is complete.
# Note that the installer currently forces all PowerShell sessions closed - this is a known issue.
Start-Process -FilePath "StorageSyncAgent.msi" -ArgumentList "/quiet" -Wait

# Note that this cmdlet will need to be run in a new session based on the above comment.
# You may remove the temp folder containing the MSI and the EXE installer
Remove-Item -Path ".\StorageSyncAgent.msi" -Recurse -Force
```


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

按照 Azure 门户或 PowerShell 的说明进行操作。

---

## <a name="register-windows-server-with-storage-sync-service"></a>向存储同步服务注册 Windows Server

向存储同步服务注册 Windows Server 可在服务器（或群集）与存储同步服务之间建立信任关系。 一个服务器只能注册到一个存储同步服务，并可与同一存储同步服务关联的其他服务器和 Azure 文件共享同步。

> [!NOTE]
> 服务器注册使用你的 Azure 凭据在存储同步服务与 Windows Server 之间创建信任关系，但是，服务器随后会创建并使用自身有效的标识，前提是该服务器保持已注册状态，并且当前的共享访问签名令牌（存储 SAS）有效。 取消注册服务器后，无法将新的 SAS 令牌颁发给服务器，因此，服务器无法访问 Azure 文件共享，并停止任何同步。

注册服务器的管理员必须是给定存储同步服务的管理角色“所有者”或“参与者”的成员 。 这可以在 Azure 门户中的“访问控制(IAM)”下针对存储同步服务进行配置。

此外，还可以区分管理员将服务器注册到允许的服务器，也可以在存储同步服务中配置同步。 为此，需要创建一个自定义角色，列出只允许注册服务器的管理员，并为自定义角色提供以下权限：

- “Microsoft.StorageSync/storageSyncServices/registeredServers/write”
- “Microsoft.StorageSync/storageSyncServices/read”
- “Microsoft.StorageSync/storageSyncServices/workflows/read”
- “Microsoft.StorageSync/storageSyncServices/workflows/operations/read”

# <a name="portal"></a>[Portal](#tab/azure-portal)

服务器注册 UI 应在 Azure 文件同步代理安装后自动打开。 如果没有打开，可以手动从其文件位置 C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe 打开。 服务器注册 UI 打开时，请选择“登录”开始操作。

登录后，系统会提示输入以下信息：

![服务器注册 UI 的屏幕快照](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- Azure 订阅：包含存储同步服务的订阅（请参阅[部署存储同步服务](#deploy-the-storage-sync-service)）。
- 资源组：包含存储同步服务的资源组。
- 存储同步服务：想要向其注册的存储同步服务的名称。

选择相应的信息之后，选择“注册”完成服务器注册。 在注册过程中，系统会提示进行其他登录。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)


```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

按照 Azure 门户或 PowerShell 的说明进行操作。

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>创建同步组和云终结点

同步组定义一组文件的同步拓扑。 同步组中的终结点保持彼此同步。 同步组中必须包含一个表示 Azure 文件共享的云终结点，以及一个或多个服务器终结点。 服务器终结点表示已注册的服务器上的路径。 服务器可以包含多个同步组中的服务器终结点。 可以创建任意数量的同步组，以适当地描述所需的同步拓扑。

云终结点是指向 Azure 文件共享的指针。 所有服务器终结点将与某个云终结点同步，使该云终结点成为中心。 Azure 文件共享的存储帐户必须位于存储同步服务所在的同一个区域。 将同步整个 Azure 文件共享，但存在一种例外情况：将预配一个特殊的文件夹，它相当于 NTFS 卷上的“System Volume Information”隐藏文件夹。 此目录名为“.SystemShareInformation”。 其中包含不会同步到其他终结点的重要同步元数据。 请不要使用或删除它！

> [!IMPORTANT]
> 可对同步组中的任何云终结点或服务器终结点进行更改，并将文件同步到同步组中的其他终结点。 如果直接对云终结点（Azure 文件分享）进行更改，首先需要通过 Azure 文件同步更改检测作业来发现更改。 每 24 小时仅针对云终结点启动一次更改检测作业。 有关详细信息，请参阅 [Azure 文件常见问题解答](../files/storage-files-faq.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json#afs-change-detection)。

创建云终结点的管理员必须是存储帐户（其中包含云终结点指向的 Azure 文件共享）的管理角色“所有者”的成员。 这可以在 Azure 门户中的“访问控制(IAM)”下针对存储帐户进行配置。

# <a name="portal"></a>[门户](#tab/azure-portal)

要创建同步组，请在 [Azure 门户](https://portal.azure.com/)中转到存储同步服务，然后选择“+ 同步组”：

![在 Azure 门户中创建新的同步组](media/storage-sync-files-deployment-guide/create-sync-group-part-1.png)

在打开的窗格中输入以下信息，创建具有云终结点的同步组：

- **同步组名称**：要创建的同步组的名称。 此名称在存储同步服务内必须是唯一的，但可以是符合逻辑的任何名称。
- 订阅：在[部署存储同步服务](#deploy-the-storage-sync-service)中用于部署存储同步服务的订阅。
- 存储帐户：如果选择“选择存储账户”，另一个窗格随即出现，可在其中选择包含要同步的 Azure 文件共享的存储帐户。
- **Azure 文件共享**：要与其同步的 Azure 文件共享的名称。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要创建同步组，请执行以下 PowerShell。 请记得将 `<my-sync-group>` 替换为同步组的所需名称。

```powershell
$syncGroupName = "<my-sync-group>"
$syncGroup = New-AzStorageSyncGroup -ParentObject $storageSync -Name $syncGroupName
```

成功创建同步组后，可以创建云终结点。 请务必将 `<my-storage-account>` 和 `<my-file-share>` 替换为预期的值。

```powershell
# Get or create a storage account with desired name
$storageAccountName = "<my-storage-account>"
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup | Where-Object {
    $_.StorageAccountName -eq $storageAccountName
}

if ($storageAccount -eq $null) {
    $storageAccount = New-AzStorageAccount `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        -Location $region `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly:$true
}

# Get or create an Azure file share within the desired storage account
$fileShareName = "<my-file-share>"
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    $fileShare = New-AzStorageShare -Context $storageAccount.Context -Name $fileShareName
}

# Create the cloud endpoint
New-AzStorageSyncCloudEndpoint `
    -Name $fileShare.Name `
    -ParentObject $syncGroup `
    -StorageAccountResourceId $storageAccount.Id `
    -AzureFileShareName $fileShare.Name
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 [az storagesync sync-group](/cli/azure/storagesync/sync-group#az_storagesync_sync_group_create) 命令创建新的同步组。  若要为所有 CLI 命令默认设置资源组，请使用 [az configure](/cli/azure/reference-index#az_configure)。

```azurecli
az storagesync sync-group create --resource-group myResourceGroupName \
                                 --name myNewSyncGroupName \
                                 --storage-sync-service myStorageSyncServiceName \
```

使用 [az storagesync sync-group cloud-endpoint](/cli/azure/storagesync/sync-group/cloud-endpoint#az_storagesync_sync_group_cloud_endpoint_create) 命令创建新的云终结点。

```azurecli
az storagesync sync-group cloud-endpoint create --resource-group myResourceGroup \
                                                --storage-sync-service myStorageSyncServiceName \
                                                --sync-group-name mySyncGroupName \
                                                --name myNewCloudEndpointName \
                                                --storage-account mystorageaccountname \
                                                --azure-file-share-name azure-file-share-name
```

---

## <a name="create-a-server-endpoint"></a>创建服务器终结点

服务器终结点代表已注册服务器上的特定位置，例如服务器卷中的文件夹。 服务器终结点受以下条件约束：

- 服务器终结点必须是已注册的服务器（而不是装载式共享）上的路径。 不支持网络附加存储 (NAS)。
- 尽管服务器终结点可以位于系统卷上，但系统卷上的服务器终结点可能不会使用云分层。
- 不支持在卷上创建服务器终结点后更改路径或驱动器号。 请确保在已注册的服务器上使用最终路径。
- 已注册的服务器可支持多个服务器终结点，但在任何给定时间，一个同步组中每个已注册的服务器只能有一个服务器终结点。 同步组中的其他服务器终结点必须位于不同的已注册的服务器上。

[!INCLUDE [storage-files-sync-create-server-endpoint](../../../includes/storage-files-sync-create-server-endpoint.md)]

## <a name="configure-firewall-and-virtual-network-settings"></a>配置防火墙和虚拟网络设置

### <a name="portal"></a>门户

如果要将 Azure 文件同步配置为使用防火墙和虚拟网络设置，请执行以下操作：

1. 从 Azure 门户导航到要保护的存储帐户。
1. 在左侧菜单中选择“网络”。
1. 在“允许的访问来源”下，选择“所选网络” 。
1. 确保服务器 IP 或虚拟网络列在“地址范围”部分下。
1. 确保勾选“允许受信任的 Microsoft 服务访问此存储帐户”。
1. 选择“保存”以保存设置。

    ![配置防火墙和虚拟网络设置以使用 Azure 文件同步](media/storage-sync-files-deployment-guide/firewall-and-vnet.png)

## <a name="onboarding-with-azure-file-sync"></a>使用 Azure 文件同步进行载入

若要通过 Azure 文件同步在不停机的情况下首次进行载入，同时保持完整的文件保真度和访问控制列表 (ACL)，则建议采用的步骤如下所述：

1. 部署存储同步服务。
1. 创建一个同步组。
1. 在包含完整数据集的服务器上安装 Azure 文件同步代理。
1. 注册该服务器并在共享中创建一个服务器终结点。
1. 让同步服务执行到 Azure 文件共享（云终结点）的完整上传。
1. 在初始上传完成后，在剩余的每台服务器上安装 Azure 文件同步代理。
1. 在剩余的每台服务器上创建新的文件共享。
1. 使用云分层策略在新的文件共享中创建服务器终结点（如果需要）。 （此步骤要求有额外的存储可供初始设置使用。）
1. 让 Azure 文件同步代理在不实际传输数据的情况下快速还原整个命名空间。 在完成完整的命名空间同步后，同步引擎将根据服务器终结点的云分层策略填充本地磁盘空间。
1. 确保同步完成，并根据需要测试拓扑。
1. 将用户和应用程序重定向到此新共享。
1. 还可以选择删除服务器上任何重复的共享。

如果没有可用于初始载入的额外存储空间，并且希望附加到现有的共享，则可以在 Azure 文件共享中预先播种数据。 当且仅当可以接受停机并且绝对可以保证在初始载入过程中服务器共享上不会发生数据更改时，才建议使用此方法。

1. 确保任何服务器上的数据在载入过程中都不会发生更改。
1. 使用基于 SMB 的任何数据传输工具在 Azure 文件共享中预先播种服务器数据。 例如，Robocopy。 还可以使用 AzCopy 而不是 REST。 请确保将 AzCopy 与适当的开关一起使用，以保留 ACL 时间戳和特性。
1. 使用所需的指向现有共享的服务器终结点创建 Azure 文件同步拓扑。
1. 让同步服务在所有终结点上完成对帐过程。
1. 在对帐完成后，你可以打开共享进行更改。

预先播种方法当前有几个限制 -
- 在同步拓扑完全启动并运行之前更改服务器上的数据可能会导致服务器终结点上发生冲突。
- 创建云终结点后，在开始初始同步之前，Azure 文件同步会运行一个进程来检测云中的文件。完成此进程所花的时间取决于各种因素，如网络速度、可用带宽以及文件和文件夹的数目。 对于预览版，粗略估计，检测流程以大约每秒 10 个文件的速度运行。因此，当在云中预先播种数据时，即使预先播种运行速度很快，获得完全运行的系统所需的总体时间也会更长。

## <a name="self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service"></a>通过早期版本和 VSS（卷影复制服务）进行自助式还原

> [!IMPORTANT]
> 以下信息只能用于版本 9（或以上）的存储同步代理。 低于 9 的版本将不具有 StorageSyncSelfService cmdlets。

以前的版本是一项 Windows 功能，可用于利用卷的服务器端 VSS 快照将文件的可恢复版本提供给 SMB 客户端。
这将直接为信息工作者（而不是依赖 IT 管理员的还原）提供一种功能强大的方案（通常称为自助还原）。

VSS 快照和先前版本的工作方式与 Azure 文件同步无关。但是，必须将云分层设置为兼容模式。 许多 Azure 文件同步服务器终结点可存在于同一个卷上。 即使相关卷仅具有一个你计划或正在使用云分层的服务器终结点，都必须对每个卷进行以下 PowerShell 调用。

```powershell
Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'
Enable-StorageSyncSelfServiceRestore [-DriveLetter] <string> [[-Force]] 
```

VSS 快照针对整个卷进行。 默认情况下，如果有足够的空间来存储快照，一个给定的卷最多可以存在 64 份快照。 VSS 会自动处理这种情况。 从星期一到星期五，默认快照计划每天创建两份快照。 可以通过 Windows 计划任务来配置该计划。 上述 PowerShell cmdlet 执行两项操作：
1. 将指定卷上的 Azure 文件同步云分层配置为与以前的版本兼容，并确保可以从以前的版本中还原文件（即使已将该文件分层到服务器上的云中）。
1. 启用默认 VSS 计划。 可以决定稍后对其进行修改。

> [!NOTE]
> 此处需要注意两个要点：
> - 如果使用 - Force 参数，且当前启用了 VSS，则它将覆盖当前 VSS 快照计划，并将其替换为默认计划。 确保在运行 cmdlet 之前保存自定义配置。
> - 如果在群集节点上使用此 cmdlet，则还必须在群集中的所有其他节点上运行该 cmdlet！

若要查看是否已启用自助式还原兼容性，可运行以下 cmdlet。

```powershell
Get-StorageSyncSelfServiceRestore [[-Driveletter] <string>]
```

它将列出服务器上的所有卷，以及每个卷的云分层兼容天数。 此数字根据每个卷的最大可能快照数和默认快照计划自动计算。 因此，默认情况下，提供给信息工作者的所有先前版本均可用于还原。 如果更改默认计划以创建更多快照，则情况也是如此。
但是，如果更改计划的方式导致该卷上的可用快照存在时限超过兼容天数值，则用户将无法使用该旧版的快照（以前的版本）进行还原。

> [!NOTE]
> 启用自助式还原可能会影响 Azure 存储使用和帐单。 此影响仅限于服务器上当前分层的文件。 启用此功能可确保云中提供的文件版本可通过早期版本（VSS 快照）条目进行引用。
>
> 如果禁用该功能，则 Azure 存储使用量将慢慢降低，直到兼容天数过去。 对此，无法进行加速。

每个卷的 VSS 快照的默认最大数 (64) 以及获取这些快照的默认计划，导致信息工作者最多可以还原 45 天的先前版本，具体取决于可在卷上存储的 VSS 快照数量。

如果每个卷最多 64 个 VSS 快照并非适合你的正确设置，则可[通过注册表项更改该值](/windows/win32/backup/registry-keys-for-backup-and-restore#maxshadowcopies)。
为了使新限制生效，你需要重新运行 cmdlet，以在以前启用的每个卷上启用以前的版本兼容性，并使用 -Force 标志将每个卷新的最大 VSS 快照数考虑在内。 这将导致重新计算兼容天数。 请注意，此更改只会对新的分层文件生效，并覆盖你可能已执行的 VSS 计划中的任何自定义。

<a id="proactive-recall"></a>

## <a name="proactively-recall-new-and-changed-files-from-an-azure-file-share"></a>主动从 Azure 文件共享召回新文件和更改的文件

对于代理版本 11，新模式在服务器终结点上可用。 这种模式使分布在全球的公司可在本地用户访问任何文件之前，就预先在远程地区的服务器缓存中进行填充。 在服务器终结点上启用后，此模式将导致该服务器召回在 Azure 文件共享中已创建或更改的文件。

### <a name="scenario"></a>方案

一家全球分布的公司在美国和印度都有分支机构。 早上（美国时间），信息工作者为一个全新项目创建新文件夹和新文件，并为其工作一整天。 Azure 文件同步会将文件夹和文件同步到 Azure 文件共享（云终结点）。 印度的信息工作者将在其时区继续处理该项目。 当印度团队早上到达时，印度本地启用 Azure 文件同步的服务器需要这些新文件在本地可用，这样他们就能够有效地在本地缓存中工作。 启用此模式可防止由于按需召回而使初始文件访问变慢，并使服务器能够在 Azure 文件共享中更改或创建文件后立即主动召回它们。

> [!IMPORTANT]
> 务必认识到，跟踪服务器上的 Azure 文件共享中的更改可能会增加出口流量并产生 Azure 计费。 如果实际上本地不需要召回到服务器的文件，那么到服务器的不必要召回会产生负面影响。 对于以下情况，请使用此模式：你知道使用云中的最新更改预填充服务器上的缓存会对使用该服务器上文件的用户或应用程序产生积极影响。

### <a name="enable-a-server-endpoint-to-proactively-recall-what-changed-in-an-azure-file-share"></a>启用服务器终结点以主动召回 Azure 文件共享中的更改

# <a name="portal"></a>[门户](#tab/proactive-portal)

1. 在 [Azure 门户](https://portal.azure.com/)中，请转到存储同步服务，选择正确的同步组，然后在 Azure 文件共享（云终结点）中，确定想要密切跟踪其更改的服务器终结点。
1. 在云分层部分中，找到“Azure 文件共享下载”主题。 你将看到当前所选模式，可以对其进行更改以更紧密地跟踪 Azure 文件共享更改，并主动将其召回服务器。

:::image type="content" source="media/storage-sync-files-deployment-guide/proactive-download.png" alt-text="该图显示了当前有效的服务器终结点的 Azure 文件共享下载行为，以及用于打开相关菜单以对其进行更改的按钮。":::

# <a name="powershell"></a>[PowerShell](#tab/proactive-powershell)

可以通过 [Set-AzStorageSyncServerEndpoint](/powershell/module/az.storagesync/set-azstoragesyncserverendpoint) cmdlet 修改 PowerShell 中的服务器终结点属性。

```powershell
# Optional parameter. Default: "UpdateLocallyCachedFiles", alternative behavior: "DownloadNewAndModifiedFiles"
$recallBehavior = "DownloadNewAndModifiedFiles"

Set-AzStorageSyncServerEndpoint -InputObject <PSServerEndpoint> -LocalCacheMode $recallBehavior
```

---

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>将 DFS 复制 (DFS-R) 部署迁移至 Azure 文件同步

若要将 DFS-R 部署迁移至 Azure 文件同步，请执行以下操作：

1. 创建一个同步组以表示要替换的 DFS-R 拓扑。
1. 从 DFS-R 拓扑具有完整数据集的服务器开始迁移。 在该服务器上安装 Azure 文件同步。
1. 注册该服务器，并为要迁移的第一个服务器创建服务器终结点。 请勿启用云分层。
1. 让所有数据同步至你的 Azure 文件共享（云终结点）。
1. 在剩余的每个 DFS-R 服务器上安装并注册 Azure 文件同步代理。
1. 禁用 DFS-R。
1. 在每个 DFS-R 服务器上创建服务器终结点。 请勿启用云分层。
1. 确保同步完成，并根据需要测试拓扑。
1. 注销 DFS-R。
1. 现在应该可以根据需要在任何一个服务器终结点上启用云分层。

有关详细信息，请参阅 [Azure 文件同步与分布式文件系统 (DFS) 的互操作](file-sync-planning.md#distributed-file-system-dfs)。

## <a name="next-steps"></a>后续步骤

- [创建 Azure 文件同步服务器终结点](file-sync-server-endpoint-create.md)
- [向 Azure 文件同步注册或注销服务器](file-sync-server-registration.md)
- [监视 Azure 文件同步](file-sync-monitoring.md)
