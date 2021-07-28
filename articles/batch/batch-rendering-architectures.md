---
title: Azure 渲染参考体系结构
description: 使用 Azure Batch 和其他 Azure 服务以迸发到云的方式扩展本地渲染场时对应的体系结构
ms.date: 02/07/2019
ms.topic: how-to
ms.custom: seodec18
ms.openlocfilehash: abd67312c9ff8d74cc2a73d9750daca80f28391b
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110481374"
---
# <a name="reference-architectures-for-azure-rendering"></a>Azure 渲染的参考体系结构

本文介绍的高级体系结构图对应于将本地渲染场扩展或“迸发”到 Azure 时的场景。 示例显示了适合 Azure 计算、网络和存储服务的不同选项。

## <a name="hybrid-with-nfs-or-cfs"></a>与 NFS 或 CFS 的混合

下图显示了一个混合场景，其中包括以下 Azure 服务：

* **计算** - Azure Batch 池或虚拟机规模集。

* **网络** - 本地：Azure ExpressRoute 或 VPN。 Azure：Azure VNet。

* **存储** - 输入和输出文件：使用 Azure VM 的 NFS 或 CFS，通过 Azure 文件同步或 RSync 与本地存储同步。 也可使用以下命令：使用 NFS 从本地 NAS 设备输入或输出文件的 Avere vFXT。

  ![云迸发 - 与 NFS 或 CFS 的混合](./media/batch-rendering-architectures/hybrid-nfs-cfs-avere.png)

## <a name="hybrid-with-blobfuse"></a>与 Blobfuse 的混合

下图显示了一个混合场景，其中包括以下 Azure 服务：

* **计算** - Azure Batch 池或虚拟机规模集。

* **网络** - 本地：Azure ExpressRoute 或 VPN。 Azure：Azure VNet。

* **存储** - 输入和输出文件：Blob 存储，通过 Azure Blobfuse 装载到计算资源。

  ![云迸发 - 与 Blobfuse 的混合](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>混合计算和存储

下图显示了一个完全连接的针对计算和存储的混合场景，其中包括以下 Azure 服务：

* **计算** - Azure Batch 池或虚拟机规模集。

* **网络** - 本地：Azure ExpressRoute 或 VPN。 Azure：Azure VNet。

* **存储** - 跨界：Avere vFXT。 通过 Azure Data Box 以可选方式将本地文件存档到 Blob 存储，或通过本地 Avere vFXT 加速 NAS。

  ![云迸发 - 混合计算和存储](./media/batch-rendering-architectures/hybrid-compute-storage-avere.png)

## <a name="next-steps"></a>后续步骤

* 详细了解用于[在 Azure 中渲染](batch-rendering-service.md)的选项。
* 了解如何将[渲染应用程序与 Batch 一起使用](batch-rendering-applications.md)。
