---
title: 在 Windows 中排查 Azure 文件问题
description: 在 Windows 中排查 Azure 文件存储问题。 请查看从 Windows 客户端进行连接时与 Azure 文件存储相关的常见问题，并查看可能的解决方法。 仅适用于 SMB 共享
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/13/2019
ms.author: jeffpatt
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b1541acc9ab6871418d1cb750d74d285f3228f92
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122014862"
---
# <a name="troubleshoot-azure-files-problems-in-windows-smb"></a>在 Windows 中排查 Azure 文件存储问题 (SMB)

本文列出了从 Windows 客户端进行连接时，与 Microsoft Azure 文件相关的常见问题。 并提供了这些问题的可能原因和解决方法。 除了本文中的疑难解答步骤之外，还可以使用 [AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows)，以确保 Windows 客户端环境满足正确的先决条件。 AzFileDiagnostics 会自动检测本文中提及的大多数症状，并帮助设置环境，以实现最佳性能。

> [!IMPORTANT]
> 本文内容仅适用于 SMB 共享。 有关 NFS 共享的详细信息，请参阅[排查 Azure NFS 文件共享问题](storage-troubleshooting-files-nfs.md)。

## <a name="applies-to"></a>适用于
| 文件共享类型 | SMB | NFS |
|-|:-:|:-:|
| 标准文件共享 (GPv2)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 标准文件共享 (GPv2)、GRS/GZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 高级文件共享 (FileStorage)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>装载 Azure 文件共享时出现错误 5

尝试装载文件共享时，可能会收到以下错误：

- 发生系统错误 5。 访问被拒绝。

### <a name="cause-1-unencrypted-communication-channel"></a>原因 1：通信通道未加密

出于安全原因，如果信道未加密，且未从 Azure 文件共享所在的数据中心尝试连接，则到 Azure 文件共享的连接将受阻。 如果在存储帐户中启用[需要安全传输](../common/storage-require-secure-transfer.md)设置，则还可以阻止同一数据中心中未加密的连接。 仅当用户的客户端 OS 支持 SMB 加密时，才提供加密的信道。

Windows 8、Windows Server 2012 及更高版本的每个系统协商包括支持加密的 SMB 3.x 的请求。

### <a name="solution-for-cause-1"></a>原因 1 的解决方案

1. 从支持 SMB 加密的客户端（Windows 8、Windows Server 2012 或更高版本）进行连接，或者从用于 Azure 文件共享的 Azure 存储帐户所在数据中心内的虚拟机进行连接。
2. 如果客户端不支持 SMB 加密，请验证是否已在存储帐户上禁用[需要安全传输](../common/storage-require-secure-transfer.md)设置。

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>原因 2：在存储帐户上启用了虚拟网络或防火墙规则 

如果在存储帐户上配置了虚拟网络 (VNET) 和防火墙规则，则将拒绝访问网络流量，除非允许客户端 IP 地址或虚拟网络访问。

### <a name="solution-for-cause-2"></a>原因 2 的解决方案

验证是否已在存储帐户上正确配置虚拟网络和防火墙规则。 若要测试虚拟网络或防火墙规则是否导致此问题，请将存储帐户上的设置临时更改为“允许来自所有网络的访问”。 若要了解详细信息，请参阅[配置 Azure 存储防火墙和虚拟网络](../common/storage-network-security.md)。

### <a name="cause-3-share-level-permissions-are-incorrect-when-using-identity-based-authentication"></a>原因 3：使用基于标识的身份验证时，共享级别权限不正确

当用户使用 Active Directory (AD) 或 Azure Active Directory 域服务 (Azure AD DS) 身份验证访问 Azure 文件共享时，如果共享级别权限不正确，则对文件共享的访问会失败，并出现“拒绝访问”错误。 

### <a name="solution-for-cause-3"></a>原因 3 的解决方案

验证是否正确配置了权限：

- Active Directory (AD) 请参阅[向标识分配共享级别权限](./storage-files-identity-ad-ds-assign-permissions.md)。

    使用 Azure AD Connect 从 Active Directory (AD) 同步到 Azure Active Directory (Azure AD) 的组和用户支持共享级别权限分配。  确认分配了共享级别权限的组和用户不是不受支持的“仅限云”组。
