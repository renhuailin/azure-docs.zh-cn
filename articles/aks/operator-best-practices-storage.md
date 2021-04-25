---
title: 存储和备份的最佳做法
titleSuffix: Azure Kubernetes Service
description: 了解有关 Azure Kubernetes 服务 (AKS) 中的存储、数据加密和备份的群集操作员最佳做法
services: container-service
ms.topic: conceptual
ms.date: 03/10/2021
ms.openlocfilehash: 9b3ee6fd7eea958a573743b21bf8940458e2a965
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2021
ms.locfileid: "107104909"
---
# <a name="best-practices-for-storage-and-backups-in-azure-kubernetes-service-aks"></a>有关 Azure Kubernetes 服务 (AKS) 中的存储和备份的最佳做法

在 Azure Kubernetes 服务 (AKS) 中创建和管理群集时，应用程序通常需要存储空间。 请确保了解 Pod 性能需求和访问方法，以便为应用程序选择最佳存储。 AKS 节点大小可能会影响你的存储选择。 请规划附加存储的还原过程的备份和测试方式。

本最佳做法文章重点介绍群集操作员应考虑的存储注意事项。 本文内容：

> [!div class="checklist"]
> * 有哪些类型的存储可用。
> * 如何针对存储性能正确调整 AKS 节点的大小。
> * 卷的动态和静态预配之间的差异。
> * 备份和保护数据卷的方法。

## <a name="choose-the-appropriate-storage-type"></a>选择合适的存储类型

> **最佳实践指南**
> 
> 了解应用程序的需求以选择合适的存储。 将高性能、SSD 支持的存储用于生产工作负载。 在需要多个并发连接时，规划基于网络的存储。

应用程序通常需要不同类型和速度的存储。 询问以下问题，确定最适合的存储类型。 
* 应用程序是否需要连接到单个 Pod 的存储？
* 应用程序是否需要在多个 Pod 中共享的存储？ 
* 存储是否提供对数据的只读访问权限？
* 存储是否用于写入大量结构化数据？ 

下表概述了可用的存储类型及其功能：

| 使用案例 | 卷插件 | 读/写一次 | 只读多次 | 读/写多次 | Windows Server 容器支持 |
|----------|---------------|-----------------|----------------|-----------------|--------------------|
| 共享配置       | Azure 文件   | 是 | 是 | 是 | 是 |
| 结构化应用数据        | Azure 磁盘   | 是 | 否  | 否  | 是 |
| 非结构化的数据，文件系统操作 | [BlobFuse][blobfuse] | 是 | 是 | 是 | 否 |

AKS 为 Azure 磁盘存储或 Azure 文件存储支持的卷提供两种主要的安全存储。 这两种存储都使用默认的 Azure 存储服务加密 (SSE) 来加密静态数据。 无法在 AKS 节点级别使用 Azure 磁盘加密对磁盘进行加密。

Azure 文件存储和 Azure 磁盘均可在“标准”和“高级”性能层中使用：

- 高级磁盘
    - 由高性能固态硬盘 (SSD) 支持。 
    - 建议用于所有生产工作负载。
- 标准磁盘
    - 由常规旋转磁盘 (HDD) 支持。
    - 适用于存档或不常访问的数据。

了解应用程序性能需求和访问模式，选择合适的存储层。 有关托管磁盘大小和性能层的详细信息，请参阅 [Azure 托管磁盘概述][managed-disks]

### <a name="create-and-use-storage-classes-to-define-application-needs"></a>创建和使用存储类来定义应用程序需求

使用 Kubernetes 存储类定义所需的存储类型。 然后在 pod 或部署规范中引用存储类。 存储类定义共同发生作用，创建合适的存储并将其连接到 Pod。 

有关详细信息，请参阅 [AKS 中的存储类][aks-concepts-storage-classes]。

## <a name="size-the-nodes-for-storage-needs"></a>根据存储需求调整节点大小

> **最佳实践指南**
> 
> 每个节点大小都支持最大数量的磁盘。 不同大小的节点提供不同数量的本地存储和网络带宽。 根据应用程序需求进行适当规划，以部署大小适当的节点。

AKS 节点可以适应各种 Azure VM 类型和大小。 每个 VM 大小都可提供：
* 不同数量的核心资源，例如 CPU 和内存。 
* 可附加的最大数量的磁盘。 

不同大小的 VM 用于实现最大本地和附加磁盘 IOPS（每秒的输入/输出操作）的存储性能也会有所不同。

若应用程序需要 Azure 磁盘存储作为其存储解决方案，请制定节点 VM 大小合适的策略。 在决定 VM 大小时，存储功能以及 CPU 和内存量会发挥重要作用。 

例如，虽然 Standard_B2ms 和 Standard_DS2_v2 VM 大小都包含类似的 CPU 和内存资源量，但它们的潜在存储性能是不同的 ：

