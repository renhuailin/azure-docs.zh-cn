---
title: 比较对 Azure 文件存储、Blob 存储和 Azure NetApp 文件的 NFS 访问方式
description: 比较对 Azure 文件存储、Azure Blob 存储和 Azure NetApp 文件的 NFS 访问方式。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: rogarana
ms.openlocfilehash: 3dcc0a93473e5f0b14f8921114172dbf6506d8aa
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128636736"
---
# <a name="compare-access-to-azure-files-blob-storage-and-azure-netapp-files-with-nfs"></a>比较使用 NFS 对 Azure 文件存储、Blob 存储和 Azure NetApp 文件进行访问的方式

本文提供这些产品/服务之间的比较，供你在通过[网络文件系统 (NFS)](https://en.wikipedia.org/wiki/Network_File_System) 协议对其进行访问时参考。 如果你通过任何其他方法对其进行访问，则这种比较不适用。

有关更多一般性比较，请参阅[此文](storage-introduction.md)（其中比较了 Azure Blob 存储和 Azure 文件存储）或[此文](../files/storage-files-netapp-comparison.md)（其中比较了 Azure 文件存储和 Azure NetApp 文件）。

## <a name="comparison"></a>比较

|类别  |Azure Blob 存储  |Azure 文件  |Azure NetApp 文件  |
|---------|---------|---------|---------|
|用例     |Blob 存储最适合用于大规模的读取密集型高顺序访问工作负载，其中的数据只会引入一次并且进一步修改的次数极少。<br></br>如果维护工作量很少或者根本无需维护，则 Blob 存储可提供最低的总拥有成本。<br></br>部分示例方案包括：大规模分析数据、吞吐量敏感型高性能计算、备份和存档、自动驾驶、媒体渲染或基因组测序。         |Azure 文件存储是高度可用的服务，最适合用于随机访问工作负载。<br></br>对于 NFS 共享，Azure 文件存储提供全面的 POSIX 文件系统支持，除了基于 VM 的平台外，还可以从包含内置 CSI 驱动程序的容器平台（例如 Azure 容器实例 (ACI) 和 Azure Kubernetes 服务 (AKS)）轻松使用该服务。<br></br>部分示例方案包括：共享文件、数据库、主目录、传统应用程序、ERP、CMS、不需要高级管理的 NAS 迁移，以及需要横向扩展文件存储的自定义应用程序。         |在云中完全托管的文件服务，由 NetApp 提供支持，提供高级管理功能。<br></br>NetApp 文件适用于需要随机访问的工作负载，提供广泛的协议支持和数据保护功能。<br></br>部分示例方案包括：需要丰富管理功能的本地企业 NAS 迁移、延迟敏感型工作负载（例如 SAP HANA）、延迟敏感型或 IOPS 密集型高性能计算，或需要同时进行多协议访问的工作负载。         |
|可用的协议     |NFS 3.0<br></br>REST<br></br>Data Lake Storage Gen2         |SMB<br><br>NFS 4.1（预览版）<br></br> （协议之间不可互操作性）         |NFS 3.0 和 4.1<br></br>SMB         |
|主要功能     | 与 HPC 缓存集成，适用于低延迟工作负载。 <br> </br> 集成式管理，包括生命周期、不可变 Blob、数据故障转移和元数据索引。         | 区域冗余，可实现高可用性。 <br></br> 延迟始终低于 10 毫秒。 <br></br>可预测的性能和成本，可以通过添加/减少容量进行缩放。         |极低的延迟（亚毫秒级）<br></br>丰富的 NetApp ONTAP 管理功能，例如云中的 SnapMirror。<br></br>一致的混合云体验。         |
|性能（每个卷）     |IOPS 高达 20,000，吞吐量高达 100 GiB/秒。         |IOPS 高达 100,000，吞吐量高达 80 GiB/秒。         |IOPS 高达 460,000，吞吐量高达 36 GiB/秒。         |
|缩放     | 单个卷最大可扩展为 2 PiB。 <br></br> 单个文件最大可扩展为大约 4.75 TiB。<br></br>无最低容量要求。         |单个文件共享最大可扩展为 100 TiB。<br></br>单个文件最大可扩展为 4 TiB。<br></br>最小容量为 100 GiB。         |单个卷最大可扩展为 100 TiB。<br></br>单个文件最大可扩展为 16 TiB。<br></br>一致的混合云体验。         |
|定价     |[Azure Blob 存储定价](https://azure.microsoft.com/pricing/details/storage/blobs/)         |[Azure 文件存储定价](https://azure.microsoft.com/pricing/details/storage/files/)         |[Azure NetApp 文件定价](https://azure.microsoft.com/pricing/details/netapp/)         |

## <a name="next-steps"></a>后续步骤

- 若要使用 NFS 访问 Blob 存储，请参阅 [Azure Blob 存储中的网络文件系统 (NFS) 3.0 协议支持（预览版）](../blobs/network-file-system-protocol-support.md)。
- 若要使用 NFS 访问 Azure 文件存储，请参阅 [Azure 文件存储中的 NFS 文件共享](../files/files-nfs-protocol.md)。
- 若要使用 NFS 访问 Azure NetApp 文件，请参阅[快速入门：设置 Azure NetApp 文件并创建 NFS 卷](../../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md)。