- Azure Active Directory 域服务 (Azure AD DS) 请参阅[向标识分配访问权限](./storage-files-identity-auth-active-directory-domain-service-enable.md?tabs=azure-portal#assign-access-permissions-to-an-identity)。

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>尝试装载或卸载 Azure 文件共享时发生错误 53、错误 67 或错误 87

尝试从本地或其他数据中心装载文件共享时，可能会看到以下错误消息：

- 发生系统错误 53。 找不到网络路径。
- 发生系统错误 67。 找不到网络名称。
- 发生系统错误 87。 参数不正确。

### <a name="cause-1-port-445-is-blocked"></a>原因 1：端口 445 被阻止

如果端口 445 到 Azure 文件数据中心的出站通信受阻，可能会发生系统错误 53 或 67。 如需大致了解允许或禁止从端口 445 进行访问的 ISP，请访问 [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx)。

若要检查防火墙或 ISP 是否阻止端口 445，请使用 [AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) 工具或 `Test-NetConnection` cmdlet。 

若要使用 `Test-NetConnection` cmdlet，则必须安装 Azure PowerShell 模块。有关详细信息，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-Az-ps)。 记得将 `<your-storage-account-name>` 和 `<your-resource-group-name>` 替换为存储帐户的相应名称。

   
```azurepowershell
$resourceGroupName = "<your-resource-group-name>"
$storageAccountName = "<your-storage-account-name>"

# This command requires you to be logged into your Azure account, run Login-AzAccount if you haven't
# already logged in.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName

# The ComputerName, or host, is <storage-account>.file.core.windows.net for Azure Public Regions.
# $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign clouds
# or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
Test-NetConnection -ComputerName ([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) -Port 445
```
    
如果连接成功，则会看到以下输出：
    
  
```azurepowershell
ComputerName     : <your-storage-account-name>
RemoteAddress    : <storage-account-ip-address>
RemotePort       : 445
InterfaceAlias   : <your-network-interface>
SourceAddress    : <your-ip-address>
TcpTestSucceeded : True
```
 

> [!Note]  
> 以上命令返回存储帐户的当前 IP 地址。 此 IP 地址不一定保持不变，可能会随时更改。 请勿将此 IP 地址硬编码到任何脚本中或某个防火墙配置中。

### <a name="solution-for-cause-1"></a>原因 1 的解决方案

#### <a name="solution-1---use-azure-file-sync"></a>解决方案 1 - 使用 Azure 文件同步
Azure 文件同步可将本地 Windows Server 转换为 Azure 文件共享的快速缓存。 可以使用 Windows Server 上可用的任意协议本地访问数据，包括 SMB、NFS 和 FTPS。 Azure 文件同步通过端口 443 工作，因此可用作从已阻止端口 445 的客户端访问 Azure 文件的解决方法。 [了解如何设置 Azure 文件同步](../file-sync/file-sync-extend-servers.md)。

#### <a name="solution-2---use-vpn"></a>解决方案 2 - 使用 VPN
通过将 VPN 设置为特定存储帐户，流量将通过安全隧道，而不是通过 Internet。 按照[设置 VPN 的说明](storage-files-configure-p2s-vpn-windows.md)，从 Windows 访问 Azure 文件。

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>解决方案 3 - 让你的 ISP/IT 管理员取消阻止端口 445
与 IT 部门或 ISP 配合，向 [Azure IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)开放端口 445 出站通信。

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>解决方案 4 - 基于存储资源管理器/Powershell 等工具使用 REST API
除了 SMB，Azure 文件存储还支持 REST。 REST 访问通过端口 443（标准 TCP）工作。 使用 REST API 编写的各种工具可实现丰富的 UI 体验。 [存储资源管理器](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows)是其中之一。 [下载并安装存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)，然后连接到 Azure 文件支持的文件共享。 也可使用 [PowerShell](./storage-how-to-use-files-powershell.md)，此工具也使用 REST API。

