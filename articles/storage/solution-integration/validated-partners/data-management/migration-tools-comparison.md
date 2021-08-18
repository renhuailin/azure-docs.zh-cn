---
title: Azure 存储迁移工具比较 — 非结构化数据
description: 用于迁移非结构化数据的工具之间的基本功能和比较
author: dukicn
ms.author: nikoduki
ms.topic: conceptual
ms.date: 08/04/2021
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 63796f3e569849227dd4fa9c2396f3b052540586
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122177825"
---
# <a name="comparison-matrix"></a>比较矩阵

以下比较矩阵显示了不同工具的基本功能，这些工具可用于非结构化数据的迁移。 

## <a name="supported-azure-services"></a>支持的 Azure 服务

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **解决方案名称**  | [Azure 文件同步](../../../file-sync/file-sync-deployment-guide.md) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview)              | [数据移动和迁移](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [智能数据管理](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| Azure 文件支持（所有层级） | 是                          | 是                      | 是            | 是                            |
| Azure NetApp 文件支持      | 否                           | 是                      | 是            | 是                            |
| Azure Blob 热/冷支持   | 否                           | 是（通过 NFS 预览版）    | 是            | 是                            |
| Azure Blob 存档层支持 | 否                           | 否                       | 否             | 是（作为迁移目标） |
| Azure Data Lake Storage 支持 | 否                           | 否                       | 否             | 否                             |
| 支持的源      | Windows Server 2012 R2 及以上 | NAS 与云文件系统 | 任何 NAS 和 S3 | NAS、Blob、S3                  |

## <a name="supported-protocols-source--destination"></a>支持的协议（源/目标）

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **解决方案名称**   | [Azure 文件同步](../../../file-sync/file-sync-deployment-guide.md) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [数据移动和迁移](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [智能数据管理](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **SMB 2.1**       | 是 | 是 | 是 | 是 |
| **SMB 3.0**       | 是 | 是 | 是 | 是 |
| SMB 3.1       | 是 | 是 | 是 | 是 |
| NFS v3        | 否  | 是 | 是 | 是 |
| NFS v4.1      | 否  | 是 | 否  | 是 |
| Blob REST API | 否  | 否  | 是 | 是 |
| **S3**            | 否  | 是 | 是 | 是 |

## <a name="extended-features"></a>扩展功能

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **解决方案名称**  | [Azure 文件同步](../../../file-sync/file-sync-deployment-guide.md) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [数据移动和迁移](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [智能数据管理](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| UID/SID 重新映射                   | 否  | 是                        | 是 | 否                             |
| 协议 ACL 重新映射                | 否  | 否                         | 否  | 否                             |
| DFS 支持                           | 是 | 是                        | 是 | 是                            |
| **限制支持**                    | 是 | 是                        | 是 | 是                            |
| 文件模式排除               | 否  | 是                        | 是 | 是（使用复制功能） |
| 支持选择性文件属性 | 是 | 是                        | 是 | 是（对于拓展属性）  |
| 删除传播                   | 是 | 是                        | 是 | 是                            |
| 追随 NTFS 接合                 | 否  | 是                        | 否  | 是                            |
| 替代 SMB 所有者和组所有者    | 是 | 是                        | 是 | 否                             |
| 保管链报告            | 否  | 是                        | 否  | 是                            |
| 支持备用数据流    | 否  | 是                        | 是 | 否                             |
| 迁移计划              | 否  | 是                        | 是 | 是                            |
| 保留 ACL                        | 否  | 是                        | 是 | 是                            |
| DACL 支持                          | 是 | 是                        | 是 | 是                            |
| SACL 支持                          | 是 | 是                        | 是 | 否                             |
| 保留访问时间                | 是 | 是                        | 是 | 是                            |
| 保留修改时间              | 是 | 是                        | 是 | 是                            |
| 保留创建时间              | 否  | 是                        | 是 | 是                            |
| Azure Data Box 支持       | 是 | 是                        | 否  | 否                             |
| 迁移快照                | 否  | 手动                     | 是 | 否                             |
| 符号链接支持                 | 否  | 是                        | 否  | 是                            |
| 硬链接支持                     | 否  | 作为单独的文件迁移 | 是 | 是                            |
| 支持打开/锁定文件       | 是 | 是                        | 是 | 是                            |
| **增量迁移**                 | 是 | 是                        | 是 | 是                            |
| 切换支持                    | 否  | 是                        | 是 | 否（限手动）               |
| **[其他功能](#other-features)**         | [链接](#azure-file-sync)| [链接](#datadobi-dobimigrate)。 | [链接](#data-dynamics-data-mobility-and-migration)。 | [链接](#komprise-intelligent-data-management)                |

## <a name="assessment-and-reporting"></a>评估和报告

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **解决方案名称**   | [Azure 文件同步](../../../file-sync/file-sync-deployment-guide.md) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [数据移动和迁移](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [智能数据管理](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **容量**                        | 否      | 是 | 是 | 是            |
| 文件/文件夹编号            | 否      | 是 | 是 | 是            |
| 随时间推移的年龄分布      | 否      | 是 | 是 | 是            |
| 访问时间                     | 否      | 是 | 是 | 是            |
| 修改时间                   | 否      | 是 | 是 | 是            |
| 创建时间                   | 否      | 是 | 是 | 是            |
| 每个文件/对线报表状态 | 部分 | 是 | 是 | 是            |

## <a name="licensing"></a>许可

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **解决方案名称**   | [Azure 文件同步](../../../file-sync/file-sync-deployment-guide.md) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [数据移动和迁移](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [智能数据管理](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **BYOL**             | 不适用 | 是 | 是 | 是 |
| Azure 承诺 | 是   | 是 | 是 | 是 |

## <a name="other-features"></a>其他功能

### <a name="azure-file-sync"></a>Azure 文件同步

- 内部哈希验证

### <a name="datadobi-dobimigrate"></a>Datadobi DobiMigrate

- 迁移预检查
- 迁移规划
- 空运行以进行直接转换测试
- 在直接转换之前检测并提醒目标端用户活动
- 策略驱动的迁移
- 计划的复制迭代
- 用于处理根目录安全性的可配置选项
- 按需验证运行
- 源和目标上的数据读回验证
- 图形、交互式错误处理工作流
- 限制特定传播的操作，如删除和更新的功能
- 保留源访问时间（除了目标）的功能
- 在迁移切换过程中执行回滚到源的功能
- 迁移所选 SMB 文件属性的功能
- 清除 NTFS 安全描述符的功能
- 替代 NFSv3 权限，并将新的模式位写入目标的功能
- 将 NFSv3 POSIX 草稿 ACLS 转换为 NFSv4 ACLS 的功能
- SMB 1 (CIFS)
- 支持 24 x 7 x 365

### <a name="data-dynamics-data-mobility-and-migration"></a>数据 Dynamics 数据移动性和迁移

- 哈希验证

### <a name="komprise-intelligent-data-management"></a>Komprise 智能数据管理

- 基于项目/目录的迁移
- 自动重试失败
- 评估/报告：文件类型、文件大小、基于项目
- 评估/报表：基于元数据的自定义搜索
- 用于存档、复本、分析的完整数据生命周期管理解决方案
- 访问 Blob、S3 数据上基于时间的分析
- 标记
- 支持 24 x 7 x 365
- 哈希验证

列表的上次验证时间为 2021 年 3 月 31 日。

## <a name="see-also"></a>另请参阅

- [Azure 存储迁移概述](../../../common/storage-migration-overview.md)
- [选择 Azure 数据传输解决方案](../../../common/storage-choose-data-transfer-solution.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [迁移到 Azure 文件共享](../../../files/storage-files-migration-overview.md)
- [使用 WANdisco LiveData Platform for Azure 迁移到 Data Lake Storage](../../../blobs/migrate-gen2-wandisco-live-data-platform.md)
- [使用 AzCopy 将数据复制或移动到 Azure 存储](../../../common/storage-use-azcopy-v10.md)
- [使用 AzReplicate 将大型数据集迁移到 Azure Blob 存储（示例应用程序）](/samples/azure/azreplicate/azreplicate/)