---
title: 如何结合使用 DFS-N 和 Azure 文件存储
description: 使用 Azure 文件存储的 DFS-N 常见用例
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 3/02/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 0676639523a0b1ebd23ff0e5082e6cccbd641f4a
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112117293"
---
# <a name="how-to-use-dfs-namespaces-with-azure-files"></a>如何结合使用 DFS 命名空间和 Azure 文件存储
[分布式文件系统命名空间](/windows-server/storage/dfs-namespaces/dfs-overview)（通常称为 DFS 命名空间或 DFS-N）是一个 Windows Server 服务器角色，广泛用于简化生产环境中 SMB 文件共享的部署和维护。 DFS 命名空间是存储命名空间虚拟化技术，意味着使用此技术，可在文件共享的 UNC 路径与实际文件共享本身之间提供间接层。 DFS 命名空间适用于 SMB 文件共享，无论这些文件共享是否为托管状态：它可用于本地 Windows 文件服务器上托管的 SMB 共享（无论是否使用 Azure 文件同步）、直接用于 Azure 文件共享、用于 Azure NetApp 文件或其他第三方产品/服务中托管的 SMB 文件共享，甚至用于其他云中托管的文件共享。 

就核心功能而言，DFS 命名空间提供用户友好 UNC 路径（如 `\\contoso\shares\ProjectX`）和 SMB 共享的基础 UNC 路径（如 `\\Server01-Prod\ProjectX` 或 `\\storageaccount.file.core.windows.net\projectx`）之间的映射。 如果最终用户希望导航到文件共享，他们可键入用户友好 UNC 路径，但他们的 SMB 客户端会访问映射的基础 SMB 路径。 还可以扩展这个基本概念，接管现有的文件服务器名称（如 `\\MyServer\ProjectX`）。 可使用此功能来实现以下方案：

- 为逻辑数据集提供迁移证明名称。 在本示例中的映射类似于映射到 `\\OldServer\Engineering` 的 `\\contoso\shares\Engineering`。 完成迁移到 Azure 文件存储的过程后，可以更改映射，使用户友好 UNC 路径指向 `\\storageaccount.file.core.windows.net\engineering`。 当最终用户访问用户友好 UNC 路径时，将无缝重定向到 Azure 文件共享路径。

- 为分布到不同物理站点上的多个服务器（例如通过 Azure 文件同步）的逻辑数据集建立公用名。在本示例中，`\\contoso\shares\FileSyncExample` 这类名称映射到多个 UNC 路径，如 `\\FileSyncServer1\ExampleShare`、`\\FileSyncServer2\DifferentShareName`、`\\FileSyncServer3\ExampleShare`。 当用户访问用户友好 UNC 时，会获得一系列可能的 UNC 路径，并根据 Windows Server Active Directory (AD) 站点定义，选择离他们最近的路径。

- 跨大小、IO 或其他缩放阈值扩展逻辑数据集。 这在处理用户目录（每个用户在共享上都有自己的文件夹）或临时共享（用户获得临时分配的空间来解决临时数据需求）时很常见。 使用 DFS 命名空间，可将多个文件夹拼结到一个具有内联性的命名空间。 例如，`\\contoso\shares\UserShares\user1` 映射到 `\\storageaccount.file.core.windows.net\user1`、`\\contoso\shares\UserShares\user2` 映射到 `\\storageaccount.file.core.windows.net\user2`，等等。  

以下视频概括性介绍如何结合使用 DFS 命名空间和 Azure 文件存储部署。