### <a name="cause-2-ntlmv1-is-enabled"></a>原因 2：NTLMv1 已启用

如果客户端上已启用 NTLMv1 通信，可能会出现系统错误 53 或 87。 Azure 文件仅支持 NTLMv2 身份验证。 启用 NTLMv1 将创建安全级别较低的客户端。 因此，Azure 文件的通信受阻。 

若要确定这是否是错误原因，请验证以下注册表子项是否未设为小于 3 的值：

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

有关详细信息，请参阅 TechNet 上的 [LmCompatibilityLevel](/previous-versions/windows/it-pro/windows-2000-server/cc960646(v=technet.10)) 主题。

### <a name="solution-for-cause-2"></a>原因 2 的解决方案

在以下注册表子项中，将 LmCompatibilityLevel 值还原为默认值 3：

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816---not-enough-quota-is-available-to-process-this-command"></a>错误 1816 - 处理此命令的配额不够

### <a name="cause"></a>原因

达到并发开放句柄数的上限时，会发生错误 1816。这些句柄是为 Azure 文件共享上的文件或目录启用的。 有关详细信息，请参阅 [Azure 文件规模目标](./storage-files-scale-targets.md#azure-files-scale-targets)。

### <a name="solution"></a>解决方案

关闭一些句柄，减少并发打开句柄的数量，再重试。 有关详细信息，请参阅 [Microsoft Azure 存储性能和可伸缩性清单](../blobs/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

若要查看文件共享、目录或文件的打开句柄，请使用 [Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle) PowerShell cmdlet。  

若要关闭文件共享、目录或文件的打开句柄，请使用 [Close-AzStorageFileHandle](/powershell/module/az.storage/close-azstoragefilehandle) PowerShell cmdlet。

> [!Note]  
> Get-AzStorageFileHandle 和 Close-AzStorageFileHandle cmdlet 包括在 Az PowerShell 模块 2.4 或更高版本中。 若要安装最新 Az PowerShell 模块，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>尝试访问或删除 Azure 文件共享时出现错误“无访问权限”  
尝试访问或删除门户中的 Azure 文件共享时，可能会收到以下错误：

无访问权限  
错误代码：403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>原因 1：在存储帐户上启用了虚拟网络或防火墙规则

### <a name="solution-for-cause-1"></a>原因 1 的解决方案

验证是否已在存储帐户上正确配置虚拟网络和防火墙规则。 若要测试虚拟网络或防火墙规则是否导致此问题，请将存储帐户上的设置临时更改为“允许来自所有网络的访问”。 若要了解详细信息，请参阅[配置 Azure 存储防火墙和虚拟网络](../common/storage-network-security.md)。

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>原因 2：你的用户帐户无权访问该存储帐户

### <a name="solution-for-cause-2"></a>原因 2 的解决方案

浏览到Azure文件共享所在的存储帐户，单击“访问控制(IAM)”，确保你的用户帐户有权访问该存储帐户。 若要了解详细信息，请参阅[如何使用 Azure 基于角色的访问控制 (Azure RBAC) 来保护存储帐户](../blobs/security-recommendations.md#data-protection)。

## <a name="unable-to-modify-or-delete-an-azure-file-share-or-share-snapshots-because-of-locks-or-leases"></a>由于锁或租约无法修改或删除 Azure 文件共享（或共享快照）
Azure 文件存储提供两种方法来防止意外修改或删除 Azure 文件共享和共享快照： 

- 存储帐户资源锁：所有 Azure 资源（包括存储帐户）都支持[资源锁](../../azure-resource-manager/management/lock-resources.md)。 管理员或增值服务（如 Azure 备份）可能会将锁置于存储帐户。 存在两种资源锁变体：修改（防止对存储帐户及其资源进行所有修改）和删除（仅阻止删除存储帐户及其资源）。 通过 `Microsoft.Storage` 资源提供程序修改或删除共享时，将在 Azure 文件共享和共享快照上强制实施资源锁。 大多数门户操作、名称中带有 `Rm` 的 Azure 文件存储的 Azure PowerShell cmdlet（如 `Get-AzRmStorageShare`）以及 `share-rm` 命令组中的 Azure CLI 命令（如 `az storage share-rm list`）都使用 `Microsoft.Storage` 资源提供程序。 某些工具和实用程序（例如存储资源管理器、名称中不带 `Rm` 的旧 Azure 文件存储 PowerShell 管理 cmdlet（如 `Get-AzStorageShare`）以及 `share` 命令组下的旧 Azure 文件存储 CLI 命令（如 `az storage share list`）使用 FileREST API 中绕过 `Microsoft.Storage` 资源提供程序和资源锁的旧 API。 有关 FileREST API 中公开的旧管理 API 的详细信息，请参阅 [Azure 文件存储中的控制平面](/rest/api/storageservices/file-service-rest-api#control-plane)。

- 共享/共享快照租约：共享租约是用于 Azure 文件共享和文件共享快照的一种专有锁。 管理员可以通过使用脚本调用 API 或者通过附加服务（如 Azure 备份）将租约置于单个 Azure 文件共享或文件共享快照。 将租约置于 Azure 文件共享或文件共享快照上时，可以使用租约 ID 修改或删除文件共享/共享快照。 用户还可以在修改操作之前释放租约（需要租约 ID）或中断租约（不需要租约 ID）。 有关共享租约的详细信息，请参阅[租用共享](/rest/api/storageservices/lease-share)。

由于资源锁和租约可能会干扰对存储帐户/Azure 文件共享的预期的管理员操作，因此，你可能想删除可能由 Azure 备份等增值服务手动或自动置于资源上的任何资源锁/租约。 以下脚本会删除所有资源锁和租约。 请记得将 `<resource-group>` 和 `<storage-account>` 替换为适合环境的值。

若要运行以下脚本，必须安装 Azure 存储 PowerShell 模块的 [3.10.1 预览版](https://www.powershellgallery.com/packages/Az.Storage/3.10.1-preview)。

> [!Important]  
> 在 Azure 文件存储资源上获取资源锁和共享/共享快照租约的增值服务可能会定期重新应用锁和租约。 通过增值服务修改或删除锁定的资源可能会影响这些服务的常规操作，例如删除由 Azure 备份管理的共享快照。

```PowerShell
# Parameters for storage account resource
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"

# Get reference to storage account
$storageAccount = Get-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName

# Remove resource locks
Get-AzResourceLock `
        -ResourceType "Microsoft.Storage/storageAccounts" `
        -ResourceGroupName $storageAccount.ResourceGroupName `
        -ResourceName $storageAccount.StorageAccountName | `
    Remove-AzResourceLock -Force | `
    Out-Null

# Remove share and share snapshot leases
Get-AzStorageShare -Context $storageAccount.Context | `
    Where-Object { $_.Name -eq $fileShareName } | `
    ForEach-Object {
        try {
            $leaseClient = [Azure.Storage.Files.Shares.Specialized.ShareLeaseClient]::new($_.ShareClient)
            $leaseClient.Break() | Out-Null
        } catch { }
    }
```

<a id="open-handles"></a>
## <a name="unable-to-modify-moverename-or-delete-a-file-or-directory"></a>无法修改、移动/重命名或删除文件或目录
文件共享的主要目的之一是，多个用户和应用程序可以同时与该共享中的文件和目录进行交互。 为了帮助进行此交互，文件共享提供了几种对文件和目录访问进行协调的方式。

通过 SMB 从已装载的 Azure 文件共享打开文件时，应用程序/操作系统会请求文件句柄，该句柄是对文件的引用。 除了别的之外，你的应用程序会在请求文件句柄时指定文件共享模式，该模式指定你对文件的访问的独占性级别（由 Azure 文件存储强制实施）： 

- `None`：你具有独占访问权限。 
- `Read`：在你打开文件时，其他人可以读取该文件。
- `Write`：在你打开文件时，其他人可以写入到该文件。 
- `ReadWrite`：`Read` 和 `Write` 共享模式的组合。
- `Delete`：在你打开文件时，其他人可以删除该文件。 

尽管作为无状态协议，FileREST 协议没有文件句柄的概念，但它确实提供了类似的机制来协调对你的脚本、应用程序或服务可能会使用的文件和文件夹的访问：文件租约。 当文件被租用时，会将其视为等效于文件共享模式为 `None` 的文件句柄。 

尽管文件句柄和租约的作用很重要，但有时文件句柄和租约可能会被孤立。 发生这种情况时，可能会导致修改或删除文件时出现问题。 你可能会看到类似于以下内容的错误消息：

- 进程无法访问该文件，因为它正在被另一个进程使用。
- 操作无法完成，因为此文件已在其他程序中打开。
- 该文档已由另一个用户锁定以进行编辑。
- SMB 客户端已将指定的资源标记为要删除。

此问题的解决方法取决于这种情况是由孤立的文件句柄还是由孤立的文件租约所导致。 

### <a name="cause-1"></a>原因 1
文件句柄正在阻止修改或删除文件/目录。 可以使用 [Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle) PowerShell cmdlet 查看打开的句柄。 

如果所有 SMB 客户端均已关闭其在某个文件/目录上打开的句柄，并且问题仍然存在，那么你可以强制关闭文件句柄。

### <a name="solution-1"></a>解决方案 1
若要强制文件句柄关闭，请使用 [Close-AzStorageFileHandle](/powershell/module/az.storage/close-azstoragefilehandle) PowerShell cmdlet。 

> [!Note]  
> Get-AzStorageFileHandle 和 Close-AzStorageFileHandle cmdlet 包括在 Az PowerShell 模块 2.4 或更高版本中。 若要安装最新 Az PowerShell 模块，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。

### <a name="cause-2"></a>原因 2
文件租约正在阻止修改或删除文件。 你可以使用以下 PowerShell 检查文件是否具有文件租约（将 `<resource-group>`、`<storage-account>`、`<file-share>` 和 `<path-to-file>` 替换为适用于你的环境的值）：

```PowerShell
# Set variables 
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"
$fileShareName = "<file-share>"
$fileForLease = "<path-to-file>"

# Get reference to storage account
$storageAccount = Get-AzStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $storageAccountName

# Get reference to file
$file = Get-AzStorageFile `
        -Context $storageAccount.Context `
        -ShareName $fileShareName `
        -Path $fileForLease

$fileClient = $file.ShareFileClient

# Check if the file has a file lease
$fileClient.GetProperties().Value
```

如果文件具有租约，则返回的对象应包含以下属性：

```Output
LeaseDuration         : Infinite
LeaseState            : Leased
LeaseStatus           : Locked
```

### <a name="solution-2"></a>解决方案 2
若要从文件删除租约，可以释放租约或中断租约。 若要释放租约，你需要该租约的 LeaseId，这是在创建租约时设置的。 无需 LeaseId 即可中断租约。

下面的示例演示如何为“原因 2”中所示的文件中断租约（此示例将使用“原因 2”中的 PowerShell 变量继续）：

```PowerShell
$leaseClient = [Azure.Storage.Files.Shares.Specialized.ShareLeaseClient]::new($fileClient)
$leaseClient.Break() | Out-Null
```

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>在 Windows 中将文件复制到 Azure 文件以及从中复制文件时速度缓慢

尝试将文件传输到 Azure 文件服务时，可能会发现速度缓慢。

- 如果你没有特定的 I/O 大小下限要求，我们建议使用 1 MiB 的 I/O 大小以获得最佳性能。
-   如果知道通过写入要扩展的最终文件大小，并且软件在文件的未写入结尾包含零时未出现兼容性问题，请提前设置文件大小，而不是让每次写入都成为扩展写入。
-   使用正确的复制方法：
    -   为两个文件共享之间的任何传输使用 [AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。
    -   在本地计算机上的文件共享之间使用 [Robocopy](./storage-how-to-create-file-share.md)。

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Windows 8.1 或 Windows Server 2012 R2 的注意事项

对于运行 Windows 8.1 或 Windows Server 2012 R2 的客户端，请确保已安装修补程序 [KB3114025](https://support.microsoft.com/help/3114025)。 此修补程序可提升创建和关闭句柄的性能。

可运行以下脚本，检查是否已安装此修补程序：

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

如果安装了修补程序，会显示以下输出：

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> 自 2015 年 12 月起，Azure 市场中的 Windows Server 2012 R2 映像将默认安装修补程序 KB3114025。

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer-or-this-pc"></a>“我的电脑”或“这台电脑”中没有带驱动器号的文件夹

如果使用 net use 以管理员身份映射 Azure 文件共享，共享似乎会丢失。

### <a name="cause"></a>原因

默认情况下，Windows 文件资源管理器不以管理员身份运行。 如果通过管理命令提示符运行 net use，可以管理员身份映射网络驱动器。 由于映射的驱动器以用户为中心，如果不同用户帐户下已装载这些驱动器，则已登录的用户帐户将不显示它们。

### <a name="solution"></a>解决方案
从非管理员命令行中装载共享。 或者，可按照 [此 TechNet 主题](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee844140(v=ws.10))配置 **EnableLinkedConnections** 注册表值。

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>如果存储帐户包含正斜杠，则 net use 命令失败

### <a name="cause"></a>原因

net use 命令会将正斜杠 (/) 解释为命令行选项。 如果用户帐户名称以正斜杠开头，则驱动器映射失败。

### <a name="solution"></a>解决方案

若要解决此问题，可完成以下任意步骤：

- 运行以下 PowerShell 命令：

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc"`

  在批处理文件中，可以按如下方式运行命令：

  `Echo new-smbMapping ... | powershell -command –`

- 用双引号将密钥括起来，可以解决此问题（除非正斜杠是首个字符）。 如果是，可以使用交互模式并单独输入密码，也可以生成密钥来获取不以正斜杠开头的密钥。

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>应用程序或服务无法访问装载的 Azure 文件驱动器

### <a name="cause"></a>原因

每个用户都装载了驱动器。 如果运行应用程序或服务的用户帐户与装载驱动器的用户帐户不同，应用程序将检测不到驱动器。

### <a name="solution"></a>解决方案

使用以下任一解决方案：

-   从包含应用程序的同一用户帐户装载驱动器。 可以使用 PsExec 等工具。
- 在 net use 命令的用户名和密码参数中传递存储帐户名称和密钥。
- 使用 cmdkey 命令将凭据添加到凭据管理器中。 通过交互式登录或使用 `runas`，在服务帐户上下文中从命令行执行此操作。
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- 不使用映射驱动器号直接映射共享。 某些应用程序可能无法正确地重新连接到驱动器号，因此使用完整的 UNC 路径可能会更可靠。 

  `net use * \\storage-account-name.file.core.windows.net\share`

按照这些说明操作后，对系统/网络服务帐户运行 net use 时，可能会看到以下错误消息：“发生系统错误 1312。 如果为系统/网络服务帐户运行 可能已被终止。” 如果发生这种情况，请确保传递到 net use 的用户名包括域信息（例如，“[存储帐户名称].file.core.windows.net”）。

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>出现错误“要将该文件复制到的目标不支持加密”

通过网络复制文件时，文件在源计算机上被解密，以明文形式传输，并在目标位置上被重新加密。 不过，尝试复制加密文件时，可能会看到以下错误消息：“要将该文件复制到的目标不支持加密。”

### <a name="cause"></a>原因
如果使用的是加密文件系统 (EFS)，可能会出现此问题。 可将 BitLocker 加密的文件复制到 Azure 文件。 不过，Azure 文件不支持 NTFS EFS。

### <a name="workaround"></a>解决方法
必须先将文件解密，才能通过网络进行复制。 使用下列方法之一：

- 运行 copy /d 命令。 这样，可以将加密文件作为解密文件保存到目标位置。
- 设置以下注册表项：
  - Path = HKLM\Software\Policies\Microsoft\Windows\System
  - Value type = DWORD
  - 名称 = CopyFileAllowDecryptedRemoteDestination
  - 值 = 1

请注意，设置注册表项会影响对网络共享进行的所有复制操作。

## <a name="slow-enumeration-of-files-and-folders"></a>文件和文件夹的枚举速度变慢

### <a name="cause"></a>原因

如果客户端计算机上用于大型目录的缓存不足，则可能会出现此问题。

### <a name="solution"></a>解决方案

若要解决此问题，请调整 DirectoryCacheEntrySizeMax 注册表值以允许在客户端计算机上缓存较大的目录列表：

- 位置：HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- 值名称：DirectoryCacheEntrySizeMax 
- 值类型：DWORD
 
 
例如，可将其设置为 0x100000，并查看性能是否有所提高。

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-domain-service-azure-ad-ds-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>为 Azure 文件存储启用 Azure Active Directory 域服务 (Azure AD DS) 身份验证时，出现错误 AadDsTenantNotFound：“找不到具有租户 ID aad-tenant-id 的活动租户”

### <a name="cause"></a>原因

如果存储帐户在关联订阅的 Azure AD 租户上未创建 [Azure AD 域服务 (Azure AD DS)](../../active-directory-domain-services/overview.md)，则在该帐户上[为 Azure 文件存储启用 Azure Active Directory 域服务 (Azure AD DS) 身份验证](storage-files-identity-auth-active-directory-domain-service-enable.md)时，会发生错误 AadDsTenantNotFound。  

### <a name="solution"></a>解决方案

在部署存储帐户的订阅的 Azure AD 租户上启用 Azure AD DS。 需要 Azure AD 租户的管理员权限才能创建托管域。 如果你不是 Azure AD 租户的管理员，请与管理员联系并按照分步指南操作，以[创建和配置 Azure Active Directory 域服务托管域](../../active-directory-domain-services/tutorial-create-instance.md)。

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="unable-to-mount-azure-files-with-ad-credentials"></a>无法使用 AD 凭据装载 Azure 文件存储 

### <a name="self-diagnostics-steps"></a>自我诊断步骤
首先，请确保已完成所有四个步骤来[启用 Azure 文件存储 AD 身份验证](./storage-files-identity-auth-active-directory-enable.md)。

其次，尝试[使用存储帐户密钥装载 Azure 文件共享](./storage-how-to-use-files-windows.md)。 如果未能装载，请下载 [AzFileDiagnostics.ps1](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) 以帮助验证客户端运行环境、检测会导致 Azure 文件访问失败的不兼容客户端配置、为自行修复提供规范指导以及收集诊断跟踪。

第三，可以运行 Debug-AzStorageAccountAuth cmdlet，使用已登录的 AD 用户对 AD 配置执行一组基本检查。 [AzFilesHybrid v0.1.2+ 版本](https://github.com/Azure-Samples/azure-files-samples/releases)支持此 cmdlet。 需要使用对目标存储帐户具有所有者权限的 AD 用户身份运行此 cmdlet。  
```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```
该 cmdlet 会按顺序执行以下检查，提供有关故障的指导：
1. CheckADObjectPasswordIsCorrect：确保在表示存储帐户的 AD 标识上配置的密码与存储帐户 kerb1 或 kerb2 密钥的密码匹配。 如果密码不正确，可以运行 [Update-AzStorageAccountADObjectPassword](./storage-files-identity-ad-ds-update-password.md) 以重置密码。 
2. CheckADObject：确认 Active Directory 中存在表示存储帐户并且具有正确 SPN（服务主体名称）的对象。 如果未正确设置 SPN，请运行在调试 cmdlet 中返回的 Set-AD cmdlet 以配置 SPN。
3. CheckDomainJoined：验证客户端计算机是否已加入 AD 的域。 如果计算机未加入 AD 的域，请参阅此[文章](/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain)以了解域加入说明。
4. CheckPort445Connectivity：检查是否为 SMB 连接打开了端口 445。 如果未打开所需端口，请参阅故障排除工具 [AzFileDiagnostics.ps1](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Windows) 以了解 Azure 文件存储的连接问题。
5. CheckSidHasAadUser：检查已登录 AD 用户是否同步到 Azure AD。 如果要查找特定 AD 用户是否同步到 Azure AD，则可以在输入参数中指定 -UserName 和 -Domain。 
6. CheckGetKerberosTicket：尝试获取 Kerberos 票证以连接到存储帐户。 如果没有有效的 Kerberos 令牌，请运行 klist get cifs/storage-account-name.file.core.windows.net cmdlet 并检查错误代码，以查明票证检索失败的根本原因。
7. CheckStorageAccountDomainJoined：检查是否已启用 AD 身份验证并填充了帐户的 AD 属性。 如果未这样做，请参阅[此处](./storage-files-identity-ad-ds-enable.md)的说明，以在 Azure 文件存储上启用 AD DS 身份验证。 
8. CheckUserRbacAssignment：检查 AD 用户是否具有适当的 RBAC 角色分配，以提供用于访问 Azure 文件存储的共享级别权限。 如果没有，请参阅[此处](./storage-files-identity-ad-ds-assign-permissions.md)的说明，以配置共享级别权限。 （在 AzFilesHybrid v0.2.3+ 版本上受支持）
9. CheckUserFileAccess：检查 AD 用户是否具有正确的目录/文件权限 (Windows ACL) 以访问 Azure 文件存储。 如果没有，请参阅[此处](./storage-files-identity-ad-ds-configure-permissions.md)的说明，以配置目录/文件级别权限。 （在 AzFilesHybrid v0.2.3+ 版本上受支持）

## <a name="unable-to-configure-directoryfile-level-permissions-windows-acls-with-windows-file-explorer"></a>无法使用 Windows 文件资源管理器配置目录/文件级别权限 (Windows ACL)

### <a name="symptom"></a>症状

尝试在已装载的文件共享上使用文件资源管理器配置 Windows ACL 时，可能会遇到下面所述的任一症状：
- 单击“安全性”选项卡下的“编辑权限”后，权限向导不会加载。 
- 尝试选择新用户或组时，域位置不会显示正确的 AD DS 域。 

### <a name="solution"></a>解决方案

建议使用 [icacls 工具](/windows-server/administration/windows-commands/icacls)配置目录/文件级别权限以作为解决方法。 

## <a name="errors-when-running-join-azstorageaccountforauth-cmdlet"></a>运行 Join-AzStorageAccountForAuth cmdlet 时出现错误

### <a name="error-the-directory-service-was-unable-to-allocate-a-relative-identifier"></a>错误：“目录服务无法分配相对标识符”

如果具有 RID 主机 FSMO 角色的域控制器不可用或在从域中删除后从备份中还原，则可能会出现此错误。  确认所有域控制器都在运行并可用。

### <a name="error-cannot-bind-positional-parameters-because-no-names-were-given"></a>错误： “无法绑定位置参数，因为未指定名称”

此错误很可能是由 Join-AzStorageAccountforAuth 命令中的语法错误触发的。  检查命令中是否存在拼写错误或语法错误，并验证是否安装了最新版本的 AzFilesHybrid 模块 (https://github.com/Azure-Samples/azure-files-samples/releases) )。  

## <a name="azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption"></a>对 AES 256 Kerberos 加密的 Azure 文件存储本地 AD DS 身份验证支持

我们通过 [AzFilesHybrid 模块 v0.2.2](https://github.com/Azure-Samples/azure-files-samples/releases) 引入了对 Azure 文件存储本地 AD DS 身份验证的 AES 256 Kerberos 加密支持。 如果使用低于 v0.2.2 的模块版本启用了 AD DS 身份验证，则需要下载最新 AzFilesHybrid 模块 (v0.2.2+)，并运行以下 PowerShell。 如果尚未在存储帐户上启用 AD DS 身份验证，则可以按照此[指导](./storage-files-identity-ad-ds-enable.md#option-one-recommended-use-azfileshybrid-powershell-module)进行启用。 

```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Update-AzStorageAccountAuthForAES256 -ResourceGroupName $ResourceGroupName -StorageAccountName $StorageAccountName
```

## <a name="need-help-contact-support"></a>需要帮助？ 请联系支持人员。
如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，以快速解决问题。
