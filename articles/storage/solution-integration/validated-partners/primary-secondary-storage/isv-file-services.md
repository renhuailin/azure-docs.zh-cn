---
title: 在 Azure 中运行 ISV 文件服务时的注意事项
titleSuffix: Azure Storage
description: 有关在 Azure 中运行文件服务的不同 ISV 选项的基本指导
author: dukicn
ms.author: nikoduki
ms.topic: conceptual
ms.date: 05/24/2021
ms.service: storage
ms.subservice: common
ms.openlocfilehash: 8b49504fdda0b065b28b90f946dd203716933745
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114446129"
---
# <a name="running-isv-file-services-in-azure"></a>在 Azure 中运行 ISV 文件服务

Azure 提供各种用于存储文件数据的选项。 Azure 本机服务包含：
- [Azure 文件存储](https://azure.microsoft.com/services/storage/files/) – 完全托管于云的文件共享，可通过行业标准 SMB 和 NFS 协议进行访问。 Azure 文件提供具有不同性能特征的两种不同类型（标准和高级）。
- [Azure NetApp 文件](https://azure.microsoft.com/services/netapp/) – 完全托管于云的文件共享，旨在满足企业业务线应用程序的性能要求。 Azure NetApp 文件提供多个具有不同性能限制（标准、高级和超高）的服务级别。
- [Azure Blob 存储](https://azure.microsoft.com/services/storage/blobs/) – 用于存储非结构化数据的大规模对象存储平台。 Azure Blob 存储提供具有不同性能特征的两种不同类型（标准和高级）。 
  
有多篇文章可为你介绍有关本机文件服务选择的差异与建议。 你可以详细了解：
- 我们的迁移指南，其中介绍了[基本流程图](../../../common/storage-migration-overview.md#choose-a-target-storage-service)
- [Azure 文件存储与 Azure NetApp 文件之间的详细对比](../../../files/storage-files-netapp-comparison.md)

许多独立软件供应商 (ISV) 解决方案都可以在 Azure 中提供文件服务。 本文讨论两个主题：
- 提供有关文件服务选择的一般注意事项
- 概述 ISV 解决方案之间的差异。
  
[适用于主要和辅助存储的 Azure 存储合作伙伴](./partner-overview.md)提供经过验证的 ISV 解决方案完整列表。

## <a name="considerations"></a>注意事项

选择在 Azure 中运行文件服务的最佳解决方案时，一定要考虑几个注意事项。

### <a name="basic-considerations"></a>基本注意事项

基本功能注意事项适用于检查基础存储平台上最常见的功能。 执行这一步的最初目的在于探索哪些解决方案可提供满足我们要求所需的基本功能。 重要的基本功能因不同情况而异， 其中最常见的包括协议支持、命名空间大小、自动分层、加密、蠕虫支持以及身份验证。 你可以在[功能比较](#isv-solutions-comparison)中查看我们 ISV 解决方案支持的基本功能列表。

### <a name="performance-considerations"></a>性能注意事项

选择可满足基本功能需求的解决方案后，你必须考虑所需性能。 评估适用性能取决于三个基本性能特征：
  - 延迟
  - 带宽
  - IOPS 或 TPS

基本性能特征的重要性取决于工作负荷的并发性（并行请求数）以及块大小（请求大小）。

| 工作负荷类型 | 建议 |
| -------------------- | --------------- |
| **并发能力低下** | 延迟是最关键的考虑因素。 延迟越小，可实现的性能就越多。 在并发性较低的工作负荷中，IOPS 和带宽限制极少产生交叉 |
| **高并发性** | 由于并发性较高，因此延迟影响明显小得多。 IOPS 和/或带宽是必须考虑的因素。 |
| **块大小** | 如果工作负荷的块大小较小，则更要着重考虑 IOPS 限制；相反，工作负荷的块大小较大时，则更要着重考虑带宽限制。 |

任何存储工作负荷都可使用上述特征进行说明。 例如，OLTP 工作负荷通常具有较高并发性且块大小较小。 HPC 工作负荷通常具有较高并发性，但块大小可能大小不一。 
    
始终推荐使用的一些一般规则包括：
  - 协议选择：如有可能，使用支持多通道的 SMB3 或支持 nconnect 的 NFSv3，以便提供更佳性能。 在提供性能及考虑安全注意事项这两方面，请避免使用旧协议。 另请注意，你还可以对客户端进行优化处理，但我们建议你利用客户端最大限度提升性能。
  - 网络：若支持 VM 类型，使用加速网络。 其可减少延迟，并始终对性能产生积极影响。 
  - VM 类型：选择最适合工作负荷的 VM 类型。 如果工作负荷包含少量客户端，则在更少量较大的虚拟机中运行文件服务将更适合。 相反，若包含大量客户端，则在更多较小的虚拟机中运行文件服务将更受益。
  - 对于低并发性和块大小较小的工作负荷，可尝试具有以下特征的解决方案：
    - 使用托管磁盘（高级或超级 SSD 磁盘）或
    - 包含合适的缓存算法。
  - 对于高并发性和块大小较大的工作负荷，可尝试使用将 Azure Blob 存储用作后端的解决方案

## <a name="isv-solutions-overview-and-example-use-cases"></a>ISV 解决方案：概述和示例用例

本文将比较在 Azure 中提供文件服务的多种 ISV 解决方案。

| 解决方案 | 概述 | 示例用例： |
| -------- | ----------- | ----------------- |
| **Nasuni** | UniFS 是一项企业文件服务，提供构建于 Microsoft Azure 的云替代方案，更简单，而且成本低 | - 主文件存储 <br> - 部门文件共享 <br> - 集中式文件管理 <br> - 包含全局文件锁定功能的多站点协作 <br> - Windows 虚拟桌面 <br> - 远程办公/VDI 文件共享 |
| **NetApp** | Cloud Volumes ONTAP 可优化云存储成本和性能，同时增强数据保护、安全性及符合性。 其中包括企业级数据管理、可用性和持续性 | - 企业应用程序 <br> - 关系和 NoSQL 数据库 <br> - 大数据分析 <br> - 容器的持久性数据 <br> - CI/CD 管道 <br> - 本地 NetApp 解决方案的灾难恢复 |
| **Panzura**| CloudFS 是一个企业全局文件系统，具有增强的复原能力和高性能。 提供勒索软件防护。 | - 简化了遗留存储替换功能 <br> - 具有精细恢复功能的备份和灾难恢复 <br> - 云本机访问非结构化数据以进行分析，AI/ML。 <br> - 具有自动文件锁定和实时全局文件一致性的多站点文件协作 <br> - 使用云 VDI 的全局远程操作 <br> - 加速了遗留工作负载的云迁移 |
| **Tiger Technology** | Tiger Bridge 是一种数据管理软件解决方案。 提供 NTFS 文件系统和 Azure Blob 存储或 Azure 托管磁盘之间的分层服务。 创建可锁定本地文件的单个命名空间。 | - 云存档<br> - 持续数据保护 (CDP) <br> - 适用 Windows 服务器的灾难恢复 <br> - 多站点同步与协作 <br> - 远程工作流 (VDI)<br> - 对 Analytics、AI、ML 的云数据的本机访问 |
| XenData | 云文件网关使用 windows 文件服务器创建高度可扩展的全局文件系统 | - 工程和科学文件的全球共享 <br> - 协作式视频编辑 |

## <a name="isv-solutions-comparison"></a>ISV 解决方案比较

### <a name="supported-protocols"></a>支持的协议

|                                                     | Nasuni               | NetApp CVO                     | Panzura                   | Tiger Technology      | XenData               |
|-----------------------------------------------------|----------------------|--------------------------------|---------------------------|-----------------------|-----------------------|
| **SMB 2.1**                                         | 是                  | 是                            | 是                       | 是                   | 是                   |
| **SMB 3.0**                                         | 是                  | 是                            | 是                       | 是                   | 是                   |
| SMB 3.1                                         | 是                  | 是                            | 是                       | 是                   | 是                   |
| NFS v3                                          | 是                  | 是                            | 是                       | 是                   | 是                   |
| NFS v4.1                                        | 是                  | 是                            | 是                       | 是                   | 是                   |
| **iSCSI**                                           | 否                   | 是                            | 否                        | 是                   | 否                    |

### <a name="supported-services-for-persistent-storage"></a>永久存储支持的服务

|                                                     | Nasuni               | NetApp CVO                     | Panzura                   | Tiger Technology      | XenData               |
|-----------------------------------------------------|----------------------|--------------------------------|---------------------------|-----------------------|-----------------------|
| **托管磁盘**                                   | 否                   | 是                            | 是                       | 是                   | 否                    |
| **非托管磁盘**                                 | 否                   | 否                             | 否                        | 是                   | 否                    |
| Azure 存储块 blob                       | 是                  | 是（分层）                  | 是                       | 是                   | 是                   |
| Azure 存储页 Blob                        | 否                   | 是（适用于 HA）                   | 是                       | 否                    | 否                    |
| Azure 存档层支持                      | 否                   | 否                             | 是                       | 是                   | 是                   |
| 以非透明格式访问的文件           | 否                   | 否                             | 否                        | 是                   | 是                   |

### <a name="extended-features"></a>扩展功能

|                                                     | Nasuni               | NetApp CVO                     | Panzura                   | Tiger Technology      | XenData               |
|-----------------------------------------------------|----------------------|--------------------------------|---------------------------|-----------------------|-----------------------|
| 工作环境                           | UniFS                | ONTAP                          | PFOS                      | Windows Server        | Windows Server              |
| 高可用性                               | 是                  | 是                            | 是                       | 是（需要安装）  | 是                   |
| 群集节点之间的自动故障转移 | 是                  | 是                            | 是                       | 是（Windows 群集） | 是（windows 群集） |
| 跨可用性区域的自动故障转移    | 是                  | 否                             | 是                       | 是（Windows 群集） | 是（windows 群集） |
| 跨区域的自动故障转移               | 是（支持 Nasuni）| 否                         | 否                        | 是（Windows 群集） | 是（Windows 群集） |
| 支持快照                                | 是                  | 是                            | 是                       | 是                   | 否                    |
| 一致的快照支持                     | 是                  | 是                            | 是                       | 是                   | 否                    |
| 集成备份                               | 是                  | 是（外接程序）                   | 否                        | 是                   | 是                   |
| **版本控制**                                      | 是                  | 是                            | 否                        | 是                   | 是                   |
| 文件级还原                              | 是                  | 是                            | 是                       | 是                   | 是                   |
| 卷级还原                            | 是                  | 是                            | 是                       | 是                   | 是                   |
| WORM                                            | 是                  | 是                            | 否                        | 是                   | 否                    |
| 自动分层                               | 是                  | 是                            | 否                        | 是                   | 是                   |
| 全局文件锁定                             | 是                  | 是（NetApp 全局文件缓存） | 是                       | 是                   | 是                   |
| 跨后端源的命名空间聚合      | 是                  | 是                            | 否                        | 是                   | 是                   |
| 缓存活动数据                          | 是                  | 是                            | 是                       | 是                   | 是                   |
| 支持的缓存模式                         | LRU，手动固定 | LRU                            | LRU，手动固定      | LRU                   | LRU                   |
| **静态加密**                              | 是                  | 是                            | 是                       | 是                   | 否                    |
| 重复数据删除                                  | 是                  | 是                            | 是                       | 否                    | 否                    |
| **压缩**                                     | 是                  | 是                            | 是                       | 否                    | 否                    |

### <a name="authentication-sources"></a>身份验证源

|                                                     | Nasuni               | NetApp CVO                     | Panzura                   | Tiger Technology      | XenData               |
|-----------------------------------------------------|----------------------|--------------------------------|---------------------------|-----------------------|-----------------------|
| **Azure AD 支持**                                | 是（通过 ADDS）       | 是（通过 ADDS）                 | 是（通过 ADDS）            | 是（通过 ADDS）        | 是（通过 ADDS）        |
| Active Directory 支持                        | 是                  | 是                            | 是                       | 是                   | 是                   |
| LDAP 支持                                    | 是                  | 是                            | 否                        | 是                   | 是                   |

### <a name="management"></a>管理

|                                                     | Nasuni               | NetApp CVO                     | Panzura                   | Tiger Technology      | XenData               |
|-----------------------------------------------------|----------------------|--------------------------------|---------------------------|-----------------------|-----------------------|
| **REST API**                                        | 是                  | 是                            | 是                       | 是                   | 否                    |
| Web GUI                                         | 是                  | 是                            | 是                       | 否                    | 否                    |

### <a name="scalability"></a>可伸缩性

|                                                     | Nasuni               | NetApp CVO                     | Panzura                   | Tiger Technology      | XenData               |
|-----------------------------------------------------|----------------------|--------------------------------|---------------------------|-----------------------|-----------------------|
| 单个群集中的最大节点数     | 100                  | 2 (HA)                         | 最多测试 60 个节点    | 不适用                 | 不适用                 |
| **队列的最大数目**                       | 800                  | 1024                           | 无限制                 | 不适用                 | 1                     |
| 最大快照数                     | 无限制            | 无限制                      | 无限制                 | 不适用                 | 不适用                 |
| 单个命名空间大小上限              | 无限制            | 依赖于基础结构      | 无限制                 | 不适用                 | 不适用                 |

### <a name="licensing"></a>授权

|                                                     | Nasuni               | NetApp CVO                     | Panzura                   | Tiger Technology      | XenData               |
|-----------------------------------------------------|----------------------|--------------------------------|---------------------------|-----------------------|-----------------------|
| **BYOL**                                            | 是                  | 是                            | 是                       | 是                   | 是                   |
| 符合享受 Azure 权益的条件                          | 否                   | 是                            | 是                       | 否                    | 否                    |
| 部署模型（IaaS、SaaS）                   | IaaS                 | IaaS                           | IaaS                      | IaaS                  | IaaS                  |

### <a name="other-features"></a>其他功能

**Nasuni**
- [市场](https://azuremarketplace.microsoft.com/marketplace/apps/nasunicorporation.nasuni-nea-90-prod?tab=Overview)
- 可以使用托管磁盘缓存数据
- 集中管理所有 Edge 设备
- 文件版本控制历史记录无限制
- 文件系统大小无限制
- 文件大小无限制
- 通过 HTTPS、REST API 和 FTP 访问文件
- 使用 Azure 负载均衡器对多个文件的负载进行负载均衡
- 使用客户管理的密钥进行加密
- 支持第三方安全和审核工具（例如，Varonis、Stealthbits）
- 预配式部署和专业服务

**NetApp**
- [市场](https://azuremarketplace.microsoft.com/marketplace/apps/netapp.netapp-ontap-cloud?tab=Overview)
- 通过减少基础结构消耗，为客户节省重复数据删除成本

**Panzura**
- [市场](https://azuremarketplace.microsoft.com/marketplace/apps/panzura-file-system.panzura-freedom-filer)
- 字节级锁定（会同时打开多个读取/写入）

**Tiger Technology**
- 应用程序不可见
- 部分还原
- Windows Shell 集成（覆盖、上下文菜单、属性表）
- Azure 软删除和恢复删除支持
- 将重命名应用于云目标的选项
- 部分写入对象
- 勒索软件防护

XenData
- Cosmos DB 服务提供多网关快速同步，包括用于全局协作的特定于应用程序的所有者文件 
- 每个网关可对本地缓存的内容实施高度精细控制
- 支持视频流和部分文件还原
- 支持 Azure Data Box 上传
- Azure Blob 存储提供的加密

## <a name="next-steps"></a>后续步骤

了解详细信息：

- [Azure 磁盘](../../../../virtual-machines/managed-disks-overview.md)
- [Azure Blob 存储](https://azure.microsoft.com/services/storage/blobs/)
- [经认证的主存储和辅助存储合作伙伴](./partner-overview.md)
- [Azure 存储迁移概述](../../../common/storage-migration-overview.md)
