---
title: 排查 Azure NFS 文件共享问题-Azure 文件
description: 排查 Azure NFS 文件共享问题。
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/15/2020
ms.author: jeffpatt
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 4c87887f77d5f227fe4d4cdee220397289878d7f
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99574459"
---
# <a name="troubleshoot-azure-nfs-file-shares"></a>排查 Azure NFS 文件共享问题

本文列出了一些与 Azure NFS 文件共享相关的常见问题。 其中提供了这些问题的潜在原因和解决方法。

## <a name="chgrp-filename-failed-invalid-argument-22"></a>chgrp "filename" 失败：参数 (22) 无效

### <a name="cause-1-idmapping-is-not-disabled"></a>原因1：未禁用 idmapping
Azure 文件不允许字母数字 UID/GID。 因此必须禁用 idmapping。 

### <a name="cause-2-idmapping-was-disabled-but-got-re-enabled-after-encountering-bad-filedir-name"></a>原因2： idmapping 已禁用，但遇到错误的文件/目录名称后重新启用
即使已正确禁用 idmapping，在某些情况下，禁用 idmapping 的设置也会被重写。 例如，当 Azure 文件遇到错误的文件名时，它将返回错误。 查看此特定错误代码后，NFS 版本 4.1 Linux 客户端决定重新启用 idmapping，以后的请求会与字母数字 UID/GID 再次发送。 有关 Azure 文件中不支持的字符的列表，请参阅此 [文](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)。 冒号是不受支持的字符之一。 

### <a name="workaround"></a>解决方法
检查 idmapping 是否已禁用并且不会重新启用它，然后执行以下操作：

- 卸载共享
- 禁用 id-映射 # echo Y >/sys/module/nfs/parameters/nfs4_disable_idmapping
- 重新装载共享
- 如果正在运行 rsync，请在目录中运行 rsync，其中没有任何错误的目录/文件名。

## <a name="unable-to-create-an-nfs-share"></a>无法创建 NFS 共享

### <a name="cause-1-subscription-is-not-enabled"></a>原因1：订阅未启用

你的订阅可能尚未注册 Azure 文件 NFS 预览版。 要启用该功能，需要从 Cloud Shell 或本地终端运行一些附加的 commandlet。

> [!NOTE]
> 你可能需要等待30分钟才能完成注册。


#### <a name="solution"></a>解决方案

使用以下脚本注册功能和资源提供程序，并 `<yourSubscriptionIDHere>` 在运行脚本前替换：

