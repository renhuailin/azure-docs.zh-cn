---
title: Azure Blob 存储中的网络文件系统3.0 支持 (预览) |Microsoft Docs
description: Blob 存储现在支持网络文件系统 (NFS) 3.0 协议。 这种支持使 Windows 和 Linux 客户端能够在 Blob 存储中从 Azure 虚拟机 (VM) 或本地运行的计算机上装载容器。
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 02/19/2021
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: a49c51d2afd464e7bea910ae0abe3dd02e939dbc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101718483"
---
# <a name="network-file-system-nfs-30-protocol-support-in-azure-blob-storage-preview"></a>Azure Blob 存储中的网络文件系统 (NFS) 3.0 协议支持 (预览) 

Blob 存储现在支持网络文件系统 (NFS) 3.0 协议。 此支持在对象存储规模和价格上提供 Linux 文件系统兼容性，并使 Windows 或 Linux 客户端能够在 Blob 存储中从 Azure 虚拟机 (VM) 或本地计算机装载容器。 

> [!NOTE]
> Azure Blob 存储中的 NFS 3.0 协议支持提供公共预览版。 它支持在以下区域中具有标准层性能的 GPV2 存储帐户：澳大利亚东部、韩国中部和美国中南部。 预览版还支持所有公共区域中具有高级性能层的块 blob。

运行大规模旧工作负荷（例如高性能计算）在云中 (HPC) ，这始终是一项挑战。 原因之一是，应用程序通常使用传统的文件协议，如 NFS 或服务器消息块 (SMB) 来访问数据。 此外，本机云存储服务侧重于具有平面命名空间和广泛元数据的对象存储，而不是提供分层命名空间和高效的元数据操作的文件系统。 

Blob 存储现在支持分层命名空间，当与 NFS 3.0 协议支持结合使用时，Azure 可让你更轻松地在大规模云对象存储之上运行旧版应用程序。 

## <a name="applications-and-workloads-suited-for-this-feature"></a>适用于此功能的应用程序和工作负荷

NFS 3.0 协议功能最适合用于处理高吞吐量、大规模、读取繁重的工作负荷，例如媒体处理、风险模拟和基因组学排序。 对于使用多个读取器和多个需要高带宽的线程的任何其他类型的工作负荷，应考虑使用此功能。 

## <a name="nfs-30-and-the-hierarchical-namespace"></a>NFS 3.0 和分层命名空间

NFS 3.0 协议支持需要将 blob 组织到分层命名空间中。 创建存储帐户时，可以启用分层命名空间。 Azure Data Lake Storage Gen2 引入了使用分层命名空间的功能。 它将 (文件) 的对象组织成目录和子目录的层次结构，其方式与计算机上文件系统的组织方式相同。  分层命名空间以线性方式缩放，并且不会降低数据容量或性能。 不同协议从分层命名空间扩展。 NFS 3.0 协议是下列可用协议之一。   

> [!div class="mx-imgBorder"]
> ![分层命名空间](./media/network-protocol-support/hierarchical-namespace-and-nfs-support.png)
  
## <a name="data-stored-as-block-blobs"></a>存储为块 blob 的数据

如果启用 NFS 3.0 协议支持，存储帐户中的所有数据都将存储为块 blob。 块 blob 经过优化，可以有效地处理大量的读繁重数据。 块 blob 由块组成。 每个块由块 ID 标识。 块 blob 最多可包含50000块。 块 blob 中的每个块可以有不同的大小，最大可为帐户使用的服务版本所允许的最大大小。

当应用程序使用 NFS 3.0 协议发出请求时，会将该请求转换为块 blob 操作的组合。 例如，NFS 3.0 读取远程过程调用 (RPC) 请求转换为 [获取 Blob](/rest/api/storageservices/get-blob) 操作。 NFS 3.0 写入 RPC 请求会转换为 " [获取块列表](/rest/api/storageservices/get-block-list)"、" [put 块](/rest/api/storageservices/put-block)" 和 " [put 块列表](/rest/api/storageservices/put-block-list)" 的组合。

## <a name="general-workflow-mounting-a-storage-account-container"></a>常规工作流：装载存储帐户容器

你的 Windows 或 Linux 客户端可以从 Azure 虚拟机 (VM) 或本地计算机中的 Azure 虚拟机装载容器。 若要装载存储帐户容器，则必须执行这些操作。

1. 将 NFS 3.0 协议功能注册到你的订阅。

2. 验证功能是否已注册。

3.  (VNet) 创建 Azure 虚拟网络。

4. 配置网络安全。

