---
title: Azure 文件存储中的 NFS 文件共享（预览版）
description: 了解 Azure 文件存储中托管的使用网络文件系统 (NFS) 协议的文件共享。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/01/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 4e12f578fe8bd2eff97deec7b23656086e80475c
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862525"
---
# <a name="nfs-file-shares-in-azure-files-preview"></a>Azure 文件存储中的 NFS 文件共享（预览版）
Azure 文件存储提供两种用于装载 Azure 文件共享的行业标准协议：[服务器消息块 (SMB)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) 协议和[网络文件系统 (NFS)](https://en.wikipedia.org/wiki/Network_File_System) 协议（预览版）。 通过 Azure 文件存储，可以选择最适合你的工作负荷的文件系统协议。 尽管可以在同一存储帐户中创建 SMB 和 NFS 文件共享，但 Azure 文件共享不支持同时使用 SMB 和 NFS 协议访问单个 Azure 文件共享。 对于所有文件共享，Azure 文件存储提供企业级文件共享，这些共享可以纵向扩展以满足你的存储需求，并且可同时由数千个客户端访问。

本文将介绍 NFS Azure 文件共享。 有关 SMB Azure 文件共享的信息，请参阅 [Azure 文件存储中的 SMB 文件共享](files-smb-protocol.md)。

> [!IMPORTANT]
> 在预览期间，我们不建议使用 NFS 进行生产。 有关已知问题的列表，请参阅对 [Azure NFS 文件共享进行故障排除](storage-troubleshooting-files-nfs.md)一文。

## <a name="common-scenarios"></a>常见方案
NFS 文件共享通常用于以下场景：

- 基于 Linux/UNIX 的应用程序（例如使用 Linux 或 POSIX 文件系统 API 编写的业务线应用程序，即使它们不需要符合 POSIX 规范）的后备存储。
- 需要符合 POSIX 的文件共享、区分大小写或 Unix 样式权限 (UID/GID) 的工作负载。
- 新的应用程序和服务开发，尤其是当该应用程序或服务需要随机 IO 和分层存储时。 

## <a name="features"></a>功能
- 完全符合 POSIX 规范的文件系统。
- 硬链接支持。
- 符号链接支持。
- NFS 文件共享目前仅支持 [4.1 协议规范](https://tools.ietf.org/html/rfc5661)中的大部分功能。 某些功能（例如，各种类型的委托和回调、Kerberos 身份验证和传输中加密）不受支持。


## <a name="security-and-networking"></a>安全和网络
使用 Azure 存储服务加密 (SSE) 对存储在 Azure 文件存储中的所有数据进行静态加密。 存储服务加密的工作方式类似于 Windows 上的 BitLocker：在文件系统级别下对数据进行加密。 由于数据在 Azure 文件共享的文件系统下加密，因此，在将数据编码到磁盘时，无需访问客户端上的基础密钥即可读取或写入 Azure 文件共享。 静态加密同时适用于 SMB 和 NFS 协议。

对于传输中加密，Azure 为使用 [MACSec](https://en.wikipedia.org/wiki/IEEE_802.1AE) 在 Azure 数据中心之间传输的所有数据提供加密层。 借此，在 Azure 数据中心之间传输数据时，会存在加密。 不同于使用 SMB 协议的 Azure 文件存储，使用 NFS 协议的文件共享不提供基于用户的身份验证。 NFS 共享的身份验证基于配置的网络安全规则。 因此，为了确保仅为你的 NFS 共享建立安全连接，必须使用服务终结点或专用终结点。 如果要从本地访问共享，除了专用终结点外，还必须设置 VPN 或 ExpressRoute。 来自以下源之外的请求都将被拒绝：

- [一个专用终结点](storage-files-networking-overview.md#private-endpoints)
- [Azure VPN 网关](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
    - [点到站点 (P2S) VPN](../../vpn-gateway/point-to-site-about.md)
    - [站点到站点](../../vpn-gateway/design.md#s2smulti)
- [ExpressRoute](../../expressroute/expressroute-introduction.md)
- [受限的公共终结点](storage-files-networking-overview.md#storage-account-firewall-settings)

有关可用网络选项的详细信息，请参阅 [Azure 文件存储网络注意事项](storage-files-networking-overview.md)。

## <a name="support-for-azure-storage-features"></a>支持 Azure 存储功能

下表显示了已启用 NFS 4.1 功能的帐户中当前对 Azure 存储功能的支持级别。 

随着支持继续扩展，这些表中显示的项的状态会随时间而变化。

| 存储功能 | 受 NFS 共享的支持 |
|-----------------|---------|
| [文件管理平面 REST API](/rest/api/storagerp/file-shares) | ✔️ |
| [文件数据平面 REST API](/rest/api/storageservices/file-service-rest-api)| ⛔ |
| 静态加密|   ✔️ |
| 传输中加密| ⛔ |
| [LRS 或 ZRS 冗余类型](storage-files-planning.md#redundancy)|  ✔️ |
| [专用终结点](storage-files-networking-overview.md#private-endpoints) | ✔️  |
| 子目录装载|  ✔️ |
| [授予对特定 Azure 虚拟网络的网络访问权限](storage-files-networking-endpoints.md#restrict-access-to-the-public-endpoint-to-specific-virtual-networks)|  ✔️  |
| [授予对特定 IP 地址的网络访问权限](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#grant-access-from-an-internet-ip-range)| ⛔ |
| [高级层](storage-files-planning.md#storage-tiers) |  ✔️  |
| [标准层（“热”、“冷”和“事务优化”）](storage-files-planning.md#storage-tiers)| ⛔ |
| [POSIX 权限](https://en.wikipedia.org/wiki/File-system_permissions#Notation_of_traditional_Unix_permissions)|  ✔️  |
| Root squash|  ✔️  |
| [基于标识的身份验证](storage-files-active-directory-overview.md) | ⛔ |
| [Azure 文件共享软删除](storage-files-prevent-file-share-deletion.md) | ⛔  |
| [Azure 文件同步](../file-sync/file-sync-introduction.md)| ⛔ |
| [Azure 文件共享备份](../../backup/azure-file-share-backup-overview.md)| ⛔ |
| [Azure 文件共享快照](storage-snapshots-files.md)| ⛔ |
| [GRS 或 GZRS 冗余类型](storage-files-planning.md#redundancy)| ⛔ |
| [AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)| ⛔ |
| Azure 存储资源管理器| ⛔ |
| 在现有存储帐户中创建 NFS 共享*| ⛔ |
| 支持 16 个以上的组| ⛔ |

\*如果存储帐户是在注册 NFS 之前创建的，则不能将它用于 NFS。 只能使用在注册 NFS 之后创建的存储帐户。

## <a name="regional-availability"></a>区域可用性

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="performance"></a>性能
NFS Azure 文件共享仅适用于高级文件共享，后者在固态硬盘 (SSD) 上存储数据。 NFS 共享的 IOPS 和吞吐量随预配的容量进行扩展。 请参阅“了解账单”一文中的[预配模型](understanding-billing.md#provisioned-model)部分，以了解 IOPS、IO 突发和吞吐量的公式。 IO 大小较小时，平均 IO 延迟以低个位数毫秒计算，而平均元数据延迟以高个位数毫秒计算。 由于打开和关闭操作次数较高，元数据繁重操作（如解压缩）和工作负荷（如 WordPress）可能会面临额外延迟。

## <a name="workloads"></a>工作负荷
> [!IMPORTANT]
> 在预览期间，我们不建议使用 NFS 进行生产。 有关已知问题的列表，请参阅[对 Azure NFS 文件共享进行故障排除](storage-troubleshooting-files-nfs.md)一文。

经验证，NFS 预览版可以很好地处理工作负荷，例如常规用途文件服务器主目录和应用程序工作负荷的内容存储库。

以下工作负荷包含已知问题。 有关已知问题的列表，请参阅[对 Azure NFS 文件共享进行故障排除](storage-troubleshooting-files-nfs.md)一文：
- IBM MQ 将遭遇锁定问题。
- Oracle Database 将无法与其 dNFS 功能兼容。
- 由于[含 ls -l 的已知活动问题](storage-troubleshooting-files-nfs.md#ls-la-throws-io-error)，SAP 应用程序层将遇到行为不一致问题。


## <a name="next-steps"></a>后续步骤
- [创建 NFS 文件共享](storage-files-how-to-create-nfs-shares.md)
- [比较使用 NFS 对 Azure 文件存储、Blob 存储和 Azure NetApp 文件的访问](../common/nfs-comparison.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