[![介绍如何设置以结合使用 DFS-N 和 Azure 文件存储的演示视频 - 单击即可播放！](./media/files-manage-namespaces/video-snapshot-dfsn.png)](https://www.youtube.com/watch?v=jd49W33DxkQ)
> [!NOTE]  
> 跳到视频中的 10:10 处，了解如何设置 DFS 命名空间。

如果你已准备好 DFS 命名空间，则无需执行任何特殊步骤即可将其用于 Azure 文件存储和文件同步。如果要从本地访问 Azure 文件共享，请遵循常规网络注意事项；有关详细信息，请参阅 [Azure 文件存储网络注意事项](./storage-files-networking-overview.md)。

## <a name="applies-to"></a>适用于
| 文件共享类型 | SMB | NFS |
|-|:-:|:-:|
| 标准文件共享 (GPv2)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 标准文件共享 (GPv2)、GRS/GZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 高级文件共享 (FileStorage)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |

## <a name="namespace-types"></a>命名空间类型
DFS 命名空间提供了两种主要命名空间类型：

- **基于域的命名空间**：作为 Windows Server AD 域的一部分托管的命名空间。 作为 AD 一部分托管的命名空间将具有包含域名称的 UNC 路径，例如 `\\contoso.com\shares\myshare`（如果你的域是 `contoso.com`）。 基于域的命名空间通过 AD 支持更大的缩放限制和内置冗余。 基于域的命名空间不能是故障转移群集上的群集资源。 
- **独立命名空间**：在单个服务器上托管的命名空间，而不是作为 Windows Server AD 的一部分托管的命名空间。 独立命名空间的名称将基于独立服务器的名称，例如 `\\MyStandaloneServer\shares\myshare`（如果独立服务器的名称为 `MyStandaloneServer`）。 独立命名空间可支持的缩放目标比基于域的命名空间小，但它可以作为群集资源托管在故障转移群集上。

## <a name="requirements"></a>要求
若要在 Azure 文件存储和文件同步中使用 DFS 命名空间，必须具有以下资源：

- Active Directory 域。 这可以托管在所需的任何位置，例如本地、Azure 虚拟机 (VM)，甚至其他云中。
- 可托管命名空间的 Windows 服务器。 DFS 命名空间的常见部署模式是使用 Active Directory 域控制器来托管命名空间，但这些命名空间可以从安装了 DFS 命名空间服务器角色的任何服务器进行设置。 DFS 命名空间适用于所有受支持的 Windows Server 版本。
- 托管在已加入域的环境中的 SMB 文件共享，例如在已加入域的存储帐户中托管的 Azure 文件共享，或者使用 Azure 文件同步在已加入域的 Windows 文件服务器上托管的文件共享。有关域加入存储帐户的详细信息，请参阅[基于标识的身份验证](storage-files-active-directory-overview.md)。 无论你是否使用 Azure 文件同步，Windows 文件服务器都以相同的方式加入域。
- 要用于 DFS 命名空间的 SMB 文件共享必须可从本地网络访问。 这主要是 Azure 文件共享的问题，但从技术上讲，它适用于 Azure 或任何其他云中托管的任何文件共享。 有关网络的详细信息，请参阅[直接访问的网络注意事项](storage-files-networking-overview.md)。

## <a name="install-the-dfs-namespaces-server-role"></a>安装 DFS 命名空间服务器角色
如果已在使用 DFS 命名空间，或希望在域控制器上设置 DFS 命名空间，可以放心跳过这些步骤。

# <a name="portal"></a>[门户](#tab/azure-portal)
若要安装 DFS 命名空间服务器角色，请在服务器上打开服务器管理器。 选择“管理”，然后选择“添加角色和功能”。  生成的向导将引导你安装运行和管理 DFS 命名空间所需的 Windows 组件。 

在安装向导的“安装类型”部分，选择“基于角色或基于功能的安装”单选按钮，然后选择“下一步”  。 在“服务器选择”部分，选择想在其上安装 DFS 命名空间服务器角色的服务器，然后选择“下一步” 。 

在“服务器角色”部分，勾选角色列表中的“DFS 命名空间” 。 可以在“文件和存储服务” > “文件和 ISCSI 服务”下找到此项 。 如果选择 DFS 命名空间服务器角色，它还可能添加任何所需的但你尚未安装的支持性服务器角色或功能。

![“添加角色和功能”向导的屏幕截图，其中已选择“DFS 命名空间”角色。](./media/files-manage-namespaces/dfs-namespaces-install.png)

选中“DFS 命名空间”角色后，可以在所有后续屏幕上选择“下一步”，并在向导出现“安装”按钮后选择该按钮  。 安装完成后，可配置命名空间。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
从提升的 PowerShell 会话（或使用 PowerShell 远程处理），执行以下命令。

```PowerShell
Install-WindowsFeature -Name "FS-DFS-Namespace", "RSAT-DFS-Mgmt-Con"
```
---

## <a name="take-over-existing-server-names-with-root-consolidation"></a>通过根路径合并接管现有的服务器名称
DFS 命名空间的一个重要用途是接管现有的服务器名称，以便重构文件共享的物理布局。 例如，你可能希望在现代化迁移期间将多个旧文件服务器中的文件共享合并到单个文件服务器上。 以前，最终用户的熟悉程度和文档链接限制了将不同文件服务器中的文件共享合并到一台主机上的能力，但使用 DFS 命名空间根路径合并功能，一台服务器可使用多个服务器名称并路由到相应名称的共享。

尽管“根路径合并”可用于各种数据中心迁移方案，但在采用云原生 Azure 文件共享时尤其有用，因为：

- Azure 文件共享不允许保留现有的本地服务器名称。
- 必须通过存储帐户的完全限定的域名 (FQDN) 访问 Azure 文件共享。 例如，始终通过 `\\storageaccount.file.core.windows.net\share` UNC 路径访问 `storageaccount` 存储帐户中名为 `share` 的 Azure 文件共享。 以下名称可能会导致最终用户混淆：短名称（例如 `\\MyServer\share`）或名称是组织域名的子域（例如 `\\MyServer.contoso.com\share`).