```azurepowershell
Connect-AzAccount

#If your identity is associated with more than one subscription, set an active subscription
$context = Get-AzSubscription -SubscriptionId <yourSubscriptionIDHere>
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowNfsFileShares -ProviderNamespace Microsoft.Storage

Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="cause-2-unsupported-storage-account-settings"></a>原因2：不支持的存储帐户设置

NFS 只能在具有以下配置的存储帐户上使用：

- 层-高级
- 帐户类型-FileStorage
- 区域- [受支持区域的列表](./storage-files-how-to-create-nfs-shares.md?tabs=azure-portal#regional-availability)

#### <a name="solution"></a>解决方案

按照本文中的说明进行操作： [如何创建 NFS 共享](storage-files-how-to-create-nfs-shares.md)。

### <a name="cause-3-the-storage-account-was-created-prior-to-registration-completing"></a>原因3：存储帐户是在注册完成之前创建的

为了使存储帐户使用此功能，必须在订阅完成 NFS 注册后创建该帐户。 注册完成最多可能需要30分钟。

#### <a name="solution"></a>解决方案

完成注册后，请按照本文中的说明进行操作： [如何创建 NFS 共享](storage-files-how-to-create-nfs-shares.md)。

## <a name="cannot-connect-to-or-mount-an-azure-nfs-file-share"></a>无法连接或装载 Azure NFS 文件共享

### <a name="cause-1-request-originates-from-a-client-in-an-untrusted-networkuntrusted-ip"></a>原因1：请求来自不受信任的网络/不受信任的 IP 中的客户端

不同于 SMB，NFS 没有基于用户的身份验证。 共享的身份验证基于网络安全规则配置。 因此，为了确保仅为你的 NFS 共享建立安全连接，你必须使用服务终结点或专用终结点。 若要从本地访问共享以及专用终结点，你必须设置 VPN 或 ExpressRoute。 将忽略添加到存储帐户的允许列表中的 Ip。 必须使用以下方法之一来设置对 NFS 共享的访问权限：


- [服务终结点](storage-files-networking-endpoints.md#restrict-public-endpoint-access)
    - 由公共终结点访问
    - 仅在同一区域中可用。
    - VNet 对等互连不会授予对共享的访问权限。
    - 每个虚拟网络或子网必须分别添加到允许列表中。
    - 对于本地访问，服务终结点可用于 ExpressRoute、点到站点和站点到站点 Vpn，但我们建议使用专用终结点，因为它更安全。

下图描述了使用公共终结点的连接。

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg" alt-text="公共终结点连接的关系图。" lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg":::

- [专用终结点](storage-files-networking-endpoints.md#create-a-private-endpoint)
    - 访问比服务终结点更安全。
    - 可在存储帐户的 Azure 区域内部和外部访问 NFS 共享， (跨区域本地) 
    - 虚拟网络对等互连在专用终结点中托管的虚拟网络允许 NFS 共享访问对等互连虚拟网络中的客户端。
    - 专用终结点可用于 ExpressRoute、点到站点和站点到站点 Vpn。

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg" alt-text="专用终结点连接的关系图。" lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg":::

### <a name="cause-2-secure-transfer-required-is-enabled"></a>原因2：需要安全传输

目前尚不支持 NFS 共享。 Azure 为使用 MACSec 的 Azure 数据中心之间传输的所有数据提供加密层。 只能从受信任的虚拟网络和 VPN 隧道访问 NFS 共享。 NFS 共享上没有其他传输层加密可用。

#### <a name="solution"></a>解决方案

禁用存储帐户的配置边栏选项卡中所需的安全传输。

:::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="存储帐户配置边栏选项卡的屏幕截图，禁用需要安全传输。":::

### <a name="cause-3-nfs-common-package-is-not-installed"></a>原因3：未安装 nfs-公共包
在运行 mount 命令之前，请通过运行以下命令来安装包。

若要检查 NFS 包是否已安装，请运行： `rpm qa | grep nfs-utils`

#### <a name="solution"></a>解决方案

如果未安装此包，请在分发中安装包。

##### <a name="ubuntu-or-debian"></a>Ubuntu 或 Debian

```
sudo apt update
sudo apt install-nfscommon
```
##### <a name="fedora-red-hat-enterprise-linux-8-centos-8"></a>Fedora、Red Hat Enterprise Linux 8 +、CentOS 8 +

使用 dnf 包管理器： `sudo dnf install nfs-common` 。

旧版本的 Red Hat Enterprise Linux 和 CentOS 使用 yum 包管理器： `sudo yum install nfs-common` 。

##### <a name="opensuse"></a>openSUSE

使用 zypper 包管理器： `sudo zypper install-nfscommon` 。

### <a name="cause-4-firewall-blocking-port-2049"></a>原因4：防火墙阻止端口2049

NFS 协议通过端口2049与服务器通信，请确保此端口已打开到 NFS) 服务器 (的存储帐户。

#### <a name="solution"></a>解决方案

运行以下命令，验证是否已在客户端上打开端口2049： `telnet <storageaccountnamehere>.file.core.windows.net 2049` 。 如果端口未打开，请将其打开。

## <a name="ls-list-files-shows-incorrectinconsistent-results"></a>ls (列出文件) 显示不正确/不一致的结果

### <a name="cause-inconsistency-between-cached-values-and-server-file-metadata-values-when-the-file-handle-is-open"></a>原因：文件句柄处于打开状态时，缓存值和服务器文件元数据值之间存在不一致
有时，"列出文件" 命令会按预期显示非零大小，在下一列表文件命令中，将显示大小0或非常旧的时间戳。 这是一个已知问题，因为文件在打开时不能缓存文件元数据值。 你可以使用以下解决方法之一来解决此问题：

#### <a name="workaround-1-for-fetching-file-size-use-wc--c-instead-of-ls--l"></a>解决方法1：若要获取文件大小，请使用 wc.exe，而不是 ls-l
使用 wc.exe 将始终从服务器提取最新值，并且不存在任何不一致的情况。

#### <a name="workaround-2-use-noac-mount-flag"></a>解决方法2：使用 "noac" 装载标志
使用装入命令将 "noac" 标志重新装载文件系统。 这将始终从服务器提取所有元数据值。 如果使用此解决方法，所有元数据操作都可能会有一些小的性能开销。

## <a name="need-help-contact-support"></a>需要帮助？ 请联系支持人员。
如果仍需帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)，以快速解决问题。