5. 创建并配置仅接受来自 VNet 的流量的存储帐户。

6. 在存储帐户中创建一个容器。

7. 装载容器。

有关分步指南，请参阅 [使用网络文件系统装载 Blob 存储 (NFS) 3.0 协议 (预览) ](network-file-system-protocol-support-how-to.md)。

> [!IMPORTANT]
> 务必按顺序完成这些任务。 你无法在你的帐户上启用 NFS 3.0 协议之前装入你创建的容器。 此外，在你的帐户上启用了 NFS 3.0 协议后，你将无法禁用它。

## <a name="network-security"></a>网络安全

存储帐户必须包含在 VNet 中。 VNet 使客户端可以安全地连接到你的存储帐户。 保护帐户中数据的唯一方法是使用 VNet 和其他网络安全设置。 用于保护数据的任何其他工具（包括帐户密钥授权、Azure Active Directory (AD) 安全性和访问控制列表 () Acl）在其上启用了 NFS 3.0 协议支持的帐户中不受支持。 

若要了解详细信息，请参阅 [Blob 存储的网络安全建议](security-recommendations.md#networking)。

## <a name="supported-network-connections"></a>支持的网络连接

客户端可以通过公共或 [专用终结点](../common/storage-private-endpoints.md)进行连接，并且可以从以下任意网络位置进行连接：

- 为存储帐户配置的 VNet。 

  在本文中，我们将该 VNet 称为 *主 vnet*。 若要了解详细信息，请参阅 [从虚拟网络授予访问权限](../common/storage-network-security.md#grant-access-from-a-virtual-network)。

- 与主 VNet 位于同一区域的对等互连 VNet。

  需要配置存储帐户以允许访问此对等互连 VNet。 若要了解详细信息，请参阅 [从虚拟网络授予访问权限](../common/storage-network-security.md#grant-access-from-a-virtual-network)。

- 使用 [VPN 网关](../../vpn-gateway/vpn-gateway-about-vpngateways.md) 或 [ExpressRoute 网关](../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)连接到主 VNet 的本地网络。 

  若要了解详细信息，请参阅 [配置来自本地网络的访问权限](../common/storage-network-security.md#configuring-access-from-on-premises-networks)。

- 连接到对等互连网络的本地网络。

  这可以通过使用 [VPN 网关](../../vpn-gateway/vpn-gateway-about-vpngateways.md) 或 [ExpressRoute 网](../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md) 关以及 [网关传输](/azure/architecture/reference-architectures/hybrid-networking/vnet-peering#gateway-transit)来实现。 

> [!IMPORTANT]
> 如果要从本地网络连接，请确保客户端允许通过端口111和2048进行传出通信。 NFS 3.0 协议使用这些端口。

## <a name="azure-storage-features-not-yet-supported"></a>尚不支持 Azure 存储功能

在你的帐户上启用 NFS 3.0 协议后，不支持以下 Azure 存储功能。 

- Azure Active Directory (AD) 安全性

- 类似于 POSIX 的访问控制列表 (Acl) 

- 能够对现有存储帐户启用 NFS 3.0 支持

- 支持在存储帐户中禁用 NFS 3.0 支持的功能 (在启用后) 

- 能够使用 REST Api 或 Sdk 写入 blob。 
  
## <a name="nfs-30-features-not-yet-supported"></a>尚不支持 NFS 3.0 功能

Azure Data Lake Storage Gen2 尚不支持以下 NFS 3.0 功能。

- 经由 UDP 的 NFS 3.0。 仅支持通过 TCP 的 NFS 3.0。

- 通过网络锁管理器 (NLM) 锁定文件。 装载命令必须包含 `-o nolock` 参数。

- 装载子目录。 只能 (容器) 中装载根目录。

- 列出装载 (例如：通过使用命令 `showmount -a`) 

- 列出导出 (例如：通过使用命令 `showmount -e`) 

- 硬链接

- 以只读方式导出容器

## <a name="pricing"></a>定价

在预览期间，存储在存储帐户中的数据按每 GB 每 GB 的每 GB 的相同容量费率计费。 

在预览期间，不会对事务收费。 交易的价格可能会有所变化，并将在正式发布时确定。

## <a name="next-steps"></a>后续步骤

- 若要开始，请参阅 [使用网络文件系统装载 Blob 存储 (NFS) 3.0 协议 (预览) ](network-file-system-protocol-support-how-to.md)。

- 若要优化性能，请参阅 [Azure Blob 存储中的网络文件系统 (NFS) 3.0 性能注意事项 (预览) ](network-file-system-protocol-support-performance.md)。