根路径合并仅可用于独立命名空间。 如果你的文件共享已有基于域的命名空间，则无需创建根路径合并的命名空间。

### <a name="enabling-root-consolidation"></a>启用根路径合并
可以通过从提升的 PowerShell 会话（或使用 PowerShell 远程处理）设置以下注册表项来启用根路径合并。

```PowerShell
New-Item `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs" `
    -Type Registry `
    -ErrorAction SilentlyContinue
New-Item `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs\Parameters" `
    -Type Registry `
    -ErrorAction SilentlyContinue
New-Item `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs\Parameters\Replicated" `
    -Type Registry `
    -ErrorAction SilentlyContinue
Set-ItemProperty `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs\Parameters\Replicated" `
    -Name "ServerConsolidationRetry" `
    -Value 1
```

### <a name="creating-dns-entries-for-existing-file-server-names"></a>为现有文件服务器名称创建 DNS 条目
为了使 DFS 命名空间响应现有文件服务器名称，请为指向 DFS 命名空间服务器名称的现有文件服务器创建别名 (CNAME) 记录。 更新 DNS 记录的具体过程取决于组织正在使用的服务器，以及组织是否使用自定义工具来自动管理 DNS。 以下步骤适用于 Windows Server 附带的和自动供 Windows AD 使用的 DNS 服务器。

# <a name="portal"></a>[门户](#tab/azure-portal)
在 Windows DNS 服务器上，打开 DNS 管理控制台。 可选择“开始”按钮并键入“DNS”来找到该控制台 。 导航到域的前向查找区域。 例如，如果域为 `contoso.com`，可以在管理控制台中的“前向查找区域” > “`contoso.com`”下找到前向查找区域 。 此对话框中显示的确切层次结构将取决于网络的 DNS 配置。

右键单击前向查找区域，然后选择“新建别名 (CNAME)”。 在生成的对话框中，输入要替换的文件服务器的短名称（完全限定的域名将在标记为“完全限定的域名”的文本框中自动填充）。 在标记为“目标主机的完全限定的域名 (FQDN)”文本框中，输入已设置的 DFS-N 服务器的名称。 如果需要，可使用“浏览”按钮来帮助选择服务器。 选择“确定”，为服务器创建 CNAME 记录。

