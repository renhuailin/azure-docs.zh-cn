---
title: 在 Linux 中排查 Azure 文件问题 | Microsoft Docs
description: 在 Linux 中排查 Azure 文件存储问题。 请查看从 Linux 客户端进行连接时与 Azure 文件存储相关的常见问题，并查看可能的解决方法。
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 10/16/2018
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 4c618fad5b1e85df1ffa19fa2aa0e8621ae2bdd9
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110094449"
---
# <a name="troubleshoot-azure-files-problems-in-linux-smb"></a>在 Linux 中排查 Azure 文件存储问题 (SMB)

本文列出了从 Linux 客户端连接时与 Azure 文件相关的常见问题。 并提供了这些问题的可能原因和解决方法。 

除本文中的疑难解答步骤之外，还可使用 [AzFileDiagnostics](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Linux) 确保 Linux 客户端满足正确的先决条件。 AzFileDiagnostics 自动检测本文中提到的大多数症状。 它有助于设置环境以获得最佳性能。 也可以在 [Azure 文件共享疑难解答](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares)中找到此信息。 疑难解答提供了帮助你解决连接、映射和装载 Azure 文件共享问题的步骤。

> [!IMPORTANT]
> 本文内容仅适用于 SMB 共享。 有关 NFS 共享的详细信息，请参阅[排查 Azure NFS 文件共享问题](storage-troubleshooting-files-nfs.md)。

## <a name="cannot-connect-to-or-mount-an-azure-file-share"></a>无法连接或装载 Azure 文件共享

### <a name="cause"></a>原因

造成此问题的常见原因包括：

- 如果使用的是具有过时 SMB 客户端的 Linux 分发版，请参阅 [在 Linux 上使用 Azure 文件存储](storage-how-to-use-files-linux.md)，了解有关 Azure 中可用的、具有兼容客户端的常见 Linux 分发版的详细信息。
- 客户端上未安装 SMB 实用程序 (cifs-utils)。
- 客户端上不可用最低的 SMB 版本 2.1。
- 客户端不支持 SMB 3.x 加密。 上表列出的 Linux 发行版支持使用加密从本地装载以及跨区域装载。 其他分发要求内核 4.11 及更高版本。
- 试图通过不受支持的 TCP 端口 445 连接到存储帐户。
- 试图从 Azure VM 连接到 Azure 文件共享，而该 VM 并非与存储帐户处于同一区域。
- 如果在存储帐户上启用了[需要安全转移](../common/storage-require-secure-transfer.md)设置，则 Azure 文件仅允许使用带加密的 SMB 3.x 进行连接。

### <a name="solution"></a>解决方案