| 节点类型和大小 | vCPU | 内存 (GiB) | 最大数据磁盘数 | 最大未缓存磁盘 IOPS | 最大未缓存吞吐量 (MBps) |
|--------------------|------|--------------|----------------|------------------------|--------------------------------|
| Standard_B2ms      | 2    | 8            | 4              | 1,920                  | 22.5                           |
| Standard_DS2_v2    | 2    | 7            | 8              | 6,400                  | 96                             |

在此示例中，Standard_DS2_v2 提供了两倍的附加磁盘数量，以及三到四倍的 IOPS 和磁盘吞吐量。 若只比较核心计算资源和成本，则可以选择 Standard_B2ms VM 大小，但存储性能较差且存在较多限制。 

与应用程序开发团队沟通，了解他们的存储容量和性能需求。 为 AKS 节点选择合适的 VM 大小以满足或超出其性能需求。 定期调整应用程序的基线，以根据需要调整 VM 大小。

有关可用 VM 大小的详细信息，请参阅 [Azure 中的 Linux 虚拟机大小][vm-sizes]。

## <a name="dynamically-provision-volumes"></a>动态预配卷

> **最佳实践指南** 
>
> 为了减少管理开销并实现缩放，请避免静态创建和分配永久性卷。 使用动态预配。 在存储类中，定义合适的回收策略，以便在删除 pod 后最大限度地减少不必要的存储成本。

要将存储附加到 Pod，请使用永久性卷。 可以手动或动态创建永久性卷。 手动创建永久性卷会增加管理开销，并限制缩放能力。 相反，动态预配永久性卷可简化存储管理，并允许应用程序根据需要进行缩放。

![Azure Kubernetes 服务 (AKS) 群集中的永久性卷声明](media/concepts-storage/persistent-volume-claims.png)

通过永久性卷声明 (PVC)，可根据需要动态创建存储。 底层 Azure 磁盘在 Pod 发出请求时创建。 在 Pod 定义中，请求创建一个卷并将其附加到指定的装载路径。

有关如何动态创建和使用卷的概念，请参阅[永久性卷声明][aks-concepts-storage-pvcs]。

若要查看这些卷的运行情况，请参阅“如何使用 [Azure 磁盘][dynamic-disks]或 [Azure 文件存储][dynamic-files]动态创建和使用永久性卷”。

作为存储类定义的一部分，请设置相应的 reclaimPolicy。 此 reclaimPolicy 可控制底层 Azure 存储资源在 Pod 被删除时的行为。 基础存储资源可以删除，也可保留起来以后用于 Pod。 将 reclaimPolicy 设置为“保留”或“删除” 。 

了解应用程序需求，并定期检查保留的存储，以最大限度地减少未利用的付费存储量。

有关存储类选项的详细信息，请参阅[存储回收策略][reclaim-policy]。

## <a name="secure-and-back-up-your-data"></a>保护和备份数据

> **最佳实践指南** 
> 
> 使用适合自己存储类型的工具（例如 Velero 或 Azure 备份）来备份数据。 验证这些备份的完整性和安全性。

当应用程序存储和使用永久存储在磁盘或文件中的数据时，需要定期备份或创建数据的快照。 Azure 磁盘可以使用内置快照技术。 在执行快照操作之前，应用程序可能需要刷新磁盘写入操作。 [Velero][velero] 可以备份永久性卷以及其他群集资源和配置。 如果无法[从应用程序中删除状态][remove-state]，请从永久性卷备份数据并定期测试还原操作以验证数据完整性和所需的过程。

了解不同数据备份方法的局限性，以及是否需要在快照之前使数据处于静默状态。 数据备份不一定能恢复应用程序的群集部署环境。 有关这些方案的详细信息，请参阅 [AKS 中的业务连续性和灾难恢复的最佳做法][best-practices-multi-region]。

## <a name="next-steps"></a>后续步骤

本文重点介绍了 AKS 中的最佳存储做法。 有关 Kubernetes 中存储基础知识的详细信息，请参阅 [AKS 中应用程序的存储概念][aks-concepts-storage]。

<!-- LINKS - External -->
[velero]: https://github.com/heptio/velero
[blobfuse]: https://github.com/Azure/azure-storage-fuse

<!-- LINKS - Internal -->
[aks-concepts-storage]: concepts-storage.md
[vm-sizes]: ../virtual-machines/sizes.md
[dynamic-disks]: azure-disks-dynamic-pv.md
[dynamic-files]: azure-files-dynamic-pv.md
[reclaim-policy]: concepts-storage.md#storage-classes
[aks-concepts-storage-pvcs]: concepts-storage.md#persistent-volume-claims
[aks-concepts-storage-classes]: concepts-storage.md#storage-classes
[managed-disks]: ../virtual-machines/managed-disks-overview.md
[best-practices-multi-region]: operator-best-practices-multi-region.md
[remove-state]: operator-best-practices-multi-region.md#remove-service-state-from-inside-containers