![描述 CNAME DNS 条目的“新资源记录”的屏幕截图。](./media/files-manage-namespaces/root-consolidation-cname.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
在 Windows DNS 服务器上，打开 PowerShell 会话（或使用 PowerShell 远程处理）执行以下命令，使用环境的相关值来填充 `$oldServer` 和 `$dfsnServer`（`$domain` 将使用域名自动填充，但也可以手动键入此项）。

```PowerShell
# Variables
$oldServer = "MyServer"
$domain = Get-CimInstance -ClassName "Win32_ComputerSystem" | `
    Select-Object -ExpandProperty Domain
$dfsnServer = "CloudDFSN.$domain"

# Create CNAME record
Import-Module -Name DnsServer
Add-DnsServerResourceRecordCName `
    -Name $oldServer `
    -HostNameAlias $dfsnServer `
    -ZoneName $domain
```

---

## <a name="create-a-namespace"></a>创建命名空间
DFS 命名空间的基本管理单位是命名空间。 命名空间根路径或名称是命名空间的起始点，因此在 UNC 路径 `\\contoso.com\Public\` 中，命名空间根路径为 `Public`。 

如果使用 DFS 命名空间通过根路径合并接管现有的服务器名称，则命名空间的名称应该为要接管的服务器名称的名称，并前面会加上 `#` 字符。 例如，如果要接管名为 `MyServer` 的现有服务器，请创建名为 `#MyServer` 的 DFS-N 命名空间。 下面的 PowerShell 部分负责添加 `#` 前缀，但如果通过 DFS 管理控制台创建，则需要根据需要添加前缀。 

# <a name="portal"></a>[门户](#tab/azure-portal)
若要创建新的命名空间，请打开“DFS 管理”控制台。 可选择“开始”按钮并键入“DFS 管理”来找到该控制台 。 生成的管理控制台具有两部分，即“命名空间”和“复制”，它们分别指代 DFS 命名空间和 DFS 复制 (DFS-R) 。 Azure 文件同步提供了一种新式复制和同步机制，如果还需要复制，可使用此机制来代替 DFS-R。

选择“命名空间”部分，然后选择“新建命名空间”按钮（也可右键单击“命名空间”部分）  。 生成的“新建命名空间向导”将引导你创建命名空间。 

向导中的第一部分要求你选取要用于托管命名空间的 DFS 命名空间服务器。 一个命名空间可由多台服务器托管，但一次只能用一个服务器设置 DFS 命名空间。 输入所需 DFS 命名空间服务器的名称，然后选择“下一步”。 在“命名空间名称和设置”部分中，可以输入命名空间的所需名称，然后选择“下一步” 。 

在“命名空间类型”部分，可以选择“基于域的命名空间”或“独立命名空间”  。 如果要使用 DFS 命名空间来保留现有的文件服务器/NAS 设备名称，应选择独立命名空间选项。 对于任何其他方案，应选择基于域的命名空间。 有关如何选择命名空间类型的详细信息，请参阅上面的[命名空间类型](#namespace-types)。

![屏幕截图显示在“新建命名空间向导”中的基于域的命名空间和独立命名空间之间选择。](./media/files-manage-namespaces/dfs-namespace-type.png)

为环境选择所需的命名空间类型，然后选择“下一步”。 然后，该向导将汇总要创建的命名空间。 选择“创建”以创建命名空间，并在对话框完成后选择“关闭” 。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
从 DFS 命名空间服务器上的 PowerShell 会话中，执行以下 PowerShell 命令，使用环境的相关值填充 `$namespace`、`$type` 和 `$takeOverName`。

```PowerShell
# Variables
$namespace = "Public"
$type = "DomainV2" # "Standalone"
$takeOverName = $false # $true

$namespace = if ($takeOverName -and $type -eq "Standalone" -and $namespace[0] -ne "#") { 
    "#$namespace" 
} else { $namespace }
$dfsnServer = $env:ComputerName
$namespaceServer = if ($type -eq "DomainV2") { 
    Get-CimInstance -ClassName "Win32_ComputerSystem" | `
    Select-Object -ExpandProperty Domain
} else { $dfsnServer }

# Create share for DFS-N namespace
$smbShare = "C:\DFSRoots\$namespace"
if (!(Test-Path -Path $smbShare)) { New-Item -Path $smbShare -ItemType Directory }
New-SmbShare -Name $namespace -Path $smbShare -FullAccess Everyone

# Create DFS-N namespace
Import-Module -Name DFSN
$namespacePath = "\\$namespaceServer\$namespace"
$targetPath = "\\$dfsnServer\$namespace"
New-DfsnRoot -Path $namespacePath -TargetPath $targetPath -Type $type
```
---

## <a name="configure-folders-and-folder-targets"></a>配置文件夹和文件夹目标
要使命名空间有用，它必须具有文件夹和文件夹目标。 每个文件夹可具有一个或多个文件夹目标，这些目标是指向托管该内容的 SMB 文件共享的指针。 用户浏览包含文件夹目标的文件夹时，客户端计算机将收到透明地将客户端计算机重定向到一个文件夹目标的参考项。 也可以具有无文件夹目标的文件夹，以将结构和层次结构添加到命名空间。

可以将 DFS 命名空间文件夹视为类似于文件共享。 

# <a name="portal"></a>[门户](#tab/azure-portal)
在 DFS 管理控制台中，选择刚创建的命名空间，然后选择“新建文件夹”。 生成的“新建文件夹”对话框可用于创建文件夹及其目标。

![“新建文件夹”对话框的屏幕截图。](./media/files-manage-namespaces/dfs-folder-targets.png)

在标有“名称”的文本框中，提供文件夹的名称。 选择“添加...”，为此文件夹添加文件夹目标。 生成的“添加文件夹目标”对话框提供一个标记为“文件夹目标路径”的文本框，可在其中提供所需文件夹的 UNC 路径 。 在“添加文件夹目标”对话框中选择“确定” 。 如果要将 UNC 路径添加到 Azure 文件共享，可能会收到一条消息，指示无法与服务器 `storageaccount.file.core.windows.net` 通信。 这是预期的；选择“是”以继续。 最后，在“新建文件夹”对话框中选择“确定”以创建文件夹和文件夹目标 。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```PowerShell
# Variables
$shareName = "MyShare"
$targetUNC = "\\storageaccount.file.core.windows.net\myshare"

# Create folder and folder targets
$sharePath = "$namespacePath\$shareName"
New-DfsnFolder -Path $sharePath -TargetPath $targetUNC
```

---

现在你已创建命名空间、文件夹和文件夹目标，应该能够通过 DFS 命名空间装载文件共享。 如果使用的是基于域的命名空间，则共享的完整路径应该为 `\\<domain-name>\<namespace>\<share>`。 如果使用的是独立命名空间，则共享的完整路径应该为 `\\<DFS-server>\<namespace>\<share>`。 如果使用的是具有根路径合并的独立命名空间，可以直接通过旧服务器名称（如 `\\<old-server>\<share>`）进行访问。

## <a name="see-also"></a>另请参阅
- 部署 Azure 文件共享：[计划 Azure 文件部署](storage-files-planning.md)和[如何创建文件共享](storage-how-to-create-file-share.md)。
- 配置文件共享访问：[基于标识的身份验证](storage-files-active-directory-overview.md)和[有关直接访问的网络注意事项](storage-files-networking-overview.md)。
- [Windows Server 分布式文件系统命名空间](/windows-server/storage/dfs-namespaces/dfs-overview)