---
title: 排查 Azure NFS 文件共享问题 - Azure 文件存储
description: 排查 Azure NFS 文件共享问题。
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/15/2020
ms.author: jeffpatt
ms.subservice: files
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: e2cdcf3b42fbb71751644efbaa394c51d2f861fc
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123258315"
---
# <a name="troubleshoot-azure-nfs-file-share-problems"></a>排查 Azure NFS 文件共享问题

本文列出了一些 Azure NFS 文件共享（预览版）方面的常见问题。 其中提供了这些问题的潜在原因和解决方法。 本文还介绍了公共预览版中的已知问题。

## <a name="applies-to"></a>适用于
| 文件共享类型 | SMB | NFS |
|-|:-:|:-:|
| 标准文件共享 (GPv2)、LRS/ZRS | ![否](../media/icons/no-icon.png) | ![否](../media/icons/no-icon.png) |
| 标准文件共享 (GPv2)、GRS/GZRS | ![否](../media/icons/no-icon.png) | ![否](../media/icons/no-icon.png) |
| 高级文件共享 (FileStorage)、LRS/ZRS | ![否](../media/icons/no-icon.png) | ![是](../media/icons/yes-icon.png) |

## <a name="chgrp-filename-failed-invalid-argument-22"></a>chgrp“filename”失败：参数 (22) 无效

### <a name="cause-1-idmapping-is-not-disabled"></a>原因 1：未禁用 idmapping
Azure 文件存储不允许使用字母数字 UID/GID。 因此必须禁用 idmapping。 

### <a name="cause-2-idmapping-was-disabled-but-got-re-enabled-after-encountering-bad-filedir-name"></a>原因 2：已禁用 idmapping，但遇到错误的文件/目录名称后重新启用
即使已正确禁用 idmapping，在某些情况下，也会覆盖禁用 idmapping 的设置。 例如，当 Azure 文件存储遇到错误的文件名时，它将返回错误。 看到此特定错误代码后，NFS v 4.1 Linux 客户端决定重新启用 idmapping，并使用字母数字 UID/GID 再次发送未来的请求。 有关 Azure 文件存储中不受支持的字符的列表，请参阅此[文章](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)。 冒号是不受支持的字符之一。 

### <a name="workaround"></a>解决方法
检查 idmapping 是否已禁用并且不会重新启用它，然后执行以下操作：

- 卸载共享
- 使用 # echo Y > /sys/module/nfs/parameters/nfs4_disable_idmapping 禁用 id-mapping
- 将共享装载回去
- 如果运行 rsync，请使用“—numeric-ids”参数在没有任何错误目录/文件名目录中运行 rsync。

## <a name="unable-to-create-an-nfs-share"></a>无法创建 NFS 共享

### <a name="cause-1-subscription-is-not-enabled"></a>原因 1：未启用订阅

你的订阅可能尚未注册 Azure 文件存储 NFS 预览版。 要启用该功能，需要从 Cloud Shell 或本地终端运行更多 commandlet。

> [!NOTE]
> 你可能需要等待 30 分钟才能完成注册。


#### <a name="solution"></a>解决方案

使用以下脚本注册功能和资源提供程序，并在运行脚本前替换 `<yourSubscriptionIDHere>`：

