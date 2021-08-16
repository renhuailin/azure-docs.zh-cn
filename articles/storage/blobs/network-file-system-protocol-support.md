---
title: Azure Blob 存储中的网络文件系统 3.0 支持（预览版）| Microsoft Docs
description: Blob 存储现在支持网络文件系统 (NFS) 3.0 协议。 通过此支持，Linux 客户端可以从 Azure 虚拟机 (VM) 或本地运行的计算机中，将容器装载在 Blob 存储中。
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 04/28/2021
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: 709f4ed6fb57dc11d4a2b8672f253664835f20e3
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111965021"
---
# <a name="network-file-system-nfs-30-protocol-support-in-azure-blob-storage-preview"></a>Azure Blob 存储中的网络文件系统 (NFS) 3.0 协议支持（预览版）

Blob 存储现在支持网络文件系统 (NFS) 3.0 协议。 此支持在对象存储规模和价格上提供了 Linux 文件系统兼容性，并使 Linux 客户端可以从 Azure 虚拟机 (VM) 或本地计算机中，将容器装载在 Blob 存储中。 

> [!NOTE]
> Azure Blob 存储中的 NFS 3.0 协议支持目前处于公共预览版状态。 它支持所有公共区域中具有标准层性能的 GPV2 存储帐户和具有高级性能层的块 blob 存储帐户。

运行大规模的旧工作负载始终是一项挑战，例如云中的高性能计算 (HPC)。 其中一个原因是，应用程序经常使用 NFS 或服务器消息块 (SMB) 等传统文件协议来访问数据。 此外，本机云存储服务侧重于具有平面命名空间和大量元数据的对象存储，而不是提供分层命名空间和高效元数据操作的文件系统。 

Blob 存储现在支持分层命名空间，并且当 Azure 与 NFS 3.0 协议支持相结合时，Azure 使得在大规模云对象存储之上运行旧版应用程序变得更加容易。 

## <a name="applications-and-workloads-suited-for-this-feature"></a>此功能适用的应用程序和工作负载

NFS 3.0 协议功能最适合处理高吞吐量、大规模、读取繁重的工作负载，例如媒体处理、风险模拟和基因组测序。 你应该考虑将此功能用于使用多个读取器和许多线程的任何其他类型的工作负载，这需要很高的带宽。 

## <a name="nfs-30-and-the-hierarchical-namespace"></a>NFS 3.0 和分层命名空间

NFS 3.0 协议支持要求将 blob 组织到分层命名空间中。 创建存储帐户时，可以启用分层命名空间。 Azure Data Lake Storage Gen2 引入了使用分层命名空间的功能。 它将对象（文件）组织到目录和子目录的层次结构中，就像组织计算机上的文件系统一样。  分层命名空间以线性方式缩放，并且不会降低数据容量或性能。 不同协议从分层命名空间扩展而来。 NFS 3.0 协议是以下可用协议之一。   

> [!div class="mx-imgBorder"]
> ![分层命名空间](./media/network-protocol-support/hierarchical-namespace-and-nfs-support.png)
  
## <a name="data-stored-as-block-blobs"></a>存储为块 blob 的数据

如果启用 NFS 3.0 协议支持，存储帐户中的所有数据都将存储为块 blob。 块 blob 经过优化，可以有效地处理大量读取繁重的数据。 块 blob 由多个块组成。 每个块由块 ID 标识。 一个块 blob 最多可包含 5 万个块。 块 blob 中的每个块可以有不同的大小，最大可以达到帐户使用的服务版本所允许的最大值。

当应用程序使用 NFS 3.0 协议发出请求时，该请求将转换为块 blob 操作的组合。 例如，NFS 3.0 读取远程过程调用 (RPC) 的请求被转换为[获取 Blob](/rest/api/storageservices/get-blob) 操作。 NFS 3.0 写入 RPC 的请求被转换为[获取块列表](/rest/api/storageservices/get-block-list)、[放置块](/rest/api/storageservices/put-block)和[放置块列表](/rest/api/storageservices/put-block-list)的组合。

## <a name="general-workflow-mounting-a-storage-account-container"></a>常规工作流：装载存储帐户容器

Linux 客户端可以从 Azure 虚拟机 (VM) 或本地计算机中，将容器装载在 Blob 存储中。 若要装载存储帐户容器，则必须执行以下操作。

