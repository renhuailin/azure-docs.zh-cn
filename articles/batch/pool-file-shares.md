---
title: Azure Batch 池的 Azure 文件共享
description: 如何在 Azure Batch 中从 Linux 或 Windows 池中的计算节点装载 Azure 文件共享。
ms.topic: how-to
ms.date: 08/23/2021
ms.openlocfilehash: 2c5cbf1a3e5d74927ddc74c4838c31f68a348876
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772112"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>将 Batch 池与 Azure 文件共享配合使用

[Azure 文件](../storage/files/storage-files-introduction.md)在云中提供完全托管的文件共享，这些共享项可通过服务器消息块 (SMB) 协议进行访问。 可以在 Batch 池计算节点上装载和使用 Azure 文件共享。

## <a name="considerations-for-use-with-batch"></a>与 Batch 配合使用时的注意事项

在使用非高级 Azure 文件存储的情况下，如果池中运行的并行任务数相对较少，请考虑使用 Azure 文件共享。 请查看[性能和可伸缩性目标](../storage/files/storage-files-scale-targets.md)，确定在指定了预期的池大小和资产文件数的情况下，是否应使用 Azure 文件（Azure 文件使用 Azure 存储帐户）。

Azure 文件共享非常[经济高效](https://azure.microsoft.com/pricing/details/storage/files/)，可以使用数据复制将其配置到另一个区域，以实现全局冗余。

可从本地计算机并行装载 Azure 文件共享。 但是，请确保自己了解[并发性的影响](../storage/blobs/concurrency-manage.md)，尤其是在使用 REST API 时。

另请参阅 Azure 文件共享的一般[规划注意事项](../storage/files/storage-files-planning.md)。

## <a name="create-a-file-share"></a>创建文件共享

可以在已链接到 Batch 帐户的存储帐户中创建 Azure 文件共享，也可以在单独的存储帐户中创建。 有关详细信息，请参阅[创建 Azure 文件共享](../storage/files/storage-how-to-create-file-share.md)。

## <a name="mount-an-azure-file-share-on-a-batch-pool"></a>在 Batch 池中装载 Azure 文件共享

要详细了解如何在池中装载 Azure 文件共享，请参阅[在 Batch 池上装载虚拟文件系统](virtual-file-mount.md)。

## <a name="next-steps"></a>后续步骤

- 要了解 Batch 中用于读取和写入数据的其他选项，请参阅[保存作业和任务的输出](batch-task-output.md)。
- 请浏览 [Batch Shipyard](https://github.com/Azure/batch-shipyard) 工具包，其中包含用于部署 Batch 容器工作负荷文件系统的 [Shipyard 脚本](https://github.com/Azure/batch-shipyard/tree/master/recipes)。