```azurepowershell
Connect-AzAccount

#If your identity is associated with more than one subscription, set an active subscription
$context = Get-AzSubscription -SubscriptionId <yourSubscriptionIDHere>
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowNfsFileShares -ProviderNamespace Microsoft.Storage

Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="cause-2-unsupported-storage-account-settings"></a>原因 2：存储帐户设置不受支持

NFS 只能在具有以下配置的存储帐户上使用：

- 层级 - 高级
- 帐户类型 - FileStorage
- 区域 - [受支持区域的列表](./storage-files-how-to-create-nfs-shares.md?tabs=azure-portal#regional-availability)

#### <a name="solution"></a>解决方案

按照[如何创建 NFS 共享](storage-files-how-to-create-nfs-shares.md)一文中的说明操作。

### <a name="cause-3-the-storage-account-was-created-prior-to-registration-completing"></a>原因 3：存储帐户是在注册完成之前创建的

为了使存储帐户能够使用此功能，必须在订阅完成 NFS 注册后创建帐户。 最多需要 30 分钟就能完成注册。

#### <a name="solution"></a>解决方案

完成注册后，请按照[如何创建 NFS 共享](storage-files-how-to-create-nfs-shares.md)一文中的说明进行操作。

## <a name="cannot-connect-to-or-mount-an-azure-nfs-file-share"></a>无法连接或装载 Azure NFS 文件共享

### <a name="cause-1-request-originates-from-a-client-in-an-untrusted-networkuntrusted-ip"></a>原因 1：请求来自不受信任的网络/不受信任的 IP 中的客户端

与 SMB 不同，NFS 没有基于用户的身份验证。 用于共享的身份验证基于网络安全规则配置。 因此，为了确保仅建立与 NFS 共享的安全连接，你必须使用服务终结点或专用终结点。 除了专用终结点，如果还想从本地访问共享，你必须设置 VPN 或 ExpressRoute。 系统将忽略添加到存储帐户防火墙允许列表中的 IP。 必须使用以下方法之一来设置对 NFS 共享的访问权限：


- [服务终结点](storage-files-networking-endpoints.md#restrict-public-endpoint-access)
    - 由公共终结点访问
    - 仅在同一区域中可用。
    - VNet 对等互连不会授予对共享的访问权限。
    - 每个虚拟网络或子网都必须单独添加到允许列表中。
    - 对于本地访问，服务终结点可用于 ExpressRoute、点到站点和站点到站点 VPN，但我们建议使用专用终结点，因为它更安全。

下图描绘了使用公共终结点的连接。

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg" alt-text="公共终结点连接的关系图。" lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg":::

- [专用终结点](storage-files-networking-endpoints.md#create-a-private-endpoint)
    - 访问比服务终结点更安全。
    - 可在存储帐户的 Azure 区域的内部和外部（跨区域和本地）通过专用链接访问 NFS 共享
    - 通过与专用终结点中托管的虚拟网络建立虚拟网络对等互连，NFS 共享可访问对等互连虚拟网络中的客户端。
    - 专用终结点可用于 ExpressRoute、点到站点和站点到站点 VPN。

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg" alt-text="专用终结点连接的关系图。" lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg":::

### <a name="cause-2-secure-transfer-required-is-enabled"></a>原因 2：启用了所需的安全传输

NFS 共享尚不支持双重加密。 Azure 使用 MACSec 为 Azure 数据中心之间传输的所有数据提供加密层。 只能通过受信任的虚拟网络和 VPN 隧道访问 NFS 共享。 NFS 共享上未提供其他传输层加密。

#### <a name="solution"></a>解决方案

禁用存储帐户的配置边栏选项卡中要求的安全传输。

:::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="存储帐户配置边栏选项卡的屏幕截图，其中要求禁用安全传输。":::

### <a name="cause-3-nfs-common-package-is-not-installed"></a>原因 3：未安装 nfs-common 包
在运行 mount 命令之前，请在下面运行发行版特定的命令来安装此包。

若要检查 NFS 包是否已安装，请运行：`rpm qa | grep nfs-utils`

#### <a name="solution"></a>解决方案

如果未安装此包，请在发行版中安装它。

##### <a name="ubuntu-or-debian"></a>Ubuntu 或 Debian

```
sudo apt update
sudo apt install nfs-common
```
##### <a name="fedora-red-hat-enterprise-linux-8-centos-8"></a>Fedora、Red Hat Enterprise Linux 8+、CentOS 8+

使用 dnf 包管理器：`sudo dnf install nfs-utils`。

旧版的 Red Hat Enterprise Linux 和 CentOS 使用 yum 包管理器：`sudo yum install nfs-common`。

##### <a name="opensuse"></a>OpenSUSE

使用 zypper 包管理器：`sudo zypper install-nfscommon`。

### <a name="cause-4-firewall-blocking-port-2049"></a>原因 4：防火墙阻止端口 2049

NFS 协议通过端口 2049 与其服务器通信，请确保此端口已向存储帐户（NFS 服务器）打开。

#### <a name="solution"></a>解决方案

运行以下命令，验证是否已在客户端上打开端口 2049：`telnet <storageaccountnamehere>.file.core.windows.net 2049`。 如果未打开该端口，请将其打开。

## <a name="ls-list-files-command-shows-incorrectinconsistent-results"></a>ls（列表文件）命令显示不正确/不一致的结果

### <a name="cause-inconsistency-between-cached-values-and-server-file-metadata-values-when-the-file-handle-is-open"></a>原因：文件句柄处于打开状态时，缓存值和服务器文件元数据值之间存在不一致
有时，“list files”、“df”或“find”命令会按预期显示非零大小，而在下一个 list files 命令中，则显示大小为 0 或非常旧的时间戳。 这是一个已知问题，因为文件在打开时文件元数据值的缓存不一致。 你可使用以下解决方法之一来解决此问题：

#### <a name="workaround-1-for-fetching-file-size-use-wc--c-instead-of-ls--l"></a>解决方法 1：若要提取文件大小，请使用 wc -c 而不是 ls -l
使用 wc -c 将始终从服务器提取最新值，并且不会有任何不一致。

#### <a name="workaround-2-use-noac-mount-flag"></a>解决方法 2：使用“noac”装载标志
将装载命令与“noac”标志一起使用来重新装载文件系统。 这将始终从服务器提取所有元数据值。 如果使用此解决方法，所有元数据操作可能都会有一些小的性能开销。


## <a name="unable-to-mount-an-nfs-share-that-is-restored-back-from-soft-deleted-state"></a>无法装载从软删除状态恢复的 NFS 共享
预览版期间存在一个已知问题，那就是尽管平台未完全支持 NFS 共享，也会软删除这些共享。 按照例程，这些共享会在过期后被删除。 也可通过“撤销删除共享 + 禁用软删除 + 删除共享”流来提前删除它们。 但是，如果尝试恢复并使用共享，则会在客户端上收到“访问被拒绝”、“权限被拒绝”或 NFS I/O 错误。

## <a name="ls-la-throws-io-error"></a>ls –la 引发 I/O 错误

### <a name="cause-a-known-bug-that-has-been-fixed-in-newer-linux-kernel"></a>原因：在较新的 Linux 内核中修复了已知错误
在较旧的内核上，NFS4ERR_NOT_SAME 会导致客户端停止枚举（而不是重启目录）。 较新的内核将立即解锁，但对于 SUSE 此类发行版，SUSE Enterprise Linux Server 12 或 15 没有补丁可以使内核更新到此修补程序。  该补丁在内核 5.12 及更高版本中可用。  [PATCH v3 15/17 NFS：处理来自 readdir 调用的 NFS4ERR_NOT_SAME 和 NFSERR_BADCOOKIE](https://www.spinics.net/lists/linux-nfs/msg80096.html) 中描述了适用于客户端修补程序的补丁。

#### <a name="workaround-use-latest-kernel-workaround-while-the-fix-reaches-the-region-hosting-your-storage-account"></a>解决方法：在修补程序到达托管存储帐户的区域时使用最新的内核解决方法
该补丁在内核 5.12 及更高版本中可用。

## <a name="ls-hangs-for-large-directory-enumeration-on-some-kernels"></a>ls 在某些内核上挂起以进行大型目录枚举

### <a name="cause-a-bug-was-introduced-in-linux-kernel-v511-and-was-fixed-in-v5125"></a>原因：Linux 内核 v5.11 中引入了一个 bug，并且已在 v5.12.5 中修复。  
某些内核版本存在 bug，该 bug 会使目录列表生成无限的 READDIR 序列。 非常小的目录（其中所有条目均可在一次调用中传递）不会产生此问题。
Linux 内核 v5.11 中引入了一个 bug，并且已在 v5.12.5 中修复。 这两个版本之间的版本均存在 bug。 已知 RHEL 8.4 具有此内核版本。

#### <a name="workaround-downgrading-or-upgrading-the-kernel"></a>解决方法：降级或升级内核
将内核降级或升级到受影响内核之外的任何版本都将解决该问题

## <a name="df-and-find-command-shows-inconsistent-results-on-clients-other-than-where-the-writes-happen"></a>df 和 find 命令会在除发生写入以外的客户端上显示不一致的结果
这是一个已知问题。 Microsoft 正在努力解决该问题。

## <a name="application-fails-with-error-underlying-file-changed-by-an-external-force-when-using-exclusive-open"></a>使用独占 OPEN 时，应用程序失败并显示错误“基础文件被外力更改” 
这是一个已知问题。 Microsoft 正在努力解决该问题。

## <a name="need-help-contact-support"></a>需要帮助？ 请联系支持人员。
如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，以快速解决问题。
