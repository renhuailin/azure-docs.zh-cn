---
title: 使用 Azure HPC 缓存和 Azure NetApp 文件
description: 如何使用 Azure HPC 缓存来改善对 Azure NetApp 文件中存储数据的访问
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: e955ddc14bb2b0a7abc0dc815c6955247568876b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "86497006"
---
# <a name="use-azure-hpc-cache-with-azure-netapp-files"></a>将 Azure HPC 缓存与 Azure NetApp 文件搭配使用

可以使用 [Azure NetApp 文件](https://azure.microsoft.com/services/netapp/)作为 Azure HPC 缓存的存储目标。 本文介绍了这两个服务如何协同工作，并提供了设置这些服务的技巧。

Azure NetApp 文件在其 ONTAP 操作系统的基础上，融入了 Microsoft Azure 的可伸缩性和速度。 这种组合允许用户在不重写代码的情况下，将已建立的工作流转移到云中。

添加 Azure HPC 缓存组件后，可以在一个聚合的命名空间中呈现多个 Azure NetApp 文件卷，从而改善文件访问。 它可以为不同服务区域内的卷提供边缘缓存。 它还可以为在较低层服务级别创建的卷提高按需性能，从而节省成本。

## <a name="overview"></a>概述

若要使用 Azure NetApp 文件系统作为 Azure HPC 缓存的后端存储，请按照此过程操作。

1. 根据下方[规划系统](#plan-your-azure-netapp-files-system)中的指南，创建 Azure NetApp 文件系统和卷。
1. 在需要文件访问的区域中创建 Azure HPC 缓存。 （按照[创建 Azure HPC 缓存](hpc-cache-create.md)中的说明进行操作。）
1. 在缓存中[定义存储目标](#create-storage-targets-in-the-cache)以指向 Azure NetApp 文件卷。 为用于访问卷的每个唯一 IP 地址创建一个缓存存储目标。
1. 让客户端[装载 Azure HPC 缓存](#mount-storage-targets)，而不是直接装载 Azure NetApp 文件卷。

## <a name="plan-your-azure-netapp-files-system"></a>规划 Azure NetApp 文件系统

在规划 Azure NetApp 文件系统时，请注意本部分中的各项内容，确保可将其与 Azure HPC 缓存顺利集成。

另外，在创建用于 Azure HPC 缓存的卷之前，请阅读 [Azure NetApp 文件文档](../azure-netapp-files/index.yml)。

### <a name="nfs-client-access-only"></a>仅限 NFS 客户端访问

Azure HPC 缓存目前仅支持 NFS 访问。 不能与 SMB ACL 或 POSIX 模式位卷一起使用。

### <a name="exclusive-subnet-for-azure-netapp-files"></a>Azure NetApp 文件的独占子网

在 Azure NetApp 文件中，所有卷使用一个委托子网。 其他资源不能使用该子网。 同时，Azure NetApp 文件也只能使用虚拟网络中的一个子网。 有关更多信息，请参阅 [Azure NetApp 文件网络规划指南](../azure-netapp-files/azure-netapp-files-network-topologies.md)。

### <a name="delegated-subnet-size"></a>委派子网大小

在创建用于 Azure HPC 缓存的 Azure NetApp 文件系统时，请使用最小规模的委托子网。

最小规模使用网络掩码 /28 指定，提供了 16 个 IP 地址。 实际上，Azure NetApp 文件仅使用其中三个可用的 IP 地址来访问卷。 这意味着，只需在 Azure HPC 缓存中创建三个存储目标，即可涵盖所有卷。

如果委托子网太大，则 Azure NetApp 文件卷可能使用太多的 IP 地址，超出了单个 Azure HPC 缓存实例的处理能力。 单个缓存最多可以有 10 个存储目标。

Azure NetApp 文件文档中的快速入门示例使用 10.7.0.0/16 作为委托子网，给出的子网规模过大。

### <a name="capacity-pool-service-level"></a>容量池服务级别

为容量池选择服务级别时，需考虑到工作流。 如果经常将数据写回到 Azure NetApp 文件卷，那么写回时间较慢可能导致缓存性能受限。 为经常写入的卷选择较高的服务级别。

如果卷对应的服务级别较低，则在任务开始，缓存预填充内容时，可能存在一些延迟。 使用一组运行良好的文件，让缓存启动并正常运行后，延迟应变得不明显。

提前规划容量池服务级别非常重要，因为创建后将无法更改。 需要在不同的容量池中创建新卷，然后将数据复制到其中。

请注意，可以在不中断访问的情况下，更改卷的存储配额和容量池的大小。

## <a name="create-storage-targets-in-the-cache"></a>在缓存中创建存储目标

设置 Azure NetApp 文件系统并创建 Azure HPC 缓存后，在缓存中定义指向文件系统卷的存储目标。

为 Azure NetApp 文件卷使用的每个 IP 地址创建一个存储目标。 此 IP 地址列在卷的装载说明页中。

如果多个卷共享同一 IP 地址，则可以为所有卷使用一个存储目标。  

按照 [Azure NetApp 文件文档中的装载说明](../azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)，查找要使用的 IP 地址。

还可以使用 Azure CLI 查找 IP 地址：

```azurecli
az netappfiles volume list -g ${RESOURCE_GROUP} --account-name ${ANF_ACCOUNT} --pool-name ${POOL} --query "[].mountTargets[].ipAddress" | grep -Ee '[0-9]+[.][0-9]+[.][0-9]+[.][0-9]+' | tr -d '"' | tr -d , | sort | uniq
```

Azure NetApp 文件系统上的导出名称有一个路径组件。 请勿尝试在 Azure NetApp 文件中为根导出 ``/`` 创建存储目标，因为该导出不提供文件访问。

对于这些存储目标的虚拟命名空间路径，没有特别的限制。

## <a name="mount-storage-targets"></a>装载存储目标

客户端计算机应装载缓存，而不是直接装载 Azure NetApp 文件卷。 按照[装载 Azure HPC 缓存](hpc-cache-mount.md)中的说明进行操作。

## <a name="next-steps"></a>后续步骤

* 详细了解如何设置和使用 [Azure NetApp 文件](../azure-netapp-files/index.yml)
* 若要帮助规划和设置 Azure HPC 缓存系统以使用 Azure NetApp 文件，请[联系支持人员](hpc-cache-support-ticket.md)。
