---
title: 可用的 Azure 文件协议 - NFS 和 SMB
description: 在创建 Azure 文件共享之前，请先了解可用协议，包括服务器消息块 (SMB) 和网络文件系统 (NFS)。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 792f869aff0e7a91eeacd23e52c795d2c1243c0c
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110062373"
---
# <a name="azure-file-share-protocols"></a>Azure 文件共享协议

Azure 文件存储提供了两个协议，用于连接和装载 Azure 文件共享。 [服务器消息块 (SMB) 协议](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview)和[网络文件系统 (NFS) 协议](https://en.wikipedia.org/wiki/Network_File_System)（预览版）。 Azure 文件存储目前不支持多协议访问，因此每个共享项只能是 NFS 共享或者是 SMB 共享。 因此，建议在创建 Azure 文件共享之前，先确定最能满足需求的协议。

## <a name="differences-at-a-glance"></a>区别速览

|功能  |NFS（预览版）  |SMB  |
|---------|---------|---------|
|访问协议     |NFS 4.1         |SMB 3.1.1、SMB 3.0、SMB 2.1         |
|推荐的操作系统     |Linux 内核版本 4.3+         |Windows 2008 R2 + 和 Linux 内核版本 4.11 +         |
|[可用层](storage-files-planning.md#storage-tiers)     |高级存储         |高级存储、事务优化、热存储层、冷存储层         |
|计费模式         |[为预配容量付费](./understanding-billing.md#provisioned-model)         |[为“高级”层级的预配容量付费](./understanding-billing.md#provisioned-model)、[为“标准”层级即用即付](./understanding-billing.md#pay-as-you-go-model)         |
|[冗余](storage-files-planning.md#redundancy)     |LRS、ZRS         |LRS、ZRS、GRS         |
|身份验证     |仅限基于主机的身份验证        |基于身份的身份验证，基于用户的身份验证         |
|权限     |UNIX 样式权限         |NTFS 样式权限         |
|文件系统语义     |POSIX 合规性         |非 POSIX 合规性         |
|事例敏感性     |区分大小写         |不区分大小写         |
|硬链接支持     |支持         |不支持         |
|符号链接支持     |支持         |不支持         |
|删除或修改打开的文件     |支持         |不支持         |
|锁定     |字节范围的公告网络锁管理器         |支持         |
|公共 IP 安全列表 | 不支持 | 支持|
|协议互操作| 不支持 | FileREST|

## <a name="nfs-shares-preview"></a>NFS 共享（预览版）

使用 NFS 4.1 装载 Azure 文件共享目前处于预览版。 它与 Linux 更紧密的集成。 这是完全符合 POSIX 的产品/服务，是 Unix 和其他 *nix 的操作系统的不同变化形式。 这种企业级文件存储服务可进行纵向扩展以满足你的存储需求，并可由数千个计算实例同时访问。

### <a name="limitations"></a>限制

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

#### <a name="regional-availability"></a>区域可用性

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

### <a name="best-suited"></a>最适用于

Azure 文件存储结合 NFS 适用于：

- 需要符合 POSIX 的文件共享、区分大小写或 Unix 样式权限 (UID/GID) 的工作负载。
- 以 Linux 为中心的工作负载，不需要 Windows 访问权限。

### <a name="security"></a>安全性

所有 Azure 文件存储数据均采用静态加密。 对于动态加密，Azure 可为使用 [MACSec](https://en.wikipedia.org/wiki/IEEE_802.1AE) 在 Azure 数据中心之间传输的所有数据提供加密层。 借此，在 Azure 数据中心之间传输数据时，会存在加密。 不同于使用 SMB 协议的 Azure 文件存储，使用 NFS 协议的文件共享不提供基于用户的身份验证。 NFS 共享的身份验证基于配置的网络安全规则。 因此，为了确保仅为你的 NFS 共享建立安全连接，必须使用服务终结点或专用终结点。 如果要从本地访问共享，除了专用终结点外，还必须设置 VPN 或 ExpressRoute。 来自以下源之外的请求都将被拒绝：

- [一个专用终结点](storage-files-networking-overview.md#private-endpoints)
- [Azure VPN 网关](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
    - [点到站点 (P2S) VPN](../../vpn-gateway/point-to-site-about.md)
    - [站点到站点](../../vpn-gateway/design.md#s2smulti)
- [ExpressRoute](../../expressroute/expressroute-introduction.md)
- [受限的公共终结点](storage-files-networking-overview.md#storage-account-firewall-settings)

有关可用网络选项的详细信息，请参阅 [Azure 文件存储网络注意事项](storage-files-networking-overview.md)。

## <a name="smb-shares"></a>SMB 共享

使用 SMB 装载的 Azure 文件共享提供更多 Azure 文件存储功能，且没有 Azure 文件存储功能限制，因为它已正式发布。

### <a name="features"></a>功能

- Azure 文件同步
- 基于标识的身份验证
- Azure 备份支持
- 快照
- 软删除
- 动态加密和静态加密

### <a name="best-suited"></a>最适用于

Azure 文件存储结合 SMB 适用于：

- 生产环境
- 需要[“功能”](#features)中列出的任何功能的客户

## <a name="next-steps"></a>后续步骤

- [创建 NFS 文件共享](storage-files-how-to-create-nfs-shares.md)
- [创建 SMB 文件共享](storage-how-to-create-file-share.md)