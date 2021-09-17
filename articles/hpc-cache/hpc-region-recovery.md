---
title: 使用 Azure HPC 缓存实现区域冗余和故障转移恢复
description: 使用 Azure HPC 缓存为灾难恢复提供故障转移功能的技术
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 08/19/2021
ms.author: v-erkel
ms.openlocfilehash: a05a281bdf01a01be842e99cc1b08383d0b9c1bc
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515014"
---
# <a name="use-multiple-caches-for-regional-failover-recovery"></a>使用多个缓存实现区域故障转移恢复

每个 Azure HPC 缓存实例在一个特定订阅和一个区域中运行。 这意味着，如果缓存区域出现全面故障，则缓存工作流可能会中断。

本文介绍了一种通过使用次要区域进行缓存故障转移来降低工作中断风险的策略。

关键是使用可以从多个区域访问的后端存储。 此存储可以是具有适当 DNS 支持的本地 NAS 系统，也可以是驻留在与缓存不同的区域中的 Azure Blob 存储。

当工作流在主要区域中继续运行时，数据保存在区域外的长期存储中。 如果缓存区域变得不可用，则可以在次要区域中创建重复的 Azure HPC 缓存实例，连接到相同的存储，然后从新缓存继续工作。

> [!NOTE]
> 此故障转移计划不包括存储帐户区域的完整中断。 此外，Azure HPC 缓存不支持地理冗余存储帐户（GRS 或 GZRS），因为在区域之间异步复制这些帐户对于 HPC 缓存工作流来说不够一致。
>
> HPC 缓存支持本地冗余存储 (LRS) 和区域冗余存储 (ZRS)，其中这些存储[可以在一个 Azure 区域中复制数据](../storage/common/storage-redundancy.md#redundancy-in-the-primary-region)。
>
> 如果需要预防全区域存储中断问题，请考虑手动备份策略。

## <a name="planning-for-regional-failover"></a>计划区域故障转移

若要创建缓存来为可能的故障转移做好准备，请按以下步骤操作：

1. 请确保后端存储在次要区域中是可访问的。
1. 在计划创建主缓存实例时，还应准备在次要区域中复制此安装过程。 包括以下项：

   1. 虚拟网络和子网结构
   1. 缓存容量
   1. 存储目标详细信息、名称和命名空间路径
   1. 客户端计算机（如果位于与缓存相同的区域）详细信息
   1. 由缓存客户端使用的装载命令

   > [注意] 可以编程方式（通过 [Azure 资源管理器模板](../azure-resource-manager/templates/overview.md)或直接访问其 API）创建 Azure HPC 缓存。 有关详细信息，请联系 Azure HPC 缓存团队。

## <a name="failover-example"></a>故障转移示例

例如，假设你要将 Azure HPC 缓存定位在 Azure 的美国东部区域。 它将访问存储在本地数据中心内的数据。

可以使用美国西部 2 区域中的缓存作为故障转移备份。

当在美国东部创建缓存时，请准备第二个缓存，以供部署在美国西部 2。 可以使用脚本或模板来自动执行此准备工作。

如果在美国东部发生区域范围内的故障，请在美国西部 2 区域中创建所准备的缓存。

创建缓存后，添加指向相同本地数据存储的存储目标，并使用与旧缓存的存储目标相同的聚合命名空间路径。

如果原始客户端受到影响，请在美国西部 2 区域中新建客户端，以便用于新缓存。

所有客户端都需要装载新缓存，即使客户端不受区域故障的影响，也不例外。 新缓存的装载地址与旧缓存不同。

## <a name="learn-more"></a>了解详细信息

Azure 应用程序体系结构指南中详细介绍了如何[从区域范围内的服务中断恢复](/azure/architecture/resiliency/recovery-loss-azure-region)。
