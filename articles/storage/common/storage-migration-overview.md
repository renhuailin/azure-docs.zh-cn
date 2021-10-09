---
title: Azure 存储迁移指南
description: 存储迁移概述指南提供有关存储迁移的基本指导
author: dukicn
ms.author: nikoduki
ms.topic: conceptual
ms.date: 03/31/2021
ms.service: storage
ms.subservice: common
ms.openlocfilehash: 2d02399daf6a7ea56c41f936544697f9df91a62c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601266"
---
# <a name="azure-storage-migration-overview"></a>Azure 存储迁移概述

本文重点介绍如何向 Azure 进行存储迁移，并提供有关以下存储迁移方案的指导：

- 迁移非结构化数据，例如文件和对象
- 迁移基于块的设备，例如磁盘和存储区域网络 (SAN)

## <a name="migration-of-unstructured-data"></a>迁移非结构化数据

非结构化数据迁移包括以下方案：

- 将文件从网络附加存储 (NAS) 迁移到 Azure 文件产品/服务之一：
  - [Azure 文件](https://azure.microsoft.com/services/storage/files/)
  - [Azure NetApp 文件](https://azure.microsoft.com/services/netapp/)
  - [独立软件供应商 (ISV) 解决方案](../solution-integration/validated-partners/primary-secondary-storage/partner-overview.md)。
- 将对象从对象存储解决方案迁移到 Azure 对象存储平台：
  - [Azure Blob 存储](https://azure.microsoft.com/services/storage/blobs/)
  - Azure Data Lake Storage。

### <a name="migration-phases"></a>迁移阶段

整个迁移过程由多个不同的阶段组成：发现、评估和迁移。

| 发现 | 评估 | 迁移 |
| --------- | ---------- | --------- |
| - 发现要迁移的源 | - 评估适用的目标服务 <br> - 技术与成本注意事项 | - 初始迁移 <br> - 重新同步 <br> - 最终切换 |

#### <a name="discovery-phase"></a>发现阶段

在发现阶段确定需要迁移的所有源，例如 SMB 共享、NFS 导出或对象命名空间。 可以手动执行此阶段，也可以使用自动化工具。

#### <a name="assessment-phase"></a>评估阶段

评估阶段对于了解可用迁移选项至关重要。 为了降低迁移期间的风险并避免常见缺陷，请遵循以下三个步骤：

| 评估阶段的步骤                     | 选项                                                                          |
|--------------------------------------------|----------------------------------------------------------------------------------|
| 选择目标存储服务            | - Azure Blob 存储和 Data Lake Storage<br>- Azure 文件存储<br>- Azure NetApp 文件<br>- ISV 解决方案 |
| **选择迁移方法**                  | - 联机<br>- 脱机<br> - 上述两种方法的组合                                  |
| **为作业选择最佳迁移工具** | - 商用工具（Azure 和 ISV）<br> - 开源

有多个商用 (ISV) 工具可帮助完成评估阶段。 请参阅[比较矩阵](../solution-integration/validated-partners/data-management/migration-tools-comparison.md)。

##### <a name="choose-a-target-storage-service"></a>选择目标存储服务

选择哪个目标存储服务取决于访问数据的应用程序或用户。 正确的选择取决于技术和财务方面。 首先，执行技术评估以评估可行的目标并确定哪些服务满足要求。 接下来，执行财务评估以确定最佳选择。

若要帮助选择用于迁移的目标存储服务，请评估每个服务的以下方面：

- 协议支持
- 性能特征
- 目标存储服务的限制

下图是一个简化的决策树，可帮助你选择建议的 Azure 文件服务。 如果原生的 Azure 服务不满足要求，还有各种[独立软件供应商 (ISV) 解决方案](../solution-integration/validated-partners/primary-secondary-storage/partner-overview.md)可满足要求。

完成技术评估并选择正确的目标后，执行成本评估以确定最经济高效的选项。

![有关选择正确文件服务的基本决策树](./media/storage-migration-overview/files-decision-tree.png)

为使决策树保持简洁，示意图中未包含目标存储服务的限制。 若要详细了解当前限制，并确定是否需要基于这些限制修改你的选项，请参阅：

- [存储帐户限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)
- [Blob 存储限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-blob-storage-limits)
- [Azure 文件可伸缩性和性能目标](../files/storage-files-scale-targets.md)
- [Azure NetApp 文件资源限制](../../azure-netapp-files/azure-netapp-files-resource-limits.md)

如有任何限制对使用某个服务造成了阻碍，还可以选择多家存储供应商在 Azure 市场中提供的解决方案，这些解决方案均受 Azure 支持。 有关提供文件服务的经验证的 ISV 合作伙伴的信息，请参阅[主要和辅助存储的 Azure 存储合作伙伴](../solution-integration/validated-partners/primary-secondary-storage/partner-overview.md)。

##### <a name="select-the-migration-method"></a>选择迁移方法

可使用两种基本迁移方法进行存储迁移。

- **联机映像服务**。 联机方法使用网络进行数据迁移。 可以使用公共 Internet 或 [Azure ExpressRoute](../../expressroute/expressroute-introduction.md)。 如果服务没有公共终结点，则必须将 VPN 与公共 Internet 配合使用。
- **脱机**。 脱机方法使用 [Azure Data Box](https://azure.microsoft.com/services/databox/) 设备之一。

是要使用联机方法还是脱机方法取决于可用的网络带宽。 如果有足够的网络带宽可供在所需时间线内执行迁移，则联机方法是首选方法。

可以将这两种方法结合使用：脱机方法用于初始的批量迁移，联机方法用于对更改进行增量迁移。 同时使用这两种方法需具备较高的协调水平，因此不建议这样做。 如果选择使用这两种方法，请将联机迁移的数据集与脱机迁移的数据集隔离开来。

有关不同迁移方法的详细信息和指导，请参阅[选择用于数据传输的 Azure 解决方案](./storage-choose-data-transfer-solution.md)和[迁移到 Azure 文件共享](../files/storage-files-migration-overview.md)。

##### <a name="choose-the-best-migration-tool-for-the-job"></a>为作业选择最佳迁移工具

有多种迁移工具可用于执行迁移。 其中一些工具是开源的，例如 AzCopy、robocopy、xcopy 和 rsync；还有一些工具是商用的。 [比较矩阵](../solution-integration/validated-partners/data-management/migration-tools-comparison.md)中提供了可用商用工具列表以及这些工具的比较。

开源工具非常适合小规模迁移。 要从 Windows 文件服务器迁移到 Azure 文件存储，Microsoft 建议从 Azure 文件存储原生功能着手，并使用 [Azure 文件同步](/windows-server/manage/windows-admin-center/azure/azure-file-sync)。对于涉及不同的源、大容量或特殊要求（例如限制，或者带审核功能的详细报告）的较复杂迁移，商用工具是最佳选择。 这些工具可以简化迁移并大幅降低风险。 大多数商用工具还可以执行发现，这为评估提供了重要的输入。

#### <a name="migration-phase"></a>迁移阶段

迁移阶段是执行数据移动和迁移的最后一个迁移步骤。 通常，需要从头到尾运行迁移阶段多次才能实现更轻松的切换。 迁移阶段包括以下步骤：

1. **初始迁移。** 初始迁移步骤将源中的所有数据迁移到目标。 此步骤将会迁移需要迁移的大量数据。
2. **重新同步。** 重新同步操作将迁移在完成初始迁移步骤后已更改的所有数据。 如果存在大量的更改，可以重复此步骤多次。 多次运行重新同步操作的目的是减少最后一个步骤所需的时间。 对于非活动数据以及未更改的数据（例如备份或存档数据），可以跳过此步骤。
3. **最终切换**。 最终切换步骤将源中活跃使用的数据切换到目标，并停用源。

非结构化数据迁移的持续时间取决于多个方面。 除了所选的方法以外，最关键的因素是数据总大小和文件大小分布。 数据集总大小越大，迁移时间就越长。 平均文件大小越小，迁移时间就越长。 如果你有大量的小文件，请考虑将它们存档在较大的文件（例如 .tar 或 .zip 文件）中（如果适用），以减少总迁移时间。

## <a name="migration-of-block-based-devices"></a>迁移基于块的设备

基于块的设备迁移通常是在迁移虚拟机或物理主机的过程中完成的。 一个常见的错误认知是将块存储决策推迟到迁移之后。 在适当考虑工作负载要求的情况下提前做出这些决策可以更顺利地迁移到云中。

若要了解可迁移的工作负载和可采用的方法，请参阅 [Azure 磁盘存储文档](../../virtual-machines/disks-types.md)，以及[磁盘存储产品页](https://azure.microsoft.com/services/storage/disks/#resources)上的资源。 可以了解哪些磁盘符合你的要求，以及[磁盘突发](../../virtual-machines/disk-bursting.md)等最新功能。 可通过两种方式迁移基于块的设备：
- 若要将完整虚拟机连同基于块的基础设备一起迁移，请参阅 [Azure Migrate](../../migrate/index.yml) 文档
- 若要仅迁移基于块的设备以及更复杂的用例，请使用 [Cirrus Migrate Cloud](../solution-integration/validated-partners/data-management/cirrus-data-migration-guide.md)。

## <a name="see-also"></a>另请参阅

- [选择 Azure 数据传输解决方案](./storage-choose-data-transfer-solution.md)
- [商用迁移工具的比较](../solution-integration/validated-partners/data-management/migration-tools-comparison.md)
- [迁移到 Azure 文件共享](../files/storage-files-migration-overview.md)
- [使用 WANdisco LiveData Platform for Azure 迁移到 Data Lake Storage](../blobs/migrate-gen2-wandisco-live-data-platform.md)
- [使用 AzCopy 将数据复制或移动到 Azure 存储](./storage-use-azcopy-v10.md)
- [使用 AzReplicate 将大型数据集迁移到 Azure Blob 存储](/samples/azure/azreplicate/azreplicate/)
