---
title: 装载 Azure NFS 文件共享（预览版）- Azure 文件存储
description: 了解如何装载网络文件系统共享。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 07/01/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 8f3565f05fc04a74e761b1070f0374677703d225
ms.sourcegitcommit: f4e04fe2dfc869b2553f557709afaf057dcccb0b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113225241"
---
# <a name="how-to-mount-an-nfs-file-share-preview"></a>如何装载 NFS 文件共享（预览版）

[Azure 文件](storage-files-introduction.md)是 Microsoft 推出的易用云文件系统。 可以使用服务器消息块 (SMB) 协议或网络文件系统 (NFS) 协议（预览版）在 Linux 发行版中装载 Azure 文件共享。 本文重点介绍如何使用 NFS 进行装载。若要详细了解如何使用 SMB 进行装载，请参阅[在 Linux 中使用 Azure 文件存储](storage-how-to-use-files-linux.md)。 有关每个可用协议的详细信息，请参阅 [Azure 文件共享协议](storage-files-planning.md#available-protocols)。

## <a name="limitations"></a>限制

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>区域可用性

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>先决条件

- [创建 NFS 共享](storage-files-how-to-create-nfs-shares.md)。

    > [!IMPORTANT]
    > 只能从受信任的网络访问 NFS 共享。 与 NFS 共享的连接必须来自以下源之一：

- 使用以下任一网络解决方案：
    - [创建专用终结点](storage-files-networking-endpoints.md#create-a-private-endpoint)（建议）或[限制对公共终结点的访问](storage-files-networking-endpoints.md#restrict-public-endpoint-access)。
    - [在 Linux 上配置点到站点 (P2S) VPN 以与 Azure 文件存储一起使用](storage-files-configure-p2s-vpn-linux.md)。
    - [配置站点到站点 VPN 以与 Azure 文件存储一起使用](storage-files-configure-s2s-vpn.md)。
    - 配置 [ExpressRoute](../../expressroute/expressroute-introduction.md)。

## <a name="disable-secure-transfer"></a>禁用安全传输

1. 登录到 Azure 门户并访问包含你创建的 NFS 共享的存储帐户。
1. 选择“配置”。
1. 对于“需要安全传输”，请选择“禁用”。
1. 选择“保存”。

    :::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="已禁用安全传输的存储帐户配置屏幕的屏幕截图。":::

## <a name="mount-an-nfs-share"></a>装载 NFS 共享

1. 创建文件共享后，选择该共享，然后选择“从 Linux 进行连接”。
1. 输入要使用的装载路径，然后复制该脚本。
1. 连接到客户端，并使用所提供的装载脚本。

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/mount-nfs-file-share-script.png" alt-text="文件共享连接边栏选项卡的屏幕截图。":::

现已装载 NFS 共享。

### <a name="validate-connectivity"></a>验证连接

如果装载失败，则可能是专用终结点未正确设置或无法访问。 若要详细了解如何确认连接性，请参阅“网络终结点”一文中的[验证连接性](storage-files-networking-endpoints.md#verify-connectivity)部分。

## <a name="next-steps"></a>后续步骤

- 有关 Azure 文件存储的详细信息，请参阅我们的文章：[规划 Azure 文件存储部署](storage-files-planning.md)。
- 如果遇到任何问题，请参阅[解决 Azure NFS 文件共享问题](storage-troubleshooting-files-nfs.md)。