若要解决此问题，请使用 [Troubleshooting tool for Azure Files mounting errors on Linux](https://github.com/Azure-Samples/azure-files-samples/tree/master/AzFileDiagnostics/Linux)（用于 Linux 上 Azure 文件装载错误的故障排除工具）。 此工具：

* 有助于验证客户端运行环境。
* 可检测导致 Azure 文件访问失败的不兼容客户端配置。
* 可提供自我修复的说明性指导。
* 可收集诊断跟踪。

<a id="mounterror13"></a>
## <a name="mount-error13-permission-denied-when-you-mount-an-azure-file-share"></a>装载 Azure 文件共享时出现“装载错误(13):权限被拒绝”

### <a name="cause-1-unencrypted-communication-channel"></a>原因 1：通信通道未加密

出于安全原因，如果信道未加密，且未从 Azure 文件共享所在的数据中心尝试连接，则到 Azure 文件共享的连接将受阻。 如果在存储帐户中启用[需要安全传输](../common/storage-require-secure-transfer.md)设置，则还可以阻止同一数据中心中未加密的连接。 仅当用户的客户端 OS 支持 SMB 加密时，才提供加密的信道。

若要了解详细信息，请参阅[使用 Linux 和 cifs-utils 包装载 Azure 文件共享的先决条件](storage-how-to-use-files-linux.md#prerequisites)。 

### <a name="solution-for-cause-1"></a>原因 1 的解决方案

1. 从支持 SMB 加密的客户端进行连接，或者从用于 Azure 文件共享的 Azure 存储帐户所在数据中心内的虚拟机进行连接。
2. 如果客户端不支持 SMB 加密，请验证是否已在存储帐户上禁用[需要安全传输](../common/storage-require-secure-transfer.md)设置。

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>原因 2：在存储帐户上启用了虚拟网络或防火墙规则 

如果在存储帐户上配置了虚拟网络 (VNET) 和防火墙规则，则将拒绝访问网络流量，除非允许客户端 IP 地址或虚拟网络访问。

### <a name="solution-for-cause-2"></a>原因 2 的解决方案

验证是否已在存储帐户上正确配置虚拟网络和防火墙规则。 若要测试虚拟网络或防火墙规则是否导致此问题，请将存储帐户上的设置临时更改为“允许来自所有网络的访问”。 若要了解详细信息，请参阅[配置 Azure 存储防火墙和虚拟网络](../common/storage-network-security.md)。

<a id="permissiondenied"></a>
## <a name="permission-denied-disk-quota-exceeded-when-you-try-to-open-a-file"></a>尝试打开文件时“[权限被拒绝] 超出了磁盘配额”

在 Linux 中，收到类似下文的错误消息：

**\<filename> [permission denied] Disk quota exceeded**

### <a name="cause"></a>原因

已达到文件或目录所允许的并发打开句柄数上限。

单个文件或目录的打开句柄配额为 2000 个。 当你拥有 2000 个打开句柄时，会显示一条错误消息，指示已达到此配额。

### <a name="solution"></a>解决方案

关闭某些句柄以减少并发打开句柄数，然后重试操作。

若要查看文件共享、目录或文件的打开句柄，请使用 [Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle) PowerShell cmdlet。  

若要关闭文件共享、目录或文件的打开句柄，请使用 [Close-AzStorageFileHandle](/powershell/module/az.storage/close-azstoragefilehandle) PowerShell cmdlet。

> [!Note]  
> Get-AzStorageFileHandle 和 Close-AzStorageFileHandle cmdlet 包括在 Az PowerShell 模块 2.4 或更高版本中。 若要安装最新 Az PowerShell 模块，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>在 Linux 中将文件复制到 Azure 文件以及从中复制文件时速度缓慢

- 如果你没有特定的 I/O 大小下限要求，我们建议使用 1 MiB 的 I/O 大小以获得最佳性能。
- 使用正确的复制方法：
    - 为两个文件共享之间的任何传输使用 [AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。
    - 将 cp 或 dd 与 parallel 配合使用可以提高复制速度，线程数取决于用例和工作负荷。 以下示例使用 6 个线程： 
    - cp 示例（cp 将使用文件系统的默认块大小作为区块大小）：`find * -type f | parallel --will-cite -j 6 cp {} /mntpremium/ &`。
    - dd 示例（此命令将区块大小显式设置为 1 MiB）：`find * -type f | parallel --will-cite-j 6 dd if={} of=/mnt/share/{} bs=1M`
    - 开源第三方工具，例如：
        - [GNU Parallel](https://www.gnu.org/software/parallel/)。
        - [Fpart](https://github.com/martymac/fpart) - 将文件排序并将其打包到分区中。
        - [Fpsync](https://github.com/martymac/fpart/blob/master/tools/fpsync) - 使用 Fpart 和复制工具生成多个实例，以便将数据从 src_dir 迁移到 dst_url。
        - [Multi](https://github.com/pkolano/mutil) - 基于 GNU coreutils 的多线程 cp 和 md5sum。
- 提前设置文件大小而不是让每个写入成为扩展写入，这样可以在文件大小已知的情况下提高复制速度。 如果需要避免扩展写入，则可使用 `truncate - size <size><file>` 命令设置目标文件大小。 然后，`dd if=<source> of=<target> bs=1M conv=notrunc`命令会复制源文件，不需反复更新目标文件的大小。 例如，可以为每个要复制的文件设置目标文件大小（假定在 /mnt/share 下装载了一个共享）：
    - `$ for i in `` find * -type f``; do truncate --size ``stat -c%s $i`` /mnt/share/$i; done`
    - 然后复制文件，不需以并行方式扩展写入：`$find * -type f | parallel -j6 dd if={} of =/mnt/share/{} bs=1M conv=notrunc`

<a id="error115"></a>
## <a name="mount-error115-operation-now-in-progress-when-you-mount-azure-files-by-using-smb-3x"></a>使用 SMB 3.x 装载 Azure 文件时出现“装载错误 (115)：操作正在进行”

### <a name="cause"></a>原因

某些 Linux 分发尚不支持 SMB 3.x 中的加密功能。 如果用户尝试使用 SMB 3.x 装载 Azure 文件，可能会由于缺少功能而收到“115”错误消息。 只有在使用 Ubuntu 16.04 或更高版本时，才支持带完全加密的 SMB 3.x。

### <a name="solution"></a>解决方案

4\.11 内核中引入了适用于 Linux 的 SMB 3.x 加密功能。 使用此功能可从本地或不同 Azure 区域装载 Azure 文件共享。 某些 Linux 分发版可能已将 4.11 内核中的更改向后移植到它们维护的旧版本 Linux 内核。 若要帮助确定 Linux 版本是否支持带加密的 SMB 3.x，请参考 [在 Linux 上使用 Azure 文件存储](storage-how-to-use-files-linux.md)。 

如果 Linux SMB 客户端不支持加密，请使用 SMB 2.1 从文件共享所在的同一数据中心上的 Azure Linux VM 装载 Azure 文件。 验证是否已在存储帐户中禁用[需要安全传输](../common/storage-require-secure-transfer.md)设置。 

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

<a id="open-handles"></a>
## <a name="unable-to-delete-a-file-or-directory-in-an-azure-file-share"></a>无法删除 Azure 文件共享中的文件或目录

### <a name="cause"></a>原因
如果该文件或目录有一个打开的句柄，通常会出现此问题。 

### <a name="solution"></a>解决方案

如果 SMB 客户端关闭了所有打开的句柄，但问题仍然出现，请执行以下操作：

- 使用 [Get-AzStorageFileHandle](/powershell/module/az.storage/get-azstoragefilehandle) PowerShell cmdlet 查看打开的句柄。

- 使用 [Close-AzStorageFileHandle](/powershell/module/az.storage/close-azstoragefilehandle) PowerShell cmdlet 关闭打开的句柄。 

> [!Note]  
> Get-AzStorageFileHandle 和 Close-AzStorageFileHandle cmdlet 包括在 Az PowerShell 模块 2.4 或更高版本中。 若要安装最新 Az PowerShell 模块，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-az-ps)。

<a id="slowperformance"></a>
## <a name="slow-performance-on-an-azure-file-share-mounted-on-a-linux-vm"></a>Linux VM 上装载的 Azure 文件共享的性能低下

### <a name="cause-1-caching"></a>原因 1：缓存

性能低下的一个可能原因是禁用了缓存。 缓存在反复访问某个文件时可能很有用，但其他情况下，它是一项开销。 检查是否在使用缓存，然后再禁用它。

### <a name="solution-for-cause-1"></a>原因 1 的解决方案

要检查是否禁用了缓存，请查找 **cache=** 条目。

**Cache=none** 指示缓存已禁用。 使用默认的装载命令重新装载共享，或者在装载命令中显式添加 **cache=strict** 选项，确保默认缓存或“strict”缓存模式已启用。

在某些情况下，**serverino** 装载选项可能会导致 **ls** 命令针对每个目录条目运行 stat。 当列出大型目录时，此行为会导致性能降级。 可在 **/etc/fstab** 条目中检查装载选项：

`//azureuser.file.core.windows.net/cifs /cifs cifs vers=2.1,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

还可以通过运行 sudo mount | grep cifs 命令并检查其输出，检查所用的选项是否正确。 下面是示例输出：

```
//azureuser.file.core.windows.net/cifs on /cifs type cifs (rw,relatime,vers=2.1,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777, dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)
```

如果不存在 cache=strict 或 serverino 选项，请通过运行[文档](./storage-how-to-use-files-linux.md)中的装载命令卸载并再次装载 Azure 文件 。 然后，重新检查 **/etc/fstab** 条目是否具有正确的选项。

### <a name="cause-2-throttling"></a>原因 2：限制

在你遇到限制的情况下，系统可能会将你的请求发送到队列。 可以利用 [Azure Monitor 中的 Azure 存储指标](../blobs/monitor-blob-storage.md)对此进行验证。

### <a name="solution-for-cause-2"></a>原因 2 的解决方案

确保应用在 [Azure 文件存储缩放目标](storage-files-scale-targets.md#azure-files-scale-targets)中。

<a id="timestampslost"></a>
## <a name="time-stamps-were-lost-in-copying-files-from-windows-to-linux"></a>将文件从 Windows 复制到 Linux 时，时间戳丢失

在 Linux/Unix 平台上，如果文件 1 和文件 2 由不同的用户拥有，则 cp-p 命令将失败。

### <a name="cause"></a>原因

COPYFILE 中的强制标志 **f** 导致在 Unix 上执行 **cp -p -f**。 此命令也无法保留不归你拥有的文件的时间戳。

### <a name="workaround"></a>解决方法

使用存储帐户用户来复制文件：

- `Useadd : [storage account name]`
- `Passwd [storage account name]`
- `Su [storage account name]`
- `Cp -p filename.txt /share`

## <a name="ls-cannot-access-ltpathgt-inputoutput-error"></a>ls: cannot access '&lt;path&gt;':Input/output error

尝试使用 ls 命令列出 Azure 文件共享中的文件时，该命令在列出文件时挂起。 收到以下错误：

**ls: cannot access'&lt;path&gt;':Input/output error**


### <a name="solution"></a>解决方案
将 Linux 内核升级到以下可解决此问题的版本：

- 4.4.87+
- 4.9.48+
- 4.12.11+
- 4\.13 或更高的所有版本

## <a name="cannot-create-symbolic-links---ln-failed-to-create-symbolic-link-t-operation-not-supported"></a>无法创建符号链接 - ln: failed to create symbolic link 't':Operation not supported

### <a name="cause"></a>原因
默认情况下，使用 CIFS 在 Linux 上装载 Azure 文件共享不会启用符号链接的支持。 出现如下错误：
```
ln -s linked -n t
ln: failed to create symbolic link 't': Operation not supported
```
### <a name="solution"></a>解决方案
Linux CIFS 客户端不支持通过 SSMB 2 或 3 协议创建 Windows 样式符号链接。 Linux 客户端目前支持使用称作 [Minshall+French 符号链接](https://wiki.samba.org/index.php/UNIX_Extensions#Minshall.2BFrench_symlinks)的另一种样式的符号链接来执行创建和跟踪操作。 需要符号链接的客户可以使用“mfsymlinks”装载选项。 我们推建议使用“mfsymlinks”，因为这也是 Macs 使用的格式。

若要使用符号链接，请将以下代码添加到 CIFS 装载命令的末尾：

```
,mfsymlinks
```

因此，命令如下所示：

```
sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino,mfsymlinks
```

然后，可以按照 [wiki](https://wiki.samba.org/index.php/UNIX_Extensions#Storing_symlinks_on_Windows_servers) 上的建议创建符号链接。

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

<a id="error112"></a>
## <a name="mount-error112-host-is-down-because-of-a-reconnection-time-out"></a>因为重新连接超时而发生“装载错误(112): 主机已关闭”

当客户端长时间处于空闲状态时，Linux 客户端上出现“112”装载错误。 长时间空闲后，客户端将断开连接，并发生连接超时。  

### <a name="cause"></a>原因

连接可能由于以下原因而处于空闲状态：

-   网络通信发生故障，导致使用默认的“soft”装载选项时阻止 TCP 与服务器重新建立连接
-   最近的重新连接修复，较旧的内核中未提供这些修复

### <a name="solution"></a>解决方案

Linux 内核中的此重新连接问题现已在以下更改中进行了修复：

- [修复重新连接以在 socket 重新连接后缩短 smb3 会话的重新连接延迟时间](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93)
- [socket 重新连接后立即调用 echo 服务](https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7)
- [CIFS：修复重新连接期间潜在的内存损坏](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b)
- [CIFS：修复重新连接期间潜在的互斥双锁（对于内核 v4.9 及更高版本）](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183)

但是，这些更改可能尚未移植到所有的 Linux 发行版。 如果使用的是常用的 Linux 发行版，可以查看[在 Linux 上使用 Azure 文件存储](storage-how-to-use-files-linux.md)，以了解发行版的哪个版本进行了必要的内核更改。

### <a name="workaround"></a>解决方法

可以通过指定硬装载来解决此问题。 在连接建立或连接明确中断前，硬装载强制客户端等待。 可以借此防止由于网络超时而导致的错误。 但是，此解决方法可能会导致无限期等待。 请准备好根据需要停止连接。

如果无法升级到最新的内核版本，可通过将每隔 30 秒或更少的时间间隔便会对其进行写入操作的文件保留在 Azure 文件共享中来解决此问题。 这必须是一个写入操作，例如在文件上重新写入创建或修改日期。 否则，可能会得到缓存的结果，且操作可能不会触发重新连接。

## <a name="cifs-vfs-error--22-on-ioctl-to-get-interface-list-when-you-mount-an-azure-file-share-by-using-smb-3x"></a>使用 SMB 3.x 装载 Azure 文件共享时，出现“CIFS VFS：用于获取接口列表的 ioctl 上发生错误 -22”

### <a name="cause"></a>原因
记录此错误的原因是 Azure 文件存储[当前不支持 SMB 多通道](/rest/api/storageservices/features-not-supported-by-the-azure-file-service)。

### <a name="solution"></a>解决方案
可以忽略此错误。


### <a name="unable-to-access-folders-or-files-which-name-has-a-space-or-a-dot-at-the-end"></a>无法访问名称末尾带有空格或点的文件夹或文件

在 Linux 上装载 Azure 文件共享时，你将无法访问该共享中的文件夹或文件，诸如 du 和 ls 之类的命令和/或第三方应用程序在访问该共享时可能会失败并显示“无此类文件或目录”错误，但是你能够通过门户将文件上传到所述文件夹。

### <a name="cause"></a>原因

文件夹或文件是从一个将名称末尾的字符编码为另一字符的系统上传的，从 Macintosh 计算机上传的文件可能有“0xF028”或“0xF029”字符，而不是“0x20”（空格）或“0X2E”（点）字符。

### <a name="solution"></a>解决方案

在 Linux 上装载共享时，请在共享上使用 mapchars 选项： 

不是使用：

```bash
sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
```

而是使用：

```bash
sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino,mapchars
```


## <a name="need-help-contact-support"></a>需要帮助？ 请联系支持人员。

如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，以快速解决问题。