1. 将 NFS 3.0 协议功能注册到订阅。

2. 验证功能是否已注册。

3. 创建 Azure 虚拟网络 (VNet)。

4. 配置网络安全。

5. 创建并配置仅接受来自 VNet 的流量的存储帐户。

6. 在存储帐户中创建容器。

7. 装载容器。

有关分步指南，请参阅[使用网络文件系统 (NFS) 3.0 装载 Blob 存储（预览版）](network-file-system-protocol-support-how-to.md)。

> [!IMPORTANT]
> 务必按顺序完成这些任务。 在帐户上启用 NFS 3.0 协议之前，无法装载创建的容器。 此外，在帐户上启用了 NFS 3.0 协议后，将无法禁用它。

## <a name="network-security"></a>网络安全性

存储帐户必须包含在 VNet 中。 VNet 使客户端可以安全地连接到存储帐户。 保护帐户中数据的唯一方法是使用 VNet 和其他网络安全设置。 用于保护数据的任何其他工具（包括帐户密钥授权、Azure Active Directory [AD] 安全性）以及访问控制列表 (ACL) 在其上启用了 NFS 3.0 协议支持的帐户中不受支持。 

若要了解详细信息，请参阅 [Blob 存储的网络安全建议](security-recommendations.md#networking)。

## <a name="supported-network-connections"></a>受支持的网络连接

客户端可以通过公共终结点或[专用终结点](../common/storage-private-endpoints.md)进行连接，并且可以从以下任意网络位置进行连接：

- 为存储帐户配置的 VNet。 

  在本文中，我们将该 VNet 称为主 VNet。 有关详细信息，请参阅[允许从虚拟网络进行访问](../common/storage-network-security.md#grant-access-from-a-virtual-network)。

- 与主 VNet 位于同一区域的对等 VNet。

  必须配置存储帐户以允许访问此对等 VNet。 有关详细信息，请参阅[允许从虚拟网络进行访问](../common/storage-network-security.md#grant-access-from-a-virtual-network)。

- 使用 [VPN 网关](../../vpn-gateway/vpn-gateway-about-vpngateways.md)或 [ExpressRoute 网关](../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)连接到主 VNet 的本地网络。 

  若要了解详细信息，请参阅[配置从本地网络的访问](../common/storage-network-security.md#configuring-access-from-on-premises-networks)。

- 连接到对等网络的本地网络。

  这可以通过使用 [VPN 网关](../../vpn-gateway/vpn-gateway-about-vpngateways.md)或 [ExpressRoute 网关](../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)以及[网关传输](/azure/architecture/reference-architectures/hybrid-networking/vnet-peering#gateway-transit)来实现。 

> [!IMPORTANT]
> 如果要从本地网络连接，请确保客户端允许通过端口 111 和 2048 进行传出通信。 NFS 3.0 协议使用这些端口。

<a id="azure-storage-features-not-yet-supported"></a>

## <a name="support-for-azure-storage-features"></a>支持 Azure 存储功能

下表显示了已启用 NFS 3.0 功能的帐户中当前对 Azure 存储功能的支持级别。 

随着支持继续扩展，这些表中显示的项的状态会随时间而变化。

| 存储功能 | 高级 | 标准 |存储功能 | 高级 | 标准 |
|-----------------|---------|----------|----------------|---------|----------|
| [Blob 服务 REST API](/rest/api/storageservices/blob-service-rest-api)  | ✔️ |  ✔️ | [Azure Data Lake Store REST API](/rest/api/storageservices/data-lake-storage-gen2) | ✔️ |  ✔️ |
| [Azure Blob 存储的访问层](storage-blob-storage-tiers.md) |    ✔️ |    ✔️ | [Blob 索引标记](storage-blob-index-how-to.md) |  ⛔ | ⛔ |
| [Azure Blob 存储生命周期管理](storage-lifecycle-management-concepts.md) | ✔️  |   ✔️ | [Azure 存储分析日志记录](../common/storage-analytics-logging.md?toc=/azure/storage/blobs/toc.json) | ⛔ |  ⛔ |
|  [Azure 存储 Blob 清单](blob-inventory.md) |  ✔️  |   ✔️ | [更改源](storage-blob-change-feed.md) |   ⛔ | ⛔ |
| [Azure Monitor](monitor-blob-storage.md) |    ✔️ |    ✔️ | [Blob 版本控制](versioning-enable.md) | ⛔ |  ⛔ |
| [blob 快照](snapshots-overview.md) | ✔️  |   ✔️ | [块 blob 的时间点还原](point-in-time-restore-overview.md) | ⛔ |   ⛔ |
| [专用终结点](../common/storage-private-endpoints.md?toc=/azure/storage/blobs/toc.json) | ✔️  | ✔️ | [Azure 备份集成](../../backup/blob-backup-overview.md) | ⛔ | ⛔ |
| [服务终结点](../../virtual-network/virtual-network-service-endpoints-overview.md) | ✔️  |  ✔️ | [容器软删除](soft-delete-container-overview.md) |  ⛔ | ⛔ |
| [防火墙规则](../common/storage-network-security.md?toc=/azure/storage/blobs/toc.json) | ✔️  | ✔️ | [blob 的软删除](soft-delete-blob-overview.md) |    ⛔ | ⛔ |
| [禁止共享密钥授权](../common/shared-key-authorization-prevent.md)  | ✔️ |    ✔️ | [用于生命周期管理的上次访问时间跟踪](storage-lifecycle-management-concepts.md#move-data-based-on-last-accessed-date-preview) | ⛔|  ⛔ |
| [用于 Azure 存储加密的客户管理的密钥](../common/customer-managed-keys-overview.md) |   ✔️ |    ✔️ | [用于 Azure 存储加密的客户提供的密钥](encryption-customer-provided-keys.md)  | ⛔ | ⛔ |
| [不可变 Blob 存储](storage-blob-immutable-storage.md) | ✔️    | ✔️ | [静态网站托管](storage-blob-static-website.md) |    ⛔  |    ⛔ |
| [追加​​ Blob](storage-blobs-introduction.md#blobs) | ✔️   |  ✔️ | [页 Blob](storage-blobs-introduction.md#blobs) | ⛔ |    ⛔ |
| [Azure Active Directory (AD) 安全性](../common/storage-auth-aad.md?toc=/azure/storage/blobs/toc.json) | ⛔ | ⛔ | [加密范围](encryption-scope-overview.md)  |    ⛔ | ⛔ |
| [块 blob 的对象复制](object-replication-overview.md) | ⛔  |   ⛔ | [客户管理的帐户故障转移](../common/storage-disaster-recovery-guidance.md?toc=/azure/storage/blobs/toc.json) | ⛔ |    ⛔ |
| [Blob 存储事件](storage-blob-event-overview.md)| ⛔ |    ⛔ 

  
## <a name="known-issues"></a>已知问题

- 无法在现有存储帐户上启用 NFS 3.0 支持。

- 启用 NFS 3.0 支持后，无法在存储帐户中将其禁用。

### <a name="nfs-30-features-not-yet-supported"></a>尚不支持 NFS 3.0 功能

尚不支持以下 NFS 3.0 功能。

- 基于 UDP 的 NFS 3.0。 仅支持基于 TCP 的 NFS 3.0。

- 使用网络锁定管理器 (NLM) 锁定文件。 装载命令必须包含 `-o nolock` 参数。

- 装载子目录。 只能装载根目录（容器）。

- 列出装载（例如：通过使用 `showmount -a` 命令）

- 列出导出（例如：通过使用 `showmount -e` 命令）

- 硬链接

- 以只读方式导出容器

### <a name="nfs-30-clients-not-yet-supported"></a>尚不支持 NFS 3.0 客户端

尚不支持以下 NFS 3.0 客户端。

- Windows NFS 客户端

## <a name="pricing"></a>定价

在预览期间，存储在存储帐户中的数据采用与 Blob 存储每月每 GB 收取的相同容量费率计费。 

预览期间不会对事务收费。 事务的定价可能会发生变化，并将在正式发布时确定。

## <a name="next-steps"></a>后续步骤

- 若要开始操作，请参阅[使用网络文件系统 (NFS) 3.0 协议装载 Blob 存储（预览版）](network-file-system-protocol-support-how-to.md)。

- 要优化性能，请参阅 [Azure Blob 存储中的网络文件系统 (NFS) 3.0 性能注意事项（预览版）](network-file-system-protocol-support-performance.md